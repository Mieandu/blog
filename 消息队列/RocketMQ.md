# RocketMQ

1.安装
2.控制台安装
rocketmq-console-ng

### 架构

1. 启动nameserver集群

2. Broker 与Namesrv保持长连接，定时发送心跳包

    > 心跳包中，包含当前 Broker 信息(IP+端口等)以及存储所有 Topic 信息。
      注册成功后，Namesrv 集群中就有 Topic 跟 Broker 的映射关系。

3. 收发消息前，先创建 Topic 。创建 Topic 时，需要指定该 Topic 要存储在 哪些 Broker上。也可以在发送消息时自动创建Topic。

4. Producer 发送消息
   
> 启动时，先跟 Namesrv 集群中的其中一台建立长连接，并从Namesrv 中获取当前发送的 Topic 存在哪些 Broker 上，然后跟对应的 Broker 建立长连接，直接向 Broker 发消息。

5.Consumer消费消息
> Consumer 跟 Producer 类似。跟其中一台 Namesrv 建立长连接，获取当前订阅 Topic 存在哪些 Broker 上，然后直接跟 Broker 建立连接通道，开始消费消息。

###开发
生产者默认会把消息存储到4个队列
1.创建生产者
DefaultMQProducer
2.设置NameSvr地址
3.开启生产者
4.创建消息
Message：
topic 主题（默认生成）
tags 主要用于消息过滤
keys 消息的唯一值
body 消息体

5.发送消息


1.创建DefaultMQPushConsumer
2.设置NameSvr地址
3.设置subscribe，这里是要读取的主题信息
4.创建消息监听MessageListener
5.获取消息
5.返回消息读取状态