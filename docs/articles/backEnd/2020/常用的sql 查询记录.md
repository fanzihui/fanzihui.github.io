---
title: 常用的sql 查询记录
date: 2020-06-10
author: FANLE
categories:
 - 数据库
tags:
 - SQL
---

这里记录下常用的 sql 查询语句, 方便必要时使用


增加

```sql

INSERT INTO 表名称 VALUES (值1, 值2,....)

INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)

```


删除

```sql

delete from t1 where xxx = 'xxx'

```


修改

```sql

update 数据表 set 字段 bai名=字段值 where 条件表 du达式


```


查找

```sql

select * from t1;


-- 条件查询
select * from t1 where xxxx='xxx';



-- 模糊查询
select * from Pump p 

LEFT JOIN Users u

on p.SysHospital_ID = u.SysHospital_ID 

where 

	u.SysHospital_ID = '666666'

	and
	
	u.SysProduct_ID like '%3%'
	
	and 
	
	p.SysProduct_ID = '3'
	
	and
	
	u.UserCode = 'floor1'

```


清空表

```sql

truncate table [表名]

```



