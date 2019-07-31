---
title: netty从零搭建一个网络通讯服务
date: 2019-07-28 14:58:42
tags:
- netty
categories:
- netty
---

以下所有都是从某大佬翻译的netty开发文档借鉴过来的https://waylau.gitbooks.io/netty-4-user-guide

## 写个抛弃服务器

```
import io.netty.bootstrap.ServerBootstrap;

import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelOption;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;

/**
 * 丢弃任何进入的数据
 */
public class DiscardServer {

    private int port;

    public DiscardServer(int port) {
        this.port = port;
    }

    public void run() throws Exception {
        EventLoopGroup bossGroup = new NioEventLoopGroup(); // (1)
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap(); // (2)
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class) // (3)
                    .childHandler(new ChannelInitializer<SocketChannel>() { // (4)
                        @Override
                        public void initChannel(SocketChannel ch) throws Exception {
                            ch.pipeline().addLast(new DiscardServerHandler());
                        }
                    })
                    .option(ChannelOption.SO_BACKLOG, 128)          // (5)
                    .childOption(ChannelOption.SO_KEEPALIVE, true); // (6)

            // 绑定端口，开始接收进来的连接
            ChannelFuture f = b.bind(port).sync(); // (7)

            // 等待服务器  socket 关闭 。
            // 在这个例子中，这不会发生，但你可以优雅地关闭你的服务器。
            f.channel().closeFuture().sync();
        } finally {
            workerGroup.shutdownGracefully();
            bossGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws Exception {
        int port;
        if (args.length > 0) {
            port = Integer.parseInt(args[0]);
        } else {
            port = 8080;
        }
        new DiscardServer(port).run();
    }
}

1.NioEventLoopGroup 是用来处理I/O操作的多线程事件循环器，Netty 提供了许多不同的 EventLoopGroup 的实现用来处理不同的传输。在这个例子中我们实现了一个服务端的应用，因此会有2个 NioEventLoopGroup 会被使用。第一个经常被叫做‘boss’，用来接收进来的连接。第二个经常被叫做‘worker’，用来处理已经被接收的连接，一旦‘boss’接收到连接，就会把连接信息注册到‘worker’上。如何知道多少个线程已经被使用，如何映射到已经创建的 Channel上都需要依赖于 EventLoopGroup 的实现，并且可以通过构造函数来配置他们的关系。
2.ServerBootstrap 是一个启动 NIO 服务的辅助启动类。你可以在这个服务中直接使用 Channel，但是这会是一个复杂的处理过程，在很多情况下你并不需要这样做。
3.这里我们指定使用 NioServerSocketChannel 类来举例说明一个新的 Channel 如何接收进来的连接。
4.这里的事件处理类经常会被用来处理一个最近的已经接收的 Channel。ChannelInitializer 是一个特殊的处理类，他的目的是帮助使用者配置一个新的 Channel。也许你想通过增加一些处理类比如DiscardServerHandler 来配置一个新的 Channel 或者其对应的ChannelPipeline 来实现你的网络程序。当你的程序变的复杂时，可能你会增加更多的处理类到 pipline 上，然后提取这些匿名类到最顶层的类上。
Sign in to comment

5.你可以设置这里指定的 Channel 实现的配置参数。我们正在写一个TCP/IP 的服务端，因此我们被允许设置 socket 的参数选项比如tcpNoDelay 和 keepAlive。请参考 ChannelOption 和详细的 ChannelConfig 实现的接口文档以此可以对ChannelOption 的有一个大概的认识。
6.你关注过 option() 和 childOption() 吗？option() 是提供给NioServerSocketChannel 用来接收进来的连接。childOption() 是提供给由父管道 ServerChannel 接收到的连接，在这个例子中也是 NioServerSocketChannel。

7.我们继续，剩下的就是绑定端口然后启动服务。这里我们在机器上绑定了机器所有网卡上的 8080 端口。当然现在你可以多次调用 bind() 方法(基于不同绑定地址)。
```

服务端DiscardServerHandler

```
import io.netty.buffer.ByteBuf;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;

import java.text.SimpleDateFormat;
import java.util.Date;

public class DiscardServerHandler extends ChannelInboundHandlerAdapter {
    // DiscardServerHandler 继承自 ChannelInboundHandlerAdapter，这个类实现了 ChannelInboundHandler接口，ChannelInboundHandler
    // 提供了许多事件处理的接口方法，然后你可以覆盖这些方法。现在仅仅只需要继承 ChannelInboundHandlerAdapter 类而不是你自己去实现接口方法。

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) {
        //这里我们覆盖了 chanelRead() 事件处理方法。每当从客户端收到新的数据时，这个方法会在收到消息时被调用，这个例子中，收到的消息的类型是 ByteBuf
        // 默默地丢弃收到的数据
        //为了测试的可见性打印一个时间
        String dateString=new SimpleDateFormat("YYYY-MM-dd HH-mm-ss").format(new Date());
        System.out.println(dateString+"接收了");
        ((ByteBuf) msg).release(); // (3)
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        // 当出现异常就关闭连接
        cause.printStackTrace();
        ctx.close();
    }
}
```

启动windows的telnet  localhost 8080

查看执行结果

![](https://s2.ax1x.com/2019/07/29/e8l5Wj.md.png)

------



## 查看客户端发送过来的数据

将DiscardServerHandler的channelRead()方法改写一下

```
@Override
public void channelRead(ChannelHandlerContext ctx, Object msg) {
      ByteBuf in = (ByteBuf) msg;
            try {
                while (in.isReadable()) { // (1)
                    String dateString=new SimpleDateFormat("YYYY-MM-dd HH:mm:ss").format(new Date());
                    System.out.println(dateString+"接收了");
                    System.out.print((char) in.readByte());
                    System.out.flush();
                }
            } finally {
                ReferenceCountUtil.release(msg); 
            }
}
```

将上面循环简化

```
      @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) {
            //这里我们覆盖了 chanelRead() 事件处理方法。每当从客户端收到新的数据时，这个方法会在收到消息时被调用，这个例子中，收到的消息的类型是 ByteBuf
            // 默默地丢弃收到的数据
            String dateString=new SimpleDateFormat("YYYY-MM-dd HH:mm:ss").format(new Date());
            System.out.println(dateString+"接收了");
            ByteBuf in = (ByteBuf) msg;
            System.out.println(in.toString(io.netty.util.CharsetUtil.US_ASCII));
            ((ByteBuf) msg).release(); 
        }
```

打开telnet ip port  使用ctrl +] 进入命令模式

send  test 发送字符串

![1564590360324](netty从零搭建一个网络通讯服务\1564590360324.png)

服务端打印

![1564590398058](netty从零搭建一个网络通讯服务\1564590398058.png)

## 写一个应答服务器

已知消息都是在DiscardServerHandler的channelRead()方法处理的应答也是再次 将方法改写 使用ChannelHandlerContext对象的方法进行发送

```
 @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) {
        ctx.write(msg); // (1)
        ctx.flush(); // (2)
    }
1. ChannelHandlerContext 对象提供了许多操作，使你能够触发各种各样的 I/O 事件和操作。这里我们调用了 write(Object) 方法来逐字地把接受到的消息写入。请注意不同于 DISCARD 的例子我们并没有释放接受到的消息，这是因为当写入的时候 Netty 已经帮我们释放了。
2.ctx.write(Object) 方法不会使消息写入到通道上，他被缓冲在了内部，你需要调用 ctx.flush() 方法来把缓冲区中数据强行输出。或者你可以用更简洁的 cxt.writeAndFlush(msg) 以达到同样的目的。
```

改写:

```
 //应答服务器
        public voidchannelRead(ChannelHandlerContext ctx, Object msg) {
            ByteBuf buf = (ByteBuf) msg;
            String receive = buf.toString(CharsetUtil.UTF_8);
            System.out.println("接受:"+receive);
            // 将消息包裹成ByteBuf并通过ctx的writeAndFlush方法回写。也可以先调用write方法再调用flush方法
            ctx.writeAndFlush(Unpooled.wrappedBuffer(("Received[" + receive + "]").getBytes()));

            /*ctx.writeAndFlush(msg);*/
        }
```

这个方法会将接受到的消息打印到控制台，并向客户端telnet回应

![1564591689010](netty从零搭建一个网络通讯服务\1564591689010.png)

![1564591701496](netty从零搭建一个网络通讯服务\1564591701496.png)

## 写一个时间服务器