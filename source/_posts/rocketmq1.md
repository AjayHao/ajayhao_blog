title: RocketMQ系列（一）
tags:
  - RocketMQ
  - 消息
categories:
  - 技术积累
thumbnail: '/images/thumbnails/accounting.jpg'
comments: true
date: 2017-06-27 10:50:00
original: false

---
RocketMQ是一个分布式开放消息中间件，底层基于队列模型来实现消息收发功能。RocketMQ集群中包含4个模块：Namesrv, Broker, Producer, Consumer。

Namesrv: 存储当前集群所有Brokers信息、Topic跟Broker的对应关系。
Broker: 集群最核心模块，主要负责Topic消息存储、消费者的消费位点管理（消费进度）。
Producer: 消息生产者，每个生产者都有一个ID(编号)，多个生产者实例可以共用同一个ID。同一个ID下所有实例组成一个生产者集群。
Consumer: 消息消费者，每个订阅者也有一个ID(编号)，多个消费者实例可以共用同一个ID。同一个ID下所有实例组成一个消费者集群。

集群工作流程：
1，启动Namesrv，Namesrv起来后监听端口，等待Broker、Produer、Consumer连上来，相当于一个路由控制中心。
2，Broker启动，跟所有的Namesrv保持长连接，定时发送心跳包。心跳包中包含当前Broker信息(IP+端口等)以及存储所有topic信息。注册成功后，namesrv集群中就有Topic跟Broker的映射关系。
3，收发消息前，先创建topic，创建topic时需要指定该topic要存储在哪些Broker上。也可以在发送消息时自动创建Topic。
4，Producer发送消息，启动时先跟Namesrv集群中的其中一台建立长连接，并从Namesrv中获取当前发送的Topic存在哪些Broker上，然后跟对应的Broker建长连接，直接向Broker发消息。
5，Consumer跟Producer类似。跟其中一台Namesrv建立长连接，获取当前订阅Topic存在哪些Broker，然后直接跟Broker建立连接通道，开始消费消息。

win7环境下 搭建过程：
1. git下载rocketmq，执行编译

    > git clone -b develop https://github.com/apache/incubator-rocketmq.git
    > cd incubator-rocketmq
    > mvn -Prelease-all -DskipTests clean install -U
    > cd distribution/target/apache-rocketmq

2. 配置ROCKETMQ_HOME环境变量，目录指向distribution的编译结果目录：distribution/target/apache-rocketmq

3. 启动Name Server，日志路径：~/logs/rocketmqlogs/namesrv.log

    > mqnamesrv.cmd

4. 启动Broker， 文件存储路径： ~/store/config/

    > mqbroker.cmd -n localhost:9876 autoCreateTopicEnable=true

5. 消息持久化路径： ~/store/consumequeue/<Topic>
