---
title: netty 粘包拆包问题
date: 2020-05-08
author: FANLE
categories:
 - 后端
tags:
 - SpringBoot
 - netty
---

今天遇到了TCP发送太快 Netty 出现了粘包和拆包得问题

解决这个问题可以使用 netty 自带的 3 种方法

处理拆/粘包的方式有以下几种。

1、定长，对于不满规定长度的帧用规定的特殊字符填满

2、在每一帧的结尾加上特殊字符，作为每一帧的结尾

3、将每一帧分为帧头和帧尾。帧头中包含帧的长度信息。

上面三种方式是比较通用的解决拆/粘包问题的方式。

对于上面

第一种方式要考虑如果帧长超过规定长度怎么办。

第二种就要考虑如果帧中出现了规定做为帧尾的特殊字符怎么办。

第三种会出现跟第一种方式一样的问题，例如我们规定帧头为2个字节，那么两个字节一次可以表示65535个字节，如果帧长超过这个数字怎么办。

在netty中，上面三种方式处理方式都是有实现的。


第一种：FixedLengthFrameDecoder

第二种：LineBaseFrameDecoder或者DelimiterBaseDecoder

第三种：LengthFieldFrameDecoder



FixdLengthFrameDecoder  自动完成对定长消息的解码

```
 //5 表示5个长度; 如：客户端输入“aaaaaa” ---> 服务端 只会接收到 “aaaaa” ; 所以数据实在不够的时候 以空位补空格。

ch.pipeline().addLast(new FixedLengthFrameDecoder(5)); 

```


LineBasedFrameDecoder 主要代码演示:

```
  ch.pipeline().addLast(new LineBasedFrameDecoder(1024)); 
  ch.pipeline().addLast(new StringDecoder());

```

DelimiterBaseFrameDecoder 自定义的分隔符

```
  ByteBuf delimiter = Unpooled.copiedBuffer("$_".getBytes());
  ch.pipeline().addLast(new    DelimiterBasedFrameDecoder(1024,delimiter));
  ch.pipeline().addLast(new StringDecoder());
  ch.pipeline().addLast(new EchoClientHandler());

```

