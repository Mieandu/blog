
# Netty

## Netty采用多Reactor多线程模型

- mainReactor 负责监听 ServerSocketChannel ，用来处理客户端新连接的建立，并将建立的客户端的 SocketChannel 指定注册给 subReactor 。

-subReactor 维护自己的 Selector ，基于 mainReactor 建立的客户端的 SocketChannel 多路分离 IO 读写事件，读写网络数据。对于业务处理的功能，另外扔给 worker 线程池来完成。

## Netty 的零拷贝实现

Netty 的接收和发送 ByteBuffer 采用堆外直接内存 Direct Buffer

## Netty 有如下六个核心组件

Bootstrap & ServerBootstrap
Channel
ChannelFuture
EventLoop & EventLoopGroup
ChannelHandler
ChannelPipeline
