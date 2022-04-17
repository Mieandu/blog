# Java优雅的异步处理

## 使用Thread、Runnable、Callable处理异步

Java中创建线程的方式有两种一种是继承Thread；另外一种是实现Runnable或者Callable接口，然后交给Thread去创建。这两种方式处理起来都不够优雅，缺点：
1. 需要创建一个新的业务类或者使用匿名内部类（使用lambda会稍微好一点），然后调用Thread.start()去启动线程。如果需要获取结果，则还需要调用Future.get()去阻塞线程，想想如果有多个异步任务要执行，代码将变成啥样
2. 无法组合多个异步任务的结果
3. 每次都需要创建一个新的线程去执行任务，性能不佳

## 使用线程池解决线程创建开销

使用线程池的优点：

- 降低资源消耗：通过重复利用已创建的线程降低线程创建和销毁造成的消耗。

- 提高响应速度：当任务到达时，可以不需要等待线程创建就能立即执行。

- 提高线程的可管理性：线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，监控和调优。

代码示例：
```java
public void excutorsTest() throws InterruptedException {
        ExecutorService excutors = Executors.newFixedThreadPool(4);
        Stream.of(1, 2, 3).map(x -> new FutureTask<>(() -> {
            System.out.println("值：" + x);
            return x;
        })).forEach(excutors::submit);
        Thread.sleep(1000);
    }
```

## 使用CompletableFuture解决代码繁琐的问题

CompletableFuture提供了非常强大的Future的扩展功能，可以帮助我们简化异步编程的复杂性，并且提供了函数式编程的能力，可以通过回调的方式处理计算结果，也提供了转换和组合 CompletableFuture 的方法。

计算两个CompletableFuture的结果之和：
```java
public void thenCombine() throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> result = CompletableFuture.supplyAsync(() -> {
            return 111;
        }).thenCombine(CompletableFuture.supplyAsync(()-> 2), Integer::sum);
        log.info("结果："+result.get());
    }
```
更多用法可以参照[CompletableFuture基本用法](https://www.cnblogs.com/cjsblog/p/9267163.html)

## 使用Spring提供的注解@EnableAsync、@Async（推荐）

@EnableAsync、@Async是Spring提供的方法异步执行的注解，以声明式的方式处理异步

使用@EnableAsync开启异步方法功能，默认启动流程：
1 搜索关联的线程池定义：上下文中唯一的 TaskExecutor 实例，或一个名为 taskExecutor 的 java.util.concurrent.Executor 实例；
2 如果以上都没找到，则会使用 SimpleAsyncTaskExecutor 处理异步方法调用。

@Async会使用动态代理代理注解的方法

代码示例：
```java
@Configuration
@EnableAsync
public class TaskPoolConfig {
    @Bean("taskExecutor")
    public Executor taskExecutro(){
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(10);
        taskExecutor.setMaxPoolSize(50);
        taskExecutor.setQueueCapacity(200);
        taskExecutor.setKeepAliveSeconds(60);
        taskExecutor.setThreadNamePrefix("taskExecutor--");
        taskExecutor.setWaitForTasksToCompleteOnShutdown(true);
        taskExecutor.setAwaitTerminationSeconds(60);
        return taskExecutor;
    }
}

@Async
public void asyncHandle(String id){
        System.out.println("异步执行,当前线程：" + Thread.currentThread().getName());
}
```
> @Async注解有可能会导致循环引用问题，原因及解决方案可以看这里[@Async注解导致循环依赖](https://blog.csdn.net/w15868676598/article/details/100139058),我这里不做过多赘述 


