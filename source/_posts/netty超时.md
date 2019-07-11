---
title: netty超时
date: 2019-04-27 14:43:40
tags:
- netty
categories:
- netty
---

[参考](https://www.cnblogs.com/wxd0108/p/6516199.html)



## Netty 超时机制的介绍

Netty 的超时类型 [IdleState](http://netty.io/4.0/api/io/netty/handler/timeout/IdleState.html) 主要分为：

- ALL_IDLE : 一段时间内没有数据接收或者发送
- READER_IDLE ： 一段时间内没有数据接收
- WRITER_IDLE ： 一段时间内没有数据发送

在 Netty 的 [timeout](http://netty.io/4.0/api/io/netty/handler/timeout/package-frame.html) 包下，主要类有：

- IdleStateEvent ： 超时的事件

- IdleStateHandler ： 超时状态处理

- ReadTimeoutHandler ： 读超时状态处理

- WriteTimeoutHandler ： 写超时状态处理

  VSEchoServer.java 启动netty

  ```
  package netty;
  
  
  
  
  
  public class VSEchoServer {
  	private int port;
  	public VSEchoServer(int port){
  	  this.port = port;
  	}
  	
  	public void do_startServer(){
  	    //System.out.println("do_startServer");
  		new Thread(){
  		  @Override
  		  public void run(){
  			  //System.out.println("start..");
  			  VSEchoServerCls esc = new VSEchoServerCls();
  			  //System.out.println("start..2");
  			  try{
  			     esc.do_start(port);
  			     //System.out.println("start..3");
  			  }catch(Exception e){
  				  
  			  }
  			  //System.out.println("end..");
  		  }
  		}.start();
  	
  	}
  	
  	public static void main(String[] args) throws InterruptedException {
  		
  		new VSEchoServer(8001).do_startServer();
  		
  		
  	}
  
  }
  
  ```

  VSEchoServerCls.java 启动

  ```
  /*
   * Copyright 2012 The Netty Project
   *
   * The Netty Project licenses this file to you under the Apache License,
   * version 2.0 (the "License"); you may not use this file except in compliance
   * with the License. You may obtain a copy of the License at:
   *
   *   http://www.apache.org/licenses/LICENSE-2.0
   *
   * Unless required by applicable law or agreed to in writing, software
   * distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
   * WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
   * License for the specific language governing permissions and limitations
   * under the License.
   */
  package netty;
  
  
  
  import io.netty.bootstrap.ServerBootstrap;
  import io.netty.channel.ChannelFuture;
  import io.netty.channel.ChannelInitializer;
  import io.netty.channel.ChannelOption;
  import io.netty.channel.ChannelPipeline;
  import io.netty.channel.EventLoopGroup;
  import io.netty.channel.nio.NioEventLoopGroup;
  import io.netty.channel.socket.SocketChannel;
  import io.netty.channel.socket.nio.NioServerSocketChannel;
  
  import io.netty.handler.codec.LengthFieldBasedFrameDecoder;
  import io.netty.handler.codec.LengthFieldPrepender;
  import io.netty.handler.codec.string.StringDecoder;
  import io.netty.handler.codec.string.StringEncoder;
  import io.netty.handler.logging.LogLevel;
  import io.netty.handler.logging.LoggingHandler;
  import io.netty.handler.ssl.SslContext;
  import io.netty.handler.ssl.SslContextBuilder;
  import io.netty.handler.ssl.util.SelfSignedCertificate;
  import io.netty.util.CharsetUtil;
  
  
  
  /**
   * Echoes back any received data from a client.
   */
    class VSEchoServerCls {
  
      static final boolean SSL = System.getProperty("ssl") != null;
     
      public static boolean flag=true;
      public  void do_start(int port) throws Exception {
      	 System.out.println("do_start..1");
          // Configure SSL.
          final SslContext sslCtx;
          if (SSL) {
              SelfSignedCertificate ssc = new SelfSignedCertificate();
              sslCtx = SslContextBuilder.forServer(ssc.certificate(), ssc.privateKey()).build();
          } else {
              sslCtx = null;
          }
          //System.out.println("do_start..2");
          // Configure the server.
          EventLoopGroup bossGroup = new NioEventLoopGroup(1);
          EventLoopGroup workerGroup = new NioEventLoopGroup();
          try {
              ServerBootstrap b = new ServerBootstrap();
              b.group(bossGroup, workerGroup)
               .channel(NioServerSocketChannel.class)
               .option(ChannelOption.SO_BACKLOG, 100)
                .handler(new LoggingHandler(LogLevel.INFO)).childHandler(new VSServerInitializer());
  
              // Start the server.
              ChannelFuture f = b.bind(port).sync();
              
              // Wait until the server socket is closed.
           
              f.channel().closeFuture().sync();
              
          } catch (Exception e) {
  			e.printStackTrace();
  		}
          finally {
              // Shut down all event loops to terminate all threads.
          
              bossGroup.shutdownGracefully();
              workerGroup.shutdownGracefully();
          }
      }
     
  }
  
  ```

  

 VSEchoServerHandler.java

```
/*
 * Copyright 2012 The Netty Project
 *
 * The Netty Project licenses this file to you under the Apache License,
 * version 2.0 (the "License"); you may not use this file except in compliance
 * with the License. You may obtain a copy of the License at:
 *
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
 * WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
 * License for the specific language governing permissions and limitations
 * under the License.
 */
package netty;



import java.text.SimpleDateFormat;
import java.util.Date;

import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelFutureListener;
import io.netty.channel.ChannelHandler.Sharable;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import io.netty.handler.timeout.IdleState;
import io.netty.handler.timeout.IdleStateEvent;
import io.netty.util.CharsetUtil;

@Sharable
public class VSEchoServerHandler extends ChannelInboundHandlerAdapter {
	private final static SimpleDateFormat sdfTime = new SimpleDateFormat(
			"yyyy-MM-dd HH:mm:ss");
	// Return a unreleasable view on the given ByteBuf
		// which will just ignore release and retain calls.
		private static final ByteBuf HEARTBEAT_SEQUENCE = Unpooled
				.unreleasableBuffer(Unpooled.copiedBuffer("Heartbeat",
						CharsetUtil.UTF_8));  // 1

		@Override
		public void userEventTriggered(ChannelHandlerContext ctx, Object evt)
				throws Exception {
			sendmsg(ctx);	
			if (evt instanceof IdleStateEvent) {  // 2
				IdleStateEvent event = (IdleStateEvent) evt;  
				String type = "";
				if (event.state() == IdleState.READER_IDLE) {
					type = "read idle";
				} else if (event.state() == IdleState.WRITER_IDLE) {
					type = "write idle";
				} else if (event.state() == IdleState.ALL_IDLE) {
					type = "all idle";
				}

				ctx.writeAndFlush(HEARTBEAT_SEQUENCE.duplicate()).addListener(
						ChannelFutureListener.CLOSE_ON_FAILURE);  // 3
	 
				System.out.println( ctx.channel().remoteAddress()+"超时类型：" + type);
				
			} else {
				super.userEventTriggered(ctx, evt);
			}
		}
		//发送消息
		public void sendmsg(ChannelHandlerContext ctx){

			
						System.out.println("测试发送"+sdfTime.format(new Date()));
						try {
							 ctx.writeAndFlush("测试发送"+sdfTime.format(new Date()));
				
							 //pingMessage.release();
							 
						} catch (Exception e) {
							e.printStackTrace();
						}
						
					

		
		}
}

```

VSServerInitializer.java

```
package netty;

 
import java.util.concurrent.TimeUnit;

import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.socket.SocketChannel;
import io.netty.handler.codec.DelimiterBasedFrameDecoder;
import io.netty.handler.codec.LineBasedFrameDecoder;
import io.netty.handler.codec.http.HttpRequestDecoder;
import io.netty.handler.codec.http.HttpResponseEncoder;
import io.netty.handler.codec.string.StringDecoder;
import io.netty.handler.codec.string.StringEncoder;
import io.netty.handler.timeout.IdleStateHandler;

 

 

public class VSServerInitializer extends ChannelInitializer<SocketChannel> { 

    @Override
    protected void initChannel(SocketChannel channel) throws Exception {
        ChannelPipeline pipeline = channel.pipeline();
 
        System.out.println("initChannel:"); 

        
        pipeline.addLast("idleStateHandler", new IdleStateHandler(60,5,30, TimeUnit.SECONDS));//读超时 写超时 写超时
        pipeline.addLast("handler", new VSEchoServerHandler());

        
    }
    

 
}

```

模拟读超时可以用[telnet](https://jingyan.baidu.com/article/ae97a646b22fb6bbfd461d19.html)

