---
title: MongoDB 安装教程
date: 2020-03-27
author: 范了饭饭
categories:
 - backEnd
tags: 
 - 数据库
 - MongoDB
---

MongoDB 配置教程

1. 下载 MongoDB
下载Mongodb安装包，下载地址：https://fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.2.21-signed.msi

2. 在安装路径下创建data\db和data\log两个文件夹

3. 在安装路径下创建mongod.cfg配置文件
```conf
systemLog:
 destination: file
 path: D:\developer\MongoDB\data\log\mongod.log
storage:
 dbPath: D:\developer\MongoDB\data\db
```
4. 使用命令启动
```shell
d: && cd /soft/mongodb/bin
回车，再执行：
mongod --dbpath d:/soft/mongodb/data
```

5. 设置系统服务
```shell
mongod.exe --logpath d:/developer/MongoDB/data/log/mongod.log --logappend --dbpath d:/developer/MongoDB/data/db --directoryperdb --serviceName MongoDB -install --auth

```

6. 启动和关闭 MongoDB 服务
```
启动服务：net start MongoDB
关闭服务：net stop MongoDB
移除服务：d:\developer\MongoDB\bin\mongod.exe --remove

```

7. 错误情况

```shell
D:\developer\MongoDB\bin>net start MongoDB
发生系统错误 5。
```

发生上面的错误是由于没有使用 管理员权限启动cmd

