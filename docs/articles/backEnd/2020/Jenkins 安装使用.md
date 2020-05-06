---
title: Jenkins 安装使用
date: 2020-03-05
author: 范了饭饭
categories:
 - backEnd
tags: 
 - 部署
 - docker
---

使用 `Docker` 安装 `Jenkins` 

## 1. 获取下载 Jenkins 

`docker pull jenkins/jenkins:lts`


## 2. 设置端口映射和挂载
```
// 最后 -e xxx 用于解决时差问题
docker run -p 8080:8080 -p 50000:5000 --name jenkins -u root -v /mydata/jenkins_home:/var/jenkins_home -d jenkins/jenkins:lts -e JAVA_OPTS=-Duser.timezone=Asia/Shanghai
```

## 3. 修改文件内容, 因为启动时候会出现错误

Jinkins 启动时 Please wait while Jenkins is getting ready to work ...

修改文件 `hudson.model.UpdateCenter.xml` 中的 `url` 地址
```xml
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>http://mirror.xmission.com/jenkins/updates/update-center.json</url>
  </site>
</sites>
```
修改` updates/default.json` 文件, 换源, 替换文件
```shell
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
```

## 4. 打开网址

http://192.168.xx.xx:8080


## 5. 获取密码

`docker logs jenkins` 或者 找到这个文件夹 `/mydata/jenkins_home/secrets/initialAdminPassword`



## 6. Oracal 账号密码
```
U：541509124@qq.com
 
P：LR4ever.1314
```