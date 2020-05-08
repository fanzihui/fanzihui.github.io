---
title: myBatis 模糊查询
date: 2020-05-07
author: 范了饭饭
categories:
 - 后端
tags: 
 - MyBatis
---

mybatis的xml中特殊转义字符和模糊查询like的写法

xml特殊符号转义写法

    &lt;          < 

    &gt;          >  

    &lt;&gt;   <>

    &amp;      & 

    &apos;      '

    &quot;      "

也可以使用<![CDATA[ ]]>符号进行说明，将此类符号不进行解析 

    <![CDATA[ 这里写你的sql ]]>  

mysql like的写法
```
  like concat('%',#{param},'%')  或者 like '%${param}%'

```
  推荐使用前者(前者需要使用 sqlserver 2012 版本及以上)，可以避免sql注入。