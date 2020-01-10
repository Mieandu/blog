#Spring Boot

## Spring Boot提供了哪些功能
- 独立运行Spring项目
- 内嵌Servlet容器
- 提供Starter简化Maven配置
- 自动配置Spring Bean
- 准生产应用的监控

### 优点
- 使【编码】变简单。
- 使【配置】变简单。
- 使【部署】变简单。
- 使【监控】变简单。

### 缺点
因为自动配置 Spring Bean 的功能，我们可能无法知道，哪些 Bean 被进行创建了。这个时候，如果我们想要自定义一些 Bean ，可能存在冲突，或者不知道实际注入的情况


## Spring Boot 2.X 有什么新特性？

1. 起步 JDK 8 和支持 JDK 9
1. 第三方库的升级
1. Reactive Spring
1. HTTP/2 支持
1. 配置属性的绑定
1. Gradle 插件
1. Actuator 改进
1. 数据支持的改进
1. Web 的改进
1. 支持 Quartz 自动配置
1. 测试的改进

## 如何统一引入 Spring Boot 版本？
① 方式一：继承 spring-boot-starter-parent 项目。配置代码如下：
② 方式二：导入 spring-boot-dependencies 项目依赖。(推荐)

	<dependencyManagement>
		<dependencies>
			<dependency>
	    		<groupId>org.springframework.boot</groupId>
	    		<artifactId>spring-boot-dependencies</artifactId>
	    		<version>1.5.1.RELEASE</version>
	    		<type>pom</type>
	    		<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>


## 什么是 Spring Boot 自动配置
- Spring Boot 在启动时扫描项目所依赖的 jar 包，寻找包含spring.factories 文件的 jar 包。
- 根据 spring.factories 配置加载 AutoConfigure 类。
- 根据 @Conditional 等条件注解 的条件，进行自动配置并将 Bean 注入 Spring IoC 中。
> 可以看看Conditional相关内容

## Spring Boot 有哪几种读取配置的方式？
- @Value注解读取配置到属性
- @ConfigurationProperties读取配置到类上
>  ps： 可以配合@PropertySource注解一起使用，指定使用的配置文件

## Spring Boot 如何定义多套不同环境配置？
spring.profiles.active属性


#Spring Cloud

Spriing Cloud用于构建微服务的技术栈

##核心功能
- 配置管理
- 服务注册和发现
- 服务监控
- 负载均衡
- 路由
- 断路器
- 分布式消息

![Spring Cloud基本组件整合图](http://static2.iocoder.cn/64e9a7827c76d38f899160da6f736ea2)

## 注册中心 Eureka
### Eureka自我保护模式
如果在15分钟内超过85%的节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了网络故障，此时会出现以下几种情况：

1. Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
1. Eureka仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上(即保证当前节点依然可用)
1. 当网络稳定时，当前实例新的注册信息会被同步到其它节点中

因此， Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像zookeeper那样使整个注册服务瘫痪。

## 负载均衡 Ribbon
负载平衡旨在优化资源使用，最大化吞吐量，最小化响应时间并避免任何单一资源的过载。使用多个组件进行负载平衡而不是单个组件可能会通过冗余来提高可靠性和可用性

### Ribbon和Eureka的整合
- 首先，Ribbon 会从 Eureka Client 里获取到对应的服务列表。
- 然后，Ribbon 使用负载均衡算法获得使用的服务。
- 最后，Ribbon 调用对应的服务。

### Ribbon负载均衡策略：
- RandomRule
- RoundRobinRule
- WeightedResponseTimeRule 根据响应时间选择配置权重
- BestAvailableRule 选择一个最小并发请求的server

## 服务调用 Feign
###Feign 实现原理？
- 首先，如果你对某个接口定义了 @FeignClient 注解，Feign 就会针对这个接口创建一个动态代理。
- 接着你要是调用那个接口，本质就是会调用 Feign 创建的动态代理，这是核心中的核心。
- Feig n的动态代理会根据你在接口上的 @RequestMapping 等注解，来动态构造出你要请求的服务的地址。
- 最后针对这个地址，发起请求、解析响应。
![](http://static2.iocoder.cn/6650aa32de0def76db0e4c5228619aef)
## 服务保障 hystrix
由于网络原因或者自身的原因，服务并不能保证服务的 100% 可用，如果单个服务出现问题，调用这个服务就会出现网络延迟，此时若有大量的网络涌入，会形成任务累积，导致服务瘫痪，甚至导致服务“雪崩
### Hystrix原理
Hystrix 断路器通过 HystrixCircuitBreaker 实现。HystrixCircuitBreaker 有三种状态 ：

- CLOSED ：关闭
- OPEN ：打开
- HALF_OPEN ：半开
![](http://static2.iocoder.cn/images/Hystrix/2018_11_08/01.png)
- 红线 ：初始时，断路器处于 CLOSED 状态，链路处于健康状态。当满足如下条件，断路器从 CLOSED 变成 OPEN 状态：
周期( 可配，HystrixCommandProperties.default_metricsRollingStatisticalWindow = 10000 ms )内，总请求数超过一定量( 可配，HystrixCommandProperties.circuitBreakerRequestVolumeThreshold = 20 ) 。
错误请求占总请求数超过一定比例( 可配，HystrixCommandProperties.circuitBreakerErrorThresholdPercentage = 50% ) 。
- 绿线 ：断路器处于 OPEN 状态，命令执行时，若当前时间超过断路器开启时间超过一定时间( HystrixCommandProperties.circuitBreakerSleepWindowInMilliseconds = 5000 ms )，断路器变成 HALF_OPEN 状态，尝试调用正常逻辑，根据执行是否成功，打开或关闭熔断器【蓝线】。
### Hystrix资源隔离策略
- 线程池
- 信号量
### Hystrix服务降级（后备器模式）

## 网关服务 zuul,gateway
- 动态路由
- 灰度发布
- 健康检查
- 限流
- 熔断
- 认证: 如数支持 HMAC, JWT, Basic, OAuth 2.0 等常用协议
- 鉴权: 权限控制，IP 黑白名单，同样是 OpenResty 的特性
- 可用性
- 高性能