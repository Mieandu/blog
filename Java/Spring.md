## Spring的好处
1.轻量级的,松散耦合的
2.可以方便的集成很多框架
3.分层体系结构，允许用户选择组件
4.事务管理
5.模块化
6.异常处理
7.面向切面编程
## 谈谈Spring原理
1.Spring的架构

2.SpringIOC和依赖注入
    2.1 什么是SpringIOC
        **Spring 框架的核心是 Spring IoC 容器。IOC即控制反转，容器创建 Bean 对象，将它们装配在一起，配置它们并管理它们的完整生命周期。**
            - Spring 容器使用依赖注入来管理组成应用程序的 Bean 对象。
            - 容器通过读取提供的配置元数据 Bean Definition 来接收对象进行实例化，配置和组装的指令。
            - 该配置元数据 Bean Definition 可以通过 XML，Java 注解或 Java Config 代码提供。
3.AOP

## 什么是依赖注入（分setter注入，构造函数注入）
   在依赖注入中，你不必主动的创建对象，只需要描述他们之间的关系，通过IOC容器来创建，然后装配到一起
## IOC和依赖注入的区别
   ioc是指让生成类的方式由传统方式（new）反过来，即程序员不调用new,需要类的时候由框架注入（di），是同一件不同层面的解读。

## SpringBean的生命周期

- Spring Bean 的初始化流程如下：

- 实例化 Bean 对象

	- Spring 容器根据配置中的 Bean Definition(定义)中实例化 Bean 对象。
	
	- Bean Definition 可以通过 XML，Java 注解或 Java Config 代码提供。

	- Spring 使用依赖注入填充所有属性，如 Bean 中所定义的配置。

- Aware 相关的属性，注入到 Bean 对象
	- 如果 Bean 实现 BeanNameAware 接口，则工厂通过传递 Bean 的 beanName 来调用 #setBeanName(String name) 方法。
	- 如果 Bean 实现 BeanFactoryAware 接口，工厂通过传递自身的实例来调用 #setBeanFactory(BeanFactory beanFactory) 方法。
	- 调用相应的方法，进一步初始化 Bean 对象
- 如果存在与 Bean 关联的任何 BeanPostProcessor 们，则调用 #preProcessBeforeInitialization(Object bean, String beanName) 方法。
	- 如果 Bean 实现 InitializingBean 接口，则会调用 #afterPropertiesSet() 方法。
	- 如果为 Bean 指定了 init 方法（例如 <bean /> 的 init-method 属性），那么将调用该方法。
	- 如果存在与 Bean 关联的任何 BeanPostProcessor 们，则将调用 #postProcessAfterInitialization(Object bean, String beanName) 方法。
- Spring Bean 的销毁流程如下：

	- 如果 Bean 实现 DisposableBean 接口，当 spring 容器关闭时，会调用 #destroy() 方法。
	- 如果为 bean 指定了 destroy 方法（例如 <bean /> 的 destroy-method 属性），那么将调用该方法。
	

![Spring Bean生命周期](http://static2.iocoder.cn/images/Spring/2018-12-24/08.png)
## Spring事务
1.声明式事务（一般使用生命式事务）
2.编程式事务

事务的传播
事务的隔离级别

## Spring解决循环依赖的问题
[Spring怎么解决循环依赖的问题](http://svip.iocoder.cn/Spring/IoC-get-Bean-createBean-5/)



