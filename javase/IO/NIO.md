# NIO

---

## IO 模型

### BIO 同步阻塞

> 在服务器和客户端通信的过程中，服务器线程会一直等待请求结果返回，无法处理其他请求。

java.io 文件夹内提供了 Java 程序中 BIO 操作使用的类，使用时需要进行导入。   

BIO 模型中，通常使用线程池机制实现伪异步：每建立一个连接就创建一个线程，在执行读写操作时该线程被阻塞，直到数据流读写完成。

在高并发情况下会有大量线程阻塞、CPU 将频繁切换线程，大量消耗计算机资源。

### NIO 同步非阻塞

> 在服务器和客户端通信的过程中，服务器线程可以先处理其他请求，定时检查结果是否返回。

JDK 1.4 引入，java.nio 文件夹内提供了 Java 程序中 NIO 操作使用的类，使用时需要进行导入。  

NIO 模型中，在执行读写操作时数据会先存入缓冲区，该线程可以先处理其他连接，一定时间后再对缓冲区读取或写出。

- **Buffer**：【缓冲区】暂存将要写入或者要读出的数据。

- **Channel**：【全双工通道】对缓冲区数据读写，在通道内部支持同时读写。

- **Selector**：【选择器】用于单线程同时管理多个通道，selector 会对多个客户进行轮询，使一个线程可以同时处理多个请求。

### AIO 异步非阻塞

> 在服务器和客户端通信的过程中，服务器线程可以先处理其他请求，客户端会主动通知服务器返回了结果。

JDK 1.7 引入，java.aio 文件夹内提供了 Java 程序中 AIO 操作使用的类，使用时需要进行导入。  

在 Linux 底层 AIO 实现的本质仍为轮询，所以 AIO 相比于 NIO 的性能提升非常有限。

---

## Netty


### Netty 框架

但 NIO 编程复杂自行实现 bug 极多，目前主流的 NIO 通信使用 Netty 开源框架。


```java
public class NettyOioServer {

    public void server(int port) throws Exception {
        final ByteBuf buf = Unpooled.unreleasableBuffer(
                Unpooled.copiedBuffer("Hi!\r\n", Charset.forName("UTF-8")));
        EventLoopGroup group = new OioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap();        // 负责连接的池

            b.group(group)                                    //2
             .channel(OioServerSocketChannel.class)
             .localAddress(new InetSocketAddress(port))
             .childHandler(new ChannelInitializer<SocketChannel>() {    // 初始化
                 @Override
                 public void initChannel(SocketChannel ch) 
                     throws Exception {
                     ch.pipeline().addLast(new ChannelInboundHandlerAdapter() {            //4
                         @Override
                         public void channelActive(ChannelHandlerContext ctx) throws Exception {
                             ctx.writeAndFlush(buf.duplicate()).addListener(ChannelFutureListener.CLOSE);//5
                         }
                     });
                 }
             });
            ChannelFuture f = b.bind().sync();  //6
            f.channel().closeFuture().sync();
        } finally {
            group.shutdownGracefully().sync();        //7
        }
    }
}


```