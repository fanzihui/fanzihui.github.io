---
title: 使用 docker 部署 SpringBoot 网站
date: 2020-03-04
author: 范了饭饭
categories:
 - 后端
tags: 
 - 部署
 - docker
---

本文简单介绍下使用 `docker` 容器部署 `SpringBoot` 网站

## 1. 首先创建 springboot 网站

使用 IDEA 创建 `spirngboot` 网站, 比较简单, 本文只写 `controller` 部分

```java
@RestController
public class HomeController {

    @RequestMapping("index")
    public String index(){
        return "<h2>This is test</h2>";
    }
}

```

## 2. 使用 `Dockerfile` 部署网站(推荐)


1. 把 `springboot` 网站使用命令:  
 
``` 
  mvn package
  
```  
打包, 文件名为: docker-0.0.1-SNAPSHOT.jar

2. 创建 `Dockerfile` 文件

```docker
# 该镜像需要依赖的基础镜像
FROM java:8

# 在宿主机的 `/var/lib/docker` 目录下创建一个临时文件并把它链接到容器中的 `/tmp` 目录
```docker
VOLUME /tmp

# 将当前目录下的jar包复制到docker容器的/目录下
ADD docker-0.0.1-SNAPSHOT.jar /test-docker-file.jar

# 运行过程中创建一个mall-tiny-docker-file.jar文件
RUN bash -c 'touch /test-docker-file.jar'

# 声明服务运行在8080端口
EXPOSE 8080

# 指定docker容器启动时运行jar包
ENTRYPOINT ["java", "-jar","/test-docker-file.jar"]

# 指定维护者的名字
MAINTAINER fanfan

```

3. 创建 `web` 文件夹, 并把 `Dockerfile`  `docker-0.0.1-SNAPSHOT.jar` 放到 `web` 文件夹内。
   
4. 将 `web` 文件夹 上传到 `Linux` 服务器, 使用下面命令生成镜像
```docker
# -t 表示指定镜像仓库名称/镜像名称:镜像标签 .表示使用当前目录下的Dockerfile
# docker build -t mall-tiny/mall-tiny-docker-file:0.0.1-SNAPSHOT .

docker build -t test-docker .

```

5. 使用命令运行网站
```docker
# -d 表示后台运行
# -p映射端口
docker run -p 8080:8080 --name test-docker

```

## 3. 使用 `Maven` 部署网站
1. 在 `pom.xml` 文件添加依赖
```xml
<plugin>
 <groupId>com.spotify</groupId>
 <artifactId>docker-maven-plugin</artifactId>
 <version>1.1.0</version>
 <executions>
     <execution>
         <id>build-image</id>
         <phase>package</phase>
         <goals>
             <goal>build</goal>
         </goals>
     </execution>
 </executions>
 <configuration>
     <imageName>${project.artifactId}:${project.version}</imageName>
    <!-- 如果有docker是远程地址,可填写下面地址 -->
     <!-- <dockerHost>http://192.168.188.129:2375</dockerHost> -->
     <!-- 下面的目录下应该有 Dockerfile 文件 -->
     <dockerDirectory>src/main/docker</dockerDirectory>
     <baseImage>java:8</baseImage>
     <entryPoint>["java", "-jar","/${project.build.finalName}.jar"]
     </entryPoint>
     <resources>
         <resource>
             <targetPath>/</targetPath>
             <directory>${project.build.directory}</directory>
             <include>${project.build.finalName}.jar</include>
         </resource>
     </resources>
 </configuration>
</plugin>

```
2. 安装 `jdk` 

```
yum -y install java-1.8.0-openjdk*
```
配置环境变量 打开 `vim /etc/profile` 添加一下内容
```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64

export PATH=$PATH:$JAVA_HOME/bin

```

修改完成之后，使其生效

```
source /etc/profile
```

输入 `java -version` 返回版本信息则安装正常。

3. 安装 `MAVEN`
```

## 下载

wget https://mirrors.aliyun.com/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz

## 解压

tar vxf apache-maven-3.6.3-bin.tar.gz

## 移动

mv apache-maven-3.6.3 /usr/local/maven3


```

修改环境变量， 在 `/etc/profile` 中添加以下几行
```

MAVEN_HOME=/usr/local/maven3

export MAVEN_HOME

export PATH=${PATH}:${MAVEN_HOME}/bin

```

记得执行 `source /etc/profile`使环境变量生效。

输入 `mvn -version` 返回版本信息则安装正常。

进入 `/usr/local/maven3/conf` 找到 `settings.xml`

```
# 添加源
<mirror>  
    <id>nexus-aliyun</id>  
    <mirrorOf>central</mirrorOf>    
    <name>Nexus aliyun</name>  
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>  
</mirror>

```

4. 将项目打包并上传到服务器
```
# 打包

mvn package

# 启动

java -jar target/docker-0.0.1-SNAPSHOT.jar
```

5. 使用 `Dockerfile` 构建镜像
```
mvn package docker:build

```

6. 查看镜像并运行

```
# 查看镜像
docker images

# 运行镜像
docker run -p 8080:8080 --name test-docker

# 查看镜像
docker ps

```
