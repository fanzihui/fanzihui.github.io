---
title: linux docker 使用总结
date: 2020-03-04
author: 范了饭饭
categories:
 - backEnd
tags: 
 - 部署
 - docker
---

`linux` 下 `docker` 的安装与使用

## 1. 基本安装使用
安装 `yum-utils`：
```
yum install -y yum-utils device-mapper-persistent-data lvm2
```

为 `yum` 源添加 `docker` 仓库位置：
```
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

```
安装 `docker`：
```
yum -y install docker-ce

```
启动 `docker`：
```
systemctl start docker
```


切换数据源
```json
// Docker 使用 /etc/docker/daemon.json（Linux）

{
  "registry-mirrors": ["https://f4my2xxu.mirror.aliyuncs.com"]
}
```
查看源是否切换:
```

docker info|grep Mirrors -A 1
```

重启 `docker`:
```
systemctl restart docker
```

安装 `docker-compose`:
```
curl -L https://get.daocloud.io/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```
修改权限:
```
chmod +x /usr/local/bin/docker-compose
```
查看是否安装成功
```
docker-compose --version
```

配置 `docker` 远程连接端口
```shell
 vi /usr/lib/systemd/system/docker.service
```

 找到 `ExecStart` ，在最后面添加 `-H tcp://0.0.0.0:2375`

重新加载配置文件
```shell
 systemctl daemon-reload
```
 
重启 `docker`
```shell
 systemctl start docker
```


## 2. 其他内容备忘

参考文件

数据库脚本 `mall.sql` ：https://github.com/macrozheng/mall/blob/master/document/sql/mall.sql

`nginx` 配置文件 `nginx.conf` ：https://github.com/macrozheng/mall/blob/master/document/docker/nginx.conf

`docker-compose-env.yml` ：https://github.com/macrozheng/mall/tree/master/document/docker/docker-compose-env.yml

`docker-compose-app.yml` ：https://github.com/macrozheng/mall/tree/master/document/docker/docker-compose-app.yml

使用 `Docker Compose` 部署应用 : https://mp.weixin.qq.com/s/iMl9bJ4SxUsNHBbiS5VUcw

开发者必备Docker命令: https://mp.weixin.qq.com/s/d_CuljDTJq680NTndAay8g
#### elasticsearch

```shell
# 改变设置
sysctl -w vm.max_map_count=262144
# 使之立即生效
sysctl -p
```
需要创建 `/mydata/elasticsearch/data` 目录并设置权限，否则会因为无权限访问而启动失败
```shell
# 创建目录
mkdir /mydata/elasticsearch/data/
# 创建并改变该目录权限
chmod 777 /mydata/elasticsearch/data
```

需要安装中文分词器 `IKAnalyzer` ，并重新启动(经常失败,网络不好 /(ㄒoㄒ)/~~)

```shell
docker exec -it elasticsearch /bin/bash

# 此命令需要在容器中运行
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.4.0/elasticsearch-analysis-ik-6.4.0.zip
docker restart elasticsearch

```

#### nginx

需要拷贝 `nginx` 配置文件，否则挂载时会因为没有配置文件而启动失败。
```
# 创建目录之后将nginx.conf文件上传到该目录下面
mkdir /mydata/nginx/

```

#### 下载 `docker-compose-env.yml` 配置好的插件:
```
docker-compose -f docker-compose-env.yml up -d

```

#### mysql


将 `mall.sql` 文件拷贝到 `mysql` 容器的/目录下：

```
docker cp /mydata/mall.sql mysql:/

```

进入 `mysql` 容器并执行如下操作：

```shell
# 进入mysql容器
docker exec -it mysql /bin/bash

# 连接到mysql服务
mysql -uroot -proot --default-character-set=utf8

# 创建远程访问用户
grant all privileges on *.* to 'reader' @'%' identified by '123456';

# 创建mall数据库
create database mall character set utf8;

# 使用mall数据库
use mall;

# 导入mall.sql脚本
source /mall.sql;

```

