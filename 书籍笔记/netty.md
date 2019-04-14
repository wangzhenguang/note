# Netty



###  netty的组件

- channel

  channel是java nio的一个基本构造。它代表一个到实体的开放连接，如读操作和写操作。

- 回调

- future

  - future提供了另一种操作完成时通知应用程序的方式。这个对象可以看做是一个异步操作的结果占位符，它将在未来的某个时刻完成，并提供对其结果的访问。

- 事件和ChannelHandler

  Netty使用不同的事件来通知我们状态的改变或者操作的状态。这使得我们能够基于已经发生的时间来触发适当的动作。

  - 记录日志
  - 数据转换
  - 流控制
  - 应用程序逻辑
  - 连接已经被激活或者连接失活。
  - 数据读取
  - 用户事件
  - 打开或者关闭到远程节点的连接
  - 将数据写到或者冲刷到套接字。



### netty网络抽象代表

- channel -- socket
- EventLoop — 控制流、多线程处理、并发
- ChannelFuture -- 异步通知

channel接口：
	基本的i/o操作（bind 、connect、read、write）依赖于底层网络传输所提供的原语。

EventLoop接口：
	用于处理连接的声明周期中所发生的时间。

Channel 、EventLoop、Thread、EventLoopGroup之间的关系:

- 一个EventLoopGroup包含一个或者多个EventLoop；
- 一个EventLoop在它的声明周期内只和一个Thread绑定；
- 所有由EventLoop处理的I/O事件都将在它专有的Thread上被处理；
- 一个Channel在它的生命周期内只注册于一个EventLoop；
- 一个EventLoop可能会被分配给一个或多个Channel；

入站 ：ChannelPipeline 头部开始
出站：ChannelPipeline 尾部开始

鉴于出站操作和入站操作是不同的，如果两种类型的ChannelHandler都添加到同一个ChannelPipeline时。Netty能区分ChannelInboundHandler和ChannelOutboundHandler实现，并确保数据只会在具有相同定向类型的两个ChannelHandler之间传递。

netty中有两种发送消息的方式。
Channel中 消息会从ChinnelPipeline尾端开始流动
ChannelHandler相关联的ChannelHandlerContext 消息从ChanelPipeline下一个流动



### ByteBuf

- 堆缓冲区 (支撑数组)

  ```java
  ByteBuf heapBuf = new ByteBuf();
  if(heapBuf.hasArray()){
     	byte[] array = heapBuf.array();
      int offset = heapBuf.arrayOffset() + heapBuf.readerIndex();
      int length = heapBuf.readableBytes();
      handleArray(array,offset,length);
  }
  ```

  

- 直接缓冲区

  ```java
  ByteBuf directBuf = new ByteBuf();
  if(!directBuf.hasArray()){
      int length = directBuf.readableBytes();
      byte[] array = new byte[length];
      directBuf.getBytes(directBuf.readerIndex(),array);
      handleArray(array,0,length);
  }
  ```

  

- 复合缓冲区

  CompositeByteBuf





### Channel的生命周期

|        状态         | 描述                                                         |
| :-----------------: | ------------------------------------------------------------ |
| ChannelUnregistered | Channel已经被创建，但还未注册到EventLoop                     |
|  ChannelRegistered  | Channel已经被注册到EventLoop                                 |
|    ChannelActive    | Channel处于活动状态（已经连接到它的远程节点）。它现在可以接收和发送数据了 |
|   ChannelInactive   | Channel没有连接远程节点                                      |



### ChannelHandler的生命周期

| 方法            | 描述                                              |
| --------------- | ------------------------------------------------- |
| handlerAdded    | 当把ChannelHandler添加到ChannelPipeline中时被调用 |
| handlerRemoved  | 当从ChannelPipeline中移除ChannelHandler时被调用   |
| exceptionCaught | 当处理过程中在ChannelPipeline中有错误产生时被调用 |

Netty定义了下面练个重要的ChannelHandler子接口

- ChannelInboundHandler -- 处理入站数据以及各种状态变化
- ChannelOutboundHandler -- 处理出站数据并且允许拦截所有的操作

### ChannelInboundHandler的方法

|           方法            | 描述                                                         |
| :-----------------------: | ------------------------------------------------------------ |
|     channelRegistered     | 当Channel已经注册到它的EventLoop并且能够处理I/O时被调用      |
|    channelUnregistered    | 当Channel从它的EventLoop注销并且无法处理任何I/O时被调用      |
|       channelActive       | 当Channel处于活动状态是被调用;Channel已经连接/绑定并且已经就绪 |
|      channelInactive      | 当Channel离开活动状态并且不再连接它的远程节点时被调用        |
|    channelReadComplete    | 当Channel上的一个读操作完成时被调用（所有可读字节都已经从Channel中读取） |
|        channelRead        | 当从Channel中读取数据时被调用                                |
| ChannelWritabilityChanged | 当Channel的可写状态发生改变时被调用。用户可以确保写操作不会完成的太快（以避免发生OutOfMemoryError)或者可以在Channel变为再次可写时回复写入。可以通过调用Channel的isWritable()方法来检测。与可写性相关的阈值可以通过Channel.config().setWriteHighWaterMark()和Channel.config().setWriteLowWaterMark()方法来设置。 |
|    userEventTriggered     | 当ChannelnboundHandler.fireUserEventTriggered()方法被调用时被调用，因为一个POJO被传经了ChannelPipeline |

### ChannelOutboundHandler接口

出站操作和数据将由ChannelOutboundHandler处理。它的方法将呗Channel、ChannelPipeline以及ChannelHandlerContext调用。

ChannelOutBoundHandler的一个强大的功能是可以按需推迟操作或者事件，这使得可以通过一些复杂的方法来处理请求。

**channeloutboundHandler的方法**

| 方法                                                         | 描述                                            |
| :----------------------------------------------------------- | ----------------------------------------------- |
| bind(ChannelHandlerContext,SocketAddress,ChannelPromise)     | 当请求将Channel绑定到本地地址时被调用           |
| connect(ChannelHandlerContext,SocketAddress,SocketAddress,ChannelPromise) | 当请求将Channel连接到远程节点时被调用           |
| disconnect(ChannelHandlerContext,ChannelPromise)             | 当请求将Channel从远程节点断开时被调用           |
| close(ChannelHandlerContext,ChannelPromise)                  | 请求关闭Channel时调用                           |
| deregister(ChannelHandlerContext,ChannelPromis)              | 当请求将Channel从它的EventLoop注销时被调用      |
| read(ChannelHandlerContext)                                  | 当请求从Channel读取更多的数据时被调用           |
| flush(ChannelHandlerContext)                                 | 当请求通过Channel将入队数据冲刷到远程节点时调用 |
| write(ChannelHandlerContext,Object,ChannelPromise)           | 请求通过Channel将数据写到远程节点时被调用       |





###资源管理

netty中4中泄露检测级别

| 级别     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| DISABLED | 禁用泄露检测。只有在相近的测试之后才应设置为这个值           |
| SIMPLE   | 使用1%的默认采样率检测并报告任何发现的谢罗。默认级别         |
| ADVANCED | 使用默认的采样率，报告所发现的任何的泄露以及对应的消息被访问的位置 |
| PARANOID | 类似ADVANCED，但是其将会对每次访问都采样。应只在调试阶段使用 |

> 如果一个消息被丢弃或者消费，并且没有传递个ChannelPipline中的下一个ChannelOutboundHandler，那么用户就有责任调用ReferenceCountUtil.release()。如果消息到达了实际的传输层，那么当它呗写入时或者Channel关闭时，都将被自动释放。





