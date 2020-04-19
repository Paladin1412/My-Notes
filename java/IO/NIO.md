# NIO

---

### IO / NIO / AIO

### IO 同步阻塞

同步就是发起调用后，被调用者未处理完请求之前，调用不返回。调用者一直等待请求结果返回，无法从事其他任务。

传统 IO 采用同步阻塞方式。尽管使用线程池可以实现伪异步（多个线程分别处理请求）。但高并发情况下大量线程阻塞、CPU频繁切换线程，仍会消耗大量资源。

*IO 模型中，每建立一个连接则创建一个线程，执行一个 while 死循环不断监测这条连接上是否有数据可以读，造成大量网络资源浪费。*

### NIO 同步非阻塞

同步就是发起调用后，被调用者未处理完请求之前，调用不返回。我们可以先处理其他请求，定时检查结果是否返回。

NIO 采用同步非阻塞方式，是目前开发的主流 IO 方式。

*NIO 模型中，每建立一个连接则注册到 selector 上由一个线程负责监听，执行一个 while 死循环同时监测所有连接是否有数据可以读，节约大量网络资源。*

### AIO 异步非阻塞

异步就是发起一个调用后，得到被调用者的回应表示已接收到请求，我们可以先处理其他请求，被调用者会主动通知调用者其返回结果（通常依靠事件，回调等机制）。

AIO 采用异步非阻塞方式，但其使用相对较少。

---

## NIO 的使用

使用 java NIO 中的类，需要进行导入： `import java.nio.*; `  

java.nio 包，提供了 Channel , Selector，Buffer 等抽象。支持面向缓冲的，基于通道的 I/O 操作方法。

但 NIO 编程复杂自行实现 bug 极多，主流使用 Netty 开源框架。


### Netty 开源框架

对NIO封装得如此完美，写出来的代码非常优雅，另外一方面，使用Netty之后，网络通信这块的性能问题几乎不用操心，尽情地让Netty榨干你的CPU吧。