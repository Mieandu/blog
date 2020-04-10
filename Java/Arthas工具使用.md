# Arthas工具使用

1. 启动目标服务
2. arthas连接目标服务器，选择连接进程

## 可以查看java进程的基本信息

sysprop 打印 System Properties
sysenv 获取环境变量
jvm 打印JVM的各种详细信息
dashboard 查看当前系统的实时数据面板

## sc/sm查看已加载的类

## Ognl命令可以动态执行代码

调用static函数
`ognl '@java.lang.System@out.println("hello ognl")`

## watch

watch可以监控某个类的方法，通过内置对象打印调用信息

`watch com.example.demo.arthas.user.UserController * '{params, throwExp}'`

第一个参数是类名，支持通配
第二个参数是函数名，支持通配
第三个参数是返回值表达式，它实际上是一个ognl表达式，它支持一些内置对象：

- loader
- clazz
- method
- target
- params
- returnObj
- throwExp
- isBefore
- isThrow
- isReturn

应用： 排查函数调用异常

## jad

jad命令可以用来反编译代码
`jad --source-only com.example.demo.arthas.user.UserController > /tmp/UserController.java`

## mc

使用mc命令编译代码，并且通过-c参数指定ClassLoader
`mc -c 1be6f5c3 /tmp/UserController.java -d /tmp`

## redefine

使用redefine命令重新加载新编译好的UserController.class

应用: 热更新代码

## sc 查找加载某个类的Classloader

sc查找加载UserController的ClassLoader

`sc -d *UserController | grep classLoaderHash`

## 动态更新应用Logger Level

### 查找UserController的ClassLoader

`sc -d com.example.demo.arthas.user.UserController | grep classLoaderHash`

### 用ognl获取logger

`ognl -c 1be6f5c3 '@com.example.demo.arthas.user.UserController@logger'`

### 单独设置UserController的logger level

`ognl -c 1be6f5c3 '@com.example.demo.arthas.user.UserController@logger.setLevel(@ch.qos.logback.classic.Level@DEBUG)'`

### 还可修改全局的loggerlever

`ognl -c 1be6f5c3 '@org.slf4j.LoggerFactory@getLogger("root").setLevel(@ch.qos.logback.classic.Level@DEBUG)'`

[实战地址](https://alibaba.github.io/arthas/arthas-tutorials?language=cn&id=arthas-advanced)

## tt

tt即 TimeTunnel，它可以记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测。

### 使用tt命令获取到spring context

`tt -t org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter invokeHandlerMethod`

### 使用tt命令从调用记录里获取到spring context

`tt -i 1000 -w 'target.getApplicationContext()`

### 获取spring bean，并调用函数

`tt -i 1000 -w 'target.getApplicationContext().getBean("helloWorldService").getHelloMessage()'`

## 排查HTTP请求返回401

### 跟踪所有的Filter函数

`trace javax.servlet.Filter *`

### 通过stack获取调用栈

`stack javax.servlet.http.HttpServletResponse sendError 'params[0]==401'`

[Arthas文档地址](https://alibaba.github.io/arthas/advanced-use.html)

### 整理出那些在日常开发中非常有用的案例

### Docker
