## RocketMQ Architecture

![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ%E5%9F%BA%E6%9C%AC%E6%9E%B6%E6%9E%84.png?raw=true)    

RocketMQ主要由四部分组成：    
1. NameServer Cluster    
    + Broker管理，NameServer接收broker的注册并通过心跳机制检查broker服务是否可用    
    + 提供轻量级的服务发现和路由功能；每个Name Server节点存储了全部broker的路由信息和queue信息 

2. Broker Cluster    
    + 通过`topic`和`queue`机制提供消息(`message`)的存储功能    
    + 支持消息的`Push`和`Pull`模式    
    + 通过备份机制（2-3个消息副本）实现容错能力    
    + 提供灾难恢复、丰富的指标统计、告警机制    
    
3. Producer Cluster    
    + 支持producer的分布式部署    
    + 分布式环境下producer通过负载均衡策略向Broker发送消息    
    
4. Consumer Cluster    
    + `push`和`pull`模式下，Consumer都支持分布式部署    
    + 支持集群消费和消息广播    
    
### Broker Server    
Broker服务器负责消息的投递、存储、查询、高可用保证等。Broker主要由以下几部分组成：    
+ 远程连接模块    
    broker的入口，处理来自客户端的连接    
+ 客户端管理模块    
    管理客户端(`Producer/Consumer`)连接，维护客户端的topic订阅关系    
+ 存储服务模块    
    提供简单的api以存储或查询消息    
+ HA服务模块    
    提供master broker和slave broker之间的数据同步功能    
+ 索引服务    
    创建消息的索引，以提供快速查询功能

![](https://github.com/huhuics/Accumulate/blob/master/image/RocketBroker%E5%9F%BA%E6%9C%AC%E6%9E%B6%E6%9E%84.png?raw=true)    

## Core Concept    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ%E6%A0%B8%E5%BF%83%E6%A6%82%E5%BF%B5.png?raw=true)    

### Producer    
向broker发送应用系统产生的消息，有多种发送策略：`synchronous`, `asynchronous`, `one-way`    

#### Producer Group    
    相同角色的producer将会被分到同一组
> Warning: Considering the provided producer is sufficiently powerful at sending messages, only one instance is allowed per producer group to avoid unnecessary initialization of producer instances.    

### Consumer    
#### PullConsumer    
    从broker拉取消息，如果批量消息被拉取，应用程序将初始化消费者线程    

#### PushConsumer    
    客户端提供回调方法，当有消息到达broker时，broker主动将消息推给consumer    

#### Consumer Group    
    与producer一样，相同角色的consumer将会被分到同一组    
> consumer group中的所有consumer实例必须订阅了相同topic    

### Topic    
    Topic表示一类消息。一个生产者可以发送不同topic的消息，producer与topic的关系是多对多，consumer与topic的关系也是多对多    

### Message    
    Message是被投递的信息。一个message必须有一个topic，类比于一封邮件必须有一个地址。message可以选择是否有`tag`或key-value键值对    

### Message Queue    
    相当于sub-topic，不同topic会被分到一个或多个queue中    

### Message Order    
+ 顺序消费
    顺序消费是消息的消费顺序与生产者生产的顺序一致，如果要对消息消费有顺序要求，应确保这个topic只有一个queue。    

> 如果指定了消费顺序，消息消费的最大并发为该队列被订阅的消费者数量    

+ 并发消费    
    如果没有指定消费顺序，即并发消费，最大并发量只受消费者客户端的线程池大小约束    
    
> 如果是并发消费模式，则不再保证消息的消费顺序    























