---
title: netty使用问题总结
date: 2020-05-13
author: FANLE
categories:
 - 后端
tags:
 - netty
 - SpringBoot
---

## 1. 解码器、编码器区分


常用的 字符串 编码 解码:

```java
@Component
public class NettyServerHandlerInitializer extends ChannelInitializer<Channel> {

    /**
     * 初始化通道
     * @param channel
     * @throws Exception
     */
    @Override
    protected void initChannel(Channel channel) throws Exception {
        ByteBuf delimiter = Unpooled.copiedBuffer("]".getBytes());
        channel.pipeline()
                // 空闲检测
                .addLast(new ServerIdleStateHandler())
                //添加自定义的 解码器
                .addLast("decoder", new StringDecoder(CharsetUtil.UTF_8))
                //添加编码器
                .addLast("encoder", new StringEncoder(CharsetUtil.UTF_8))
                //添加Netty 自带的 换行解码器（用来解决 沾包，拆包） 详细见 https://juejin.im/post/5b67902f6fb9a04fc67c1a24
//                .addLast(new LineBasedFrameDecoder(messageMaxLength,stripDelimiter,failFast))
                .addLast(new DelimiterBasedFrameDecoder(65535,delimiter))
                //添加 接收消息的 处理器
                .addLast(new NettyServerHandler());

    }
}

```

常用的 二级制 数据解码编码

```java

@Component
public class NettyServerHandlerInitializer extends ChannelInitializer<Channel> {

  /**
   * 初始化通道
   * @param channel
   * @throws Exception
   */
  @Override
  protected void initChannel(Channel channel) throws Exception {
      ByteBuf delimiter = Unpooled.copiedBuffer("]".getBytes());
      channel.pipeline()
        // 空闲检测
        .addLast(new ServerIdleStateHandler())
        //添加自定义的 解码器
        .addLast("decoder", new ByteArrayDecoder())
        //添加编码器
        .addLast("encoder", new ByteArrayEncoder())
        //添加Netty 自带的 换行解码器（用来解决 沾包，拆包） 详细见 https://juejin.im/post/5b67902f6fb9a04fc67c1a24
        .addLast(new DelimiterBasedFrameDecoder(65535,delimiter))
        //添加 接收消息的 处理器
        .addLast(new pumpNettyServerHandler());

  }
}

```
常用的 二级制 接收到数据解析

```java
/**
 * 客户端发消息会触发,业务逻辑处理
 */
@Override
public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
    super.channelRead(ctx, msg);
    final byte[] bytes = (byte[]) msg;
    String recStr = DataUtil.byteArrToHexString(bytes);
    int len = bytes.length;
}
```
