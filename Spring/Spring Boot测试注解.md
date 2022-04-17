# SpringBoot测试

## @WebMvcTest

这是Spring Boot所提供的一个特殊测试注解，它会让这个测试在Spring MVC应用的上下文中执行，并会把属性中的Bean注册到Spring容器中

## SpringRunner

## SQL文件

如果在应用的根类路径下存在名为schema.sql和data.sql的文件(可以使用spring.datasource.data/schema进行配置)，那么在应用启动的时候将会基于数据库执行这个文件中的SQL。所以，我们需要将程序清单3.8中的内容保存为名为schema.sql的文件并放到“src/main/resources”文件夹下。

Spring环境会拉取多个属性源，包括
•JVM系统属性；•操作系统环境变量；•命令行参数；•应用属性配置文件。

如果在类路径中存在Tomcat的JDBC连接池，DataSource将使用该连接池

否则，Spring Boot将会在类路径下尝试查找并使用如下的连接池实现：•HikariCP•Commons DBCP 2

将server.port属性显式设置成了0，但是服务器并不会真的在端口0上启动。相反，它会任选一个可用的端口。在我们运行自动化集成测试的时候，这会非常有用，因为这样能够保证并发运行的测试不会与硬编码的端口号冲突。

@ConfigurationProperties 可以为Bean中的属性复制

> 生产环境使用环境变量来设置处于激活状态的profile：
> 在生产环境中，我们可以这样设置SPRING_PROFILES_ACTIVE：
> export SPRING_PROFILES_ACTIVE=prod
> 也可以在命令行中指定属性

使用profile对不同的环境进行配置：application-{profile名}.yml

> 另外，值得一提的是，如果我们将Spring应用部署到Cloud Foundry中，将会自动激活一个名为cloud的profile。如果你的生产环境是Cloud Foundry，那么你可以将生产环境相关的属性放到cloud profile下

@Profile 使用profile条件化地创建bean: 假设我们希望某些bean仅在特定profile激活的情况下才需要创建。在这种情况下，@Profile注解可以将某些bean设置为仅适用于给定的profile。支持非（!）操作
