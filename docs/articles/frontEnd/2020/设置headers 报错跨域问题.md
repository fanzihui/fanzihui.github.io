---
title: 设置headers 报错跨域问题
date: 2020-11-05
author: FANLE
categories:
 - 前端
tags:
 - Node
---

## 设置headers 报错Request header field Content-Type is not allowed by Access-Control-Allow-Headers


我要自定义一些参数放在headers里面，试了好多方法都不行

需要在后端设置:

```
// TODO 支持跨域访问  
response.setHeader("Access-Control-Allow-Origin", "*");  
response.setHeader("Access-Control-Allow-Credentials", "true");  
response.setHeader("Access-Control-Allow-Methods", "*");  
response.setHeader("Access-Control-Allow-Headers", "Content-Type,Access-Token");//这里“Access-Token”是我要传到后台的内容key  
response.setHeader("Access-Control-Expose-Headers", "*");  

if (request.getMethod().equals("OPTIONS")) {  
    HttpUtil.setResponse(response, HttpStatus.OK.value(), null);  
    return;  
}

```

跨域问题
报错信息为：Failed to load http://192.168.30.119: Request header field language is not allowed by Access-Control-Allow-Headers in preflight response.

原因：前台发送的请求体包含了没有允许的Header

如何解决问题：在请求体加入允许的Header类型

原有代码： response.setHeader(“Access-Control-Allow-Headers”, “x-requested-with,Authorization”);
修改后： response.setHeader(“Access-Control-Allow-Headers”, "x-requested-with,Authorization，language");
