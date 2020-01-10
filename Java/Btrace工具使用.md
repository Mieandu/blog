# BTrace使用

## 简介

编写BTrace代码是一个Java平台安全可靠的动态追踪工具。

可以对**正在运行的程序**，进行**动态追踪**、**错误诊断**、**性能剖析**。

BTrace采用**字节码注入**的方式进行跟踪。其它的Java运行时追踪工具还有Greys、Byteman等

## 使用

### 安装Btrace系统环境

1.首先下载BTrace工具

[下载地址](https://github.com/btraceio/btrace/releases)

2.将bin目录加到系统环境变量中

3.使用btrace命令验证是否安装成功,安装成功会出现下面的输出

```
PS D:\doc\final_code\javase\src\main\java\com\mieandu\btrace> btrace
Usage: btrace <options> <pid> <btrace source or .class file> <btrace arguments>
where possible options include:
	--version             Show the version
	-v                    Run in verbose mode
	-o <file>             The path to store the probe output (will disable showing the output in console)
-u                    Run in trusted mode
	-d <path>             Dump the instrumented classes to the specified path
	-pd <path>            The search path for the probe XML descriptors
	-classpath <path>     Specify where to find user class files and annotation processors
	-cp <path>            Specify where to find user class files and annotation processors
	-I <path>             Specify where to find include files
	-p <port>             Specify port to which the btrace agent listens for clients
	-host <host>          Specify host to which btrace client's socket connect
	-statsd <host[:port]> Specify the statsd server, if any

```

### 编写BTrace代码

BTrace最好只使用BTraceUtils中的静态方法，不能修改代码。
可以对方法耗时进行统计，打印线程堆栈，打印方法参数，打印方法执行等情况。

**注意**：

- **在追踪代码中，原则上只能使用BTraceUtils中提供的静态方法**，如果想要使用JDK中的方法，那么在命令行中需要使用-cp指定依赖的Jar
- OnMethod这个注解只会在方法启动的时候被触发，如果该方法已经启动，再运行追踪代码是无法触发的，所以在示例中，先休息了15s钟
  
Btracer可以使用下面四种形式的注解：

- Method Annotations
  - @com.sun.btrace.annotations.OnMethod
  - @com.sun.btrace.annotations.OnTimer
  - @com.sun.btrace.annotations.OnError
  - @com.sun.btrace.annotations.OnExit
  - @com.sun.btrace.annotations.OnEvent
  - @com.sun.btrace.annotations.OnLowMemory
  - @com.sun.btrace.annotations.OnProbe
- Argument Annotations
  - @com.sun.btrace.annotations.Self
  - @com.sun.btrace.annotations.Return
  - @com.sun.btrace.annotations.CalledInstance
  - @com.sun.btrace.annotations.CalledMethod
- Field Annotations
  - @com.sun.btrace.annotations.Export
  - @com.sun.btrace.annotations.Property
  - @com.sun.btrace.annotations.TLS
- Class Annotations
  - @com.sun.btrace.annotations.DTrace
  - @com.sun.btrace.annotations.DTraceRef
  - @com.sun.btrace.annotations.BTrace

#### 代码示例

1.在idea中编写Java代码时首先需要引入BTrace中build中的jar包

2.代码示例：
> ps:一次只能运行一个方法

```
	import com.sun.btrace.AnyType;

	import com.sun.btrace.annotations.*;
	
	import static com.sun.btrace.BTraceUtils.*;
	
	@BTrace
	public class BtracerTest {
	    /*@OnMethod(
	            clazz = "com.example.demo.service.DemoService",
	            method = "getById",
	            location = @Location(Kind.RETURN)
	    )
	    public static void getById(@ProbeClassName String probeClassName, @Duration long duration){
	
	        println(probeClassName + ",duration:" + duration / 1000000 + " ms");
	
	    }*/
	
	  /*  @OnMethod(
	            clazz = "com.example.demo.service.DemoService",
	            method = "getById",
	            location = @Location(value = Kind.CALL, clazz=".*",method=".*",where = Where.AFTER)
	    )
	    public static void getOutCall(@Self Object self,@TargetInstance Object instance,@TargetMethodOrField String methon,@Duration long duration){
	
	        println(methon + ",duration:" + duration / 1000000 + " ms");
	
	    }*/
	
	    /*@OnMethod(
	            clazz = "com.example.demo.service.DemoService",
	            location = @Location(value = Kind.LINE, line = 9)
	    )
	    public static void arrivLine(){
	
	        println("arrive line 9" );
	
	    }*/
	
	    @OnMethod(
	            clazz = "java.lang.System",
	            method = "gc"
	    )
	    public static void gc(){
	
	        println("entered System.gc()" );
	        jstack();
	
	    }
	
	    /*@OnMethod(
	            clazz = "com.example.demo.service.DemoService",
	            method = "getById",
	            location = @Location(Kind.RETURN)
	    )
	    public static void param(@ProbeClassName String probeClassName,String id, @Return AnyType result){
	
	        println( "Id is:" + id);
	
	        println("result:" + result );
	
	    }*/
	}
```

### 运行脚本

将Java代码copy到服务器上执行`btrace -v 29612 BtracerTest.java`

参数解析：

- -v 将输出打印到控制台
- -o 文件名 输出到指定文件，linux中也可以使用 `>>` 命令输出到执行文件
- 29612 Java进程ID

```

	PS D:\doc\final_code\javase\src\main\java\com\mieandu\btrace> btrace -v 29612 BtracerTest.java
	DEBUG: assuming default port 2020
	DEBUG: assuming default classpath '.'
	DEBUG: compiling BtracerTest.java
	DEBUG: compiled BtracerTest.java
	DEBUG: attaching to 29612
	DEBUG: checking port availability: 2020
	DEBUG: attached to 29612
	DEBUG: loading D:\Tools\java\btrace\build\btrace-agent.jar
	DEBUG: agent args: port=2020,statsd=,debug=true,bootClassPath=.,systemClassPath=D:\Tools\java\jdk\jdk1.8.0_192\jre/../lib/tools.jar,probeDescPath=.
	DEBUG: loaded D:\Tools\java\btrace\build\btrace-agent.jar
	DEBUG: registering shutdown hook
	DEBUG: registering signal handler for SIGINT
	DEBUG: submitting the BTrace program
	DEBUG: opening socket to 2020
	DEBUG: setting up client settings
	DEBUG: sending instrument command: []
	DEBUG: entering into command loop
	DEBUG: received com.sun.btrace.comm.OkayCommand@13c27452
	DEBUG: received com.sun.btrace.comm.RetransformationStartNotification@262b2c86
	DEBUG: received com.sun.btrace.comm.OkayCommand@371a67ec
```

当出现OkayCommand时就表示注入成功了，现在就可以愉快的处理问题了。

其它用法参考：<https://github.com/btraceio/btrace/tree/master/samples>
