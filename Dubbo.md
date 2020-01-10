## Dubbo 框架的分层设计

相对比较复杂，一共分成 10 层[Dubbo核心流程一览](http://svip.iocoder.cn/)

![dubbo分层](http://static2.iocoder.cn/images/Dubbo/2018_03_01/01.png)
图例说明

最顶上九个图标，代表本图中的对象与流程。
图中左边 淡蓝背景( Consumer ) 的为服务消费方使用的接口，右边 淡绿色背景( Provider ) 的为服务提供方使用的接口，位于中轴线上的为双方都用到的接口。
图中从下至上分为十层，各层均为单向依赖，右边的 黑色箭头( Depend ) 代表层之间的依赖关系，每一层都可以剥离上层被复用。其中，Service 和 Config 层为 API，其它各层均为 SPI 。

注意，Dubbo 并未使用 JDK SPI 机制，而是自己实现了一套 Dubbo SPI 机制。

图中 绿色小块( Interface ) 的为扩展接口，蓝色小块( Class ) 为实现类，图中只显示用于关联各层的实现类。

图中 蓝色虚线( Init ) 为初始化过程，即启动时组装链。红色实线( Call )为方法调用过程，即运行时调时链。紫色三角箭头( Inherit )为继承，可以把子类看作父类的同一个节点，线上的文字为调用的方法。
各层说明

虽然，有 10 层这么多，但是总体是分层 Business、RPC、Remoting 三大层。如下：

==================== Business ====================

- **Service 业务层**：业务代码的接口与实现。我们实际使用 Dubbo 的业务层级。

> 接口层，给服务提供者和消费者来实现的。

==================== RPC ====================

- **config 配置层**：对外配置接口，以 ServiceConfig, ReferenceConfig 为中心，可以直接初始化配置类，也可以通过 Spring 解析配置生成配置类。

>配置层，主要是对 Dubbo 进行各种配置的。

- **proxy 服务代理层**：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 扩展接口为 ProxyFactory 。

> 服务代理层，无论是 consumer 还是 provider，Dubbo 都会给你生成代理，代理之间进行网络通信。

如果胖友了解 Spring Cloud 体系，可以类比成 Feign 对于 consumer ，Spring MVC 对于 provider 。

- registry 注册中心层：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 RegistryFactory, Registry, RegistryService 。

> 服务注册层，负责服务的注册与发现。

如果胖友了解 Spring Cloud 体系，可以类比成 Eureka Client 。

- **cluster 路由层**：封装多个提供者的路由及负载均衡，并桥接注册中心，以 Invoker 为中心，扩展接口为 Cluster, Directory, Router, LoadBalance 。

>集群层，封装多个服务提供者的路由以及负载均衡，将多个实例组合成一个服务。

如果胖友了解 Spring Cloud 体系，可以类比城 Ribbon 。

- **monitor 监控层**：RPC 调用次数和调用时间监控，以 Statistics 为中心，扩展接口为 MonitorFactory, Monitor, MonitorService 。

>监控层，对 rpc 接口的调用次数和调用时间进行监控。

如果胖友了解 SkyWalking 链路追踪，你会发现，SkyWalking 基于 MonitorFilter 实现增强，从而透明化埋点监控。

==================== Remoting ====================

- **protocol 远程调用层**：封将 RPC 调用，以 Invocation, Result 为中心，扩展接口为 Protocol, Invoker, Exporter 。

>远程调用层，封装 rpc 调用。

- **exchange 信息交换层**：封装请求响应模式，同步转异步，以 Request, Response 为中心，扩展接口为 Exchanger, ExchangeChannel, ExchangeClient, ExchangeServer 。

>信息交换层，封装请求响应模式，同步转异步。

- **transport 网络传输层**：抽象 mina 和 netty 为统一接口，以 Message 为中心，扩展接口为 Channel, Transporter, Client, Server, Codec 。

>网络传输层，抽象 mina 和 netty 为统一接口。

- **serialize 数据序列化层**：可复用的一些工具，扩展接口为 Serialization, ObjectInput, ObjectOutput, ThreadPool 。

>数据序列化层。

## Dubbo 在 Zookeeper 存储了哪些信息

![](http://static2.iocoder.cn/images/Dubbo/2018_08_04/01.png)


在图中，我们可以看到 Zookeeper 的节点层级，自上而下是：

- Root 层：根目录，可通过 <dubbo:registry group="dubbo" /> 的 "group" 设置 Zookeeper 的根节点，缺省使用 "dubbo" 。
- Service 层：服务接口全名。
- Type 层：分类。目前除了我们在图中看到的 "providers"( 服务提供者列表 ) "consumers"( 服务消费者列表 ) 外，还有 "routes"( 路由规则列表 ) 和 "configurations"( 配置规则列表 )。
- URL 层：URL ，根据不同 Type 目录，下面可以是服务提供者 URL 、服务消费者 URL 、路由规则 URL 、配置规则 URL 。
- 实际上 URL 上带有 "category" 参数，已经能判断每个 URL 的分类，但是 Zookeeper 是基于节点目录订阅的，所以增加了 Type 层。
- 实际上，服务消费者启动后，不仅仅订阅了 "providers" 分类，也订阅了 "routes" "configurations" 分类。

## Dubbo支持哪些通信协议?

dubbo,rest,rmi,http,webservice,thrift,redis

## Dubbo支持哪些序列化方式

hessian2（dubbo协议的默认序列化），Dubbo，kryo,fst,json,nativeJave,CompactedJava 

## Dubbo负载均衡策略

随机，轮询， 最小活跃数，一致性hash

## 集群容错策略

**Failover Cluster:**
失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。可通过 retries="2" 来设置重试次数(不含第一次)。

**Failfast Cluster:** (一般用这种)
快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

**Failsafe Cluster:** 失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

**Failback Cluster:** 失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

**Forking Cluster:** 并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 forks="2" 来设置最大并行数。

**Broadcast Cluster:** 广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。

## Dubbo 如何做限流?

在做服务稳定性时，有一句非常经典的话：

- 怀疑第三方
- 防备使用方
- 做好自己
那么，上面看到的服务降级，就属于怀疑第三方。
而本小节的限流目的，就是防备使用方。

此处，艿艿要再推荐一篇文章：《你应该如何正确健壮后端服务？》 。

目前，在 Dubbo 中，实现服务降级的功能，一共有两大种方式。

① Dubbo 原生自带的限流功能

通过 TpsLimitFilter 实现，仅适用于服务提供者。具体的使用方式，源码实现，看看 《精尽 Dubbo 源码分析 —— 过滤器（九）之 TpsLimitFilter》 。

 参照 TpsLimitFilter 的思路，可以实现自定义限流的 Filter ，并且使用 Guava RateLimiter 工具类，达到 令牌桶算法限流 的功能。

② 引入支持限流的组件

关于这个功能，还是推荐集成 Sentinel 组件。

## Dubbo 服务如何做降级

① Dubbo 原生自带的服务降级功能（mock）
② 引入支持服务降级的组件

## Dubbo 如何集成配置中心

- Dubbo 自身配置
  
> Provider 启动时，会注册到注册中心中，包括我们在 <dubbo:service /> 中的配置。
> Consumer 启动时，从注册中心获取到 Provider 列表后，会合并它们在 <dubbo:service /> 的配置来使用

- 非 Dubbo 自身配置（配置中心）

## Dubbo SPI 的设计思想是什么

## Dubbo优雅停机

### 服务提供方的优雅停机过程

首先，从注册中心中取消注册自己，从而使消费者不要再拉取到它。
然后，sleep 10 秒( 可配 )，等到服务消费，接收到注册中心通知到该服务提供者已经下线，加大了在不重试情况下优雅停机的成功率。😈 此处是个概率学，嘻嘻。
之后，广播 READONLY 事件给所有 Consumer 们，告诉它们不要在调用我了！！！【很有趣的一个步骤】并且，如果此处注册中心挂掉的情况，依然能达到告诉 Consumer ，我要下线了的功能。
再之后，sleep 10 毫秒，保证 Consumer 们，尽可能接收到该消息。
再再之后，先标记为不接收新请求，新请求过来时直接报错，让客户端重试其它机器。
再再再之后，关闭心跳线程。
最后，检测线程池中的线程是否正在运行，如果有，等待所有线程执行完成，除非超时，则强制关闭。
最最后，关闭服务器。
整个过程比较复杂，感兴趣的胖友，可以详细来看看 《精尽 Dubbo 源码解析 —— 优雅停机》 。

### 服务消费方的优雅停机过程

停止时，不再发起新的调用请求，所有新的调用在客户端即报错。
然后，检测有没有请求的响应还没有返回，等待响应返回，除非超时，则强制关闭
