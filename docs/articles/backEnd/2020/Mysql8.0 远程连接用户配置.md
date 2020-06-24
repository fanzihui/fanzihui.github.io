---
title: Mysql8.0 远程连接用户配置
date: 2020-06-24
author: FANLE
categories:
 - 后端
tags:
 - 数据库
---


Mysql8.0 远程连接用户配置

## 方法一 已存在用户授权

```sql
mysql> use mysql;
```
### 确保下面语句执行为单一结果,结果多条增加筛选条件

```sql
mysql> select host,user from user where user='root';
mysql> update user set host='%' where user='root'; 
mysql> flush privileges

```

## 方法二 新建用户并授权

```sql
mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';

```
### 语法模版 GRANT ALL PRIVILEGES ON 库名.表名 TO '用户名'@'IP地址' WITH GRANT OPTION

```sql

mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;

```