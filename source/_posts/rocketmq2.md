title: RocketMQ系列
tags:
  - RocketMQ
categories:
  - 技术积累
thumbnail: 'http://7xth6d.com1.z0.glb.clouddn.com/images/thumbnails/accounting.jpg'
comments: true
date: 2017-07-03 10:50:00
origin_type : 'f'

---

目前RocketMQ在Apache的项目标识还是孵化中，版本号为4.1，包含子模块的数量比起3系列略有增加，：

* rocketmq-common：通用的常量枚举、基类方法或者数据结构。 子包有：admin，consumer，filter，hook，message等。
* rocketmq-remoting：用Netty4写的客户端和服务端，fastjson做的序列化，自定义二进制协议。
* rocketmq-srvutil：服务端的命令行解析。
* rocketmq-store：存储服务，消息存储，索引存储，commitLog存储。
* rocketmq-client:客户端，包含producer端和consumer端，发送消息和接收消息的实现细节。
* rocketmq-filter:消息过滤器,提供自定义表达式与解析逻辑。
* rocketmq-filtersrv:消息过滤器server。
* rocketmq-broker：对consumer和producer来说是服务端，接收producer发来的消息并存储，同时consumer来这里拉取消息。
* rocketmq-tools：命令行工具。
* rocketmq-namesrv：NameServer，类似SOA服务的注册中心，这里保存着消息的TopicName，队列等运行时的meta信息。一般系统分dataNode和nameNode，这里是nameNode。
* rocketmq-logappender：日志组件，支持log4j与logback，支持slf4j接口。
* rocketmq-openmessaging: 基于openmessaging接口的实现。
* rocketmq-distribution: 编译后供服务端部署。
* rocketmq-example: 提供了许多实用的demo。