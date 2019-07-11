---
title: netty
date: 2019-04-07 16:43:26
tags:
- netty
categories:
- netty
---

[参考](https://www.jianshu.com/p/a4e03835921a)

[学习教程](https://www.w3cschool.cn/essential_netty_in_action/)

[先了解NIO是什么](https://zhbzdnb.github.io/zhbzdnb.github.io/2019/02/17/NIO/)

#### 百度百科上netty的介绍

------

Netty是由[JBOSS](https://baike.baidu.com/item/JBOSS)提供的一个[java开源](https://baike.baidu.com/item/java%E5%BC%80%E6%BA%90/10795649)框架。Netty提供异步的、[事件驱动](https://baike.baidu.com/item/%E4%BA%8B%E4%BB%B6%E9%A9%B1%E5%8A%A8/9597519)的网络应用程序框架和工具，用以快速开发高性能、高可靠性的[网络服务器](https://baike.baidu.com/item/%E7%BD%91%E7%BB%9C%E6%9C%8D%E5%8A%A1%E5%99%A8/99096)和客户端程序。

也就是说，Netty 是一个**基于NIO的客户、服务器端编程框架**，使用Netty 可以确保你快速和简单的开发出一个网络应用，例如实现了某种协议的客户、[服务端](https://baike.baidu.com/item/%E6%9C%8D%E5%8A%A1%E7%AB%AF/6492316)应用。Netty相当于简化和流线化了网络应用的编程开发过程，例如：**基于TCP和UDP的socket服务开发**。

“快速”和“简单”并不用产生维护性或性能上的问题。Netty 是一个吸收了多种协议（包括FTP、SMTP、HTTP等各种二进制文本协议）的实现经验，并经过相当精心设计的项目。最终，Netty 成功的找到了一种方式，在保证易于开发的同时还保证了其应用的性能，稳定性和伸缩性。 [1] 

#### Netty的组件

------

```
 	Bootstrap or ServerBootstrap
    EventLoop
    EventLoopGroup
    ChannelPipeline
    Channel
    Future or ChannelFuture
    ChannelInitializer
    ChannelHandler

     Bootstrap，一个Netty应用通常由一个Bootstrap开始，它主要作用是配置整个Netty程序，串联起各个组件。

     Handler，为了支持各种协议和处理数据的方式，便诞生了Handler组件。Handler主要用来处理各种事件，这里的事件很广泛，比如可以是连接、数据接收、异常、数据转换等。

     ChannelInboundHandler，一个最常用的Handler。这个Handler的作用就是处理接收到数据时的事件，也就是说，我们的业务逻辑一般就是写在这个Handler里面的，ChannelInboundHandler就是用来处理我们的核心业务逻辑。

     ChannelInitializer，当一个链接建立时，我们需要知道怎么来接收或者发送数据，当然，我们有各种各样的Handler实现来处理它，那么ChannelInitializer便是用来配置这些Handler，它会提供一个ChannelPipeline，并把Handler加入到ChannelPipeline。

     ChannelPipeline，一个Netty应用基于ChannelPipeline机制，这种机制需要依赖于EventLoop和EventLoopGroup，因为它们三个都和事件或者事件处理相关。

     EventLoops的目的是为Channel处理IO操作，一个EventLoop可以为多个Channel服务。

     EventLoopGroup会包含多个EventLoop。

     Channel代表了一个Socket链接，或者其它和IO操作相关的组件，它和EventLoop一起用来参与IO处理。

     Future，在Netty中所有的IO操作都是异步的，因此，你不能立刻得知消息是否被正确处理，但是我们可以过一会等它执行完成或者直接注册一个监听，具体的实现就是通过Future和ChannelFutures,他们可以注册一个监听，当操作执行成功或失败时监听会自动触发。总之，所有的操作都会返回一个ChannelFuture。
```

回到我正在弄得项目，查看与客户端(即安卓端)进行交互的主要代码中向客户端发送消息的主要代码

	//发送消息
	public synchronized void sendmsg(ChannelHandlerContext ctx,String msg){
		
		//System.out.println("发送:"+msg);
		try {
			 if (new VVersion().pwdPloy.equals("1")){
			       msg=DesUtil.encrypt(msg,DesUtil.keyStatic);
		     }
			
			 byte[] req;
			 if(checkMsgFlag){
				  req =	getSendMsgNew(msg,versionCode);
			 }else{
				  req = msg.getBytes("UTF-8");
			 }
			 //System.out.println(new String(req));
			 ByteBuf pingMessage = Unpooled.buffer();
			 
			 //ByteBuf pingMessage=PooledByteBufAllocator.DEFAULT.directBuffer(1024);
			 
			 pingMessage.writeBytes(req);
			 
			 ctx.writeAndFlush(pingMessage);
			 
			 //pingMessage.release();
			 
		} catch (Exception e) {
			e.printStackTrace();
		}
		 
	}

其中主要是使用netty的接口ChannelHandlerContext用此接口的writeAndFlush()进行消息发送

[此接口详解](https://www.w3cschool.cn/essential_netty_in_action/essential_netty_in_action-bj5a28bq.html)

