## 一、RocketMQ Architecture

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

## 二、Core Concept    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ%E6%A0%B8%E5%BF%83%E6%A6%82%E5%BF%B5.png?raw=true)    

### 1.Producer    
向broker发送应用系统产生的消息，有多种发送策略：`synchronous`, `asynchronous`, `one-way`    

#### Producer Group    
    相同角色的producer将会被分到同一组
> Warning: Considering the provided producer is sufficiently powerful at sending messages, only one instance is allowed per producer group to avoid unnecessary initialization of producer instances.    

### 2.Consumer    
#### PullConsumer    
    从broker拉取消息，如果批量消息被拉取，应用程序将初始化消费者线程    

#### PushConsumer    
    客户端提供回调方法，当有消息到达broker时，broker主动将消息推给consumer    

#### Consumer Group    
    与producer一样，相同角色的consumer将会被分到同一组    
> consumer group中的所有consumer实例必须订阅了相同topic    

### 3.Topic    
    Topic表示一类消息。一个生产者可以发送不同topic的消息，producer与topic的关系是多对多，consumer与topic的关系也是多对多    

### 4.Message    
    Message是被投递的信息。一个message必须有一个topic，类比于一封邮件必须有一个地址。message可以选择是否有`tag`或key-value键值对    

### 5.Message Queue    
    相当于sub-topic，不同topic会被分到一个或多个queue中    

### 6.Message Order    
+ 顺序消费
    顺序消费是消息的消费顺序与生产者生产的顺序一致，如果要对消息消费有顺序要求，应确保这个topic只有一个queue。    

> 如果指定了消费顺序，消息消费的最大并发为该队列被订阅的消费者数量    

+ 并发消费    
    如果没有指定消费顺序，即并发消费，最大并发量只受消费者客户端的线程池大小约束    
    
> 如果是并发消费模式，则不再保证消息的消费顺序    

## 三、RocketMQ物理部署结构    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ%E7%89%A9%E7%90%86%E9%83%A8%E7%BD%B2%E7%BB%93%E6%9E%84.png?raw=true)    
如上图所示，RocketMQ的部署结构有以下特点：    
  + NameServer是一个几乎无状态节点，可集群部署，节点之间无任何信息同步。    
  + Broker部署相对复杂，Broker分为Master与Slave，一个Master可以对应多个Slave，但是一个Slave只能对应一个Master，Master与Slave的对应关系通过指定相同的BrokerName，不同的BrokerId来定义，BrokerId为0表示Master，非0表示Slave。Master也可以部署多个。每个Broker与NameServer集群中的所有节点建立长连接，定时注册Topic信息到所有NameServer。    
  + Producer与NameServer集群中的其中一个节点（随机选择）建立长连接，定期从NameServer取Topic路由信息，并向提供Topic服务的Mater建立长连接，且定时向Master发送心跳。Producer完全无状态，可集群部署。    
  + Consumer与NameServer集群中其中一个节点（随机选择）建立长连接，定期从NameServer取Topic路由信息，并向提供Topic服务的Master、Slave建立长连接，且定时向Master、Slave发送心跳。Consumer既可以从Master订阅消息，也可以从Slave订阅消息，订阅规则由Broker配置决定。

## 四、关键特性及实现    
以下内容来源自： [http://www.jianshu.com/p/453c6e7ff81c](http://www.jianshu.com/p/453c6e7ff81c)
### 1.顺序消息    
假如生产者产生了2条消息：M1、M2，要保证这两条消息的顺序，应该怎样做？可能是这样：    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ-OrderM1.png?raw=true)    

假定M1发送到S1，M2发送到S2，如果要保证M1先于2被消费，那么需要M1达到消费端被消费后，通知S2，然后S2再将M2发送到消费端。    

这个模型存在的问题是，如果M1和M2分别发送到两台Server上，就不能保证M1先到达MQ集群，也不能保证M1被先消费。换个角度看，如果M2先于M1到达MQ集群，甚至M2被消费后，M1才到达消费端，这时消息也就乱序了，说明以上模型是不能保证消息的顺序的。如何才能在MQ集群保证消息的顺序？一种简单的方式就是将M1、M2发送到同一个MQ Server上：    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ-OrderM2.png?raw=true)    

这样可以保证M1先于M2到达MQServer（生产者等待M1发送成功后再发送M2），根据先到达先被消费的原则，M1会先于M2被消费，这样就保证了消息的顺序。    

这个模型也仅仅是理论上可以保证消息的顺序，在实际场景中可能会遇到下面的问题：    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ-OrderM3.png?raw=true)    

只要将消息从一台服务器发往另一台服务器，就会存在网络延迟问题。如上图所示，如果发送M1耗时大于发送M2的耗时，那么M2就扔将被先消费，仍然不能保证消息的有序。即使M1和M2同事到达消费端，由于不清楚消费端1和消费端2的负载情况，仍然有可能出现M2先于M1被消费的情况。    

那如何解决这个问题？将M1和M2发往同一个消费者，且发送M1后，需要消费端响应成功后才能发送M2。这可能引起另外的问题：如果M1被发送到消费端后，消费端1没有响应，那么是继续发送M2，还是重新发送M1？一般为了保证消息一定被消费，肯定会选择重发M1到另外一个消费端2，如下图所示：    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ-OrderM4.png?raw=true)    

这样的模型就严格保证消息的顺序，但如果消费端没有响应MQServer呢？消费端1没有响应MQServer时有两种情况，一种是M1确实没有到达（数据在网络传送中丢失）；另外一种消费端已经消费M1且已经发送响应信息，只是MQServer端没有收到。如果是第二种情况，重发M1，就会造成M1被重复消费。    

严格的顺序消息容易理解，总结起来，要实现严格的顺序消息，简单且可行的办法是：    
> 保证 `生产者 - MQServer - 消费者` 是一对一对一的关系    

这样设计虽然简单，但是也会存在一些很严重的问题，比如：    
  + 并行度就会成为消息系统的瓶颈（吞吐量不够）    
  + 更多的异常处理，比如：只要消费端出了问题，就会导致整个处理流程阻塞    
 
但我们需要的是集群的高容错性和高吞吐量，那么RocketMQ是如何解决了呢？    

有些问题看起来很重要，但实际上我们通过**合理设计**或者**将问题分解**来规避，如果硬要把时间花在解决问题本身，实际不仅效率低下，也是一种浪费。从这个角度来看消息的顺序问题，我们可以得出两个结论：    
> 1.不关注顺序的应用实际大量存在    
2.队列无序并不意味着消息无序    

所以从业务层面来保证消息的顺序而不仅仅是依赖于消息系统。    

最后我们从源码角度分析RocketMQ怎么实现发送顺序消息。RocketMQ通过轮询所有队列的方式来确定消息被发送到哪一个队列（负载均衡策略），比如下面的示例中，订单号相同的消息会被先后发送到同一个队列中：    
```java
    // RocketMQ通过MessageQueueSelector中实现的算法来确定消息发送到哪一个队列上
    // RocketMQ默认提供了两种MessageQueueSelector实现：随机/Hash
    // 当然你可以根据业务实现自己的MessageQueueSelector来决定消息按照何种策略发送到消息队列中
    SendResult sendResult = producer.send(msg, new MessageQueueSelector() {
        @Override
        public MessageQueue select(List<MessageQueue> mqs, Message msg, Object arg) {
            Integer id = (Integer) arg;
            int index = id % mqs.size();
            return mqs.get(index);
        }
    }, orderId);
```    

在获取到路由信息后，会根据`MessageQueueSelector`实现的算法来选择一个队列，同一个OrderId获取到的肯定是同一个队列。    

```java
private SendResult send()  {
    // 获取topic路由信息
    TopicPublishInfo topicPublishInfo = this.tryToFindTopicPublishInfo(msg.getTopic());
    if (topicPublishInfo != null && topicPublishInfo.ok()) {
        MessageQueue mq = null;
        // 根据我们的算法，选择一个发送队列
        // 这里的arg = orderId
        mq = selector.select(topicPublishInfo.getMessageQueueList(), msg, arg);
        if (mq != null) {
            return this.sendKernelImpl(msg, mq, communicationMode, sendCallback, timeout);
        }
    }
}
```

### 2.消息重复    
造成消息重复的根本原因是：网络不可达。只要通过网络交换数据，就无法避免这个问题。所以解决这个问题的办法就是绕过这个问题，那么问题就变成了：如果消费端收到两条一样的消息，应该怎样处理？    
> 1.消费端处理消息的业务逻辑保持幂等性    
2.保证每条消息都有唯一编号且保证消息处理成功与去重表的日志同时出现    

第一条解决方案很好理解。第二条原理就是利用日志表来记录已经处理成功的消息ID，如果新到的消息已经在日志表中，就不再处理这条消息。    

第一条方案很明显是在消费端实现，不属于消息系统要实现的功能。第二条可以由消息系统实现，也可以由业务端实现。    

**RocketMQ不保证消息不重复，所以需要业务端去重**    

### 3.事务消息    
> **大事务 = 小事务 + 异步**    

同样的业务，在单机环境下和集群环境下耗时是不一样的，集群下耗时可能成倍于单机环境。为规避这个问题，可以将大事务拆分成多个小事务异步执行，这样基本上能够将跨机事务的执行效率优化到与单机一致。    

我们来看看RocketMQ是如何发送事务消息的。    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ-tx1.png?raw=true)    

RocketMQ第一阶段发送`Prepared消息`后，会拿到消息的地址，第二阶段执行本地事务，第三阶段通过第一阶段拿到的地址去访问消息，并修改消息的状态。    

如果`Prepared消息`发送失败了怎么办？RocketMQ会定期扫描消息集群中的事务消息，如果发现`Prepared消息`，它会向消息发送端（生产者）确认，Bob的钱到底是减了还是没减？如果减了是回滚还是继续发送确认消息？RocketMQ会根据发送端设置的策略来决定是回滚还是继续发送确认消息。这样就保证了消息发送与本地事务同时成功或同时失败。这个策略的确定是通过`TransactionCheckListener`接口由生产者确定的。    

再回到转账的例子，如果Bob的账户余额已经减少，且消息已经发送成功，Smith端开始消费这条消息，这个时候就会出现消费失败和消费超时两个问题，解决超时问题的思路就是一直重试，直到消费端消费消息成功，这个过程有可能会出现消息重复消费的问题，消费端做幂等即可。    
![](https://github.com/huhuics/Accumulate/blob/master/image/RocketMQ-tx2.png?raw=true)    

这样基本上可以解决消费端超时问题。如果消费端消费失败怎么办？阿里提供给我们的解决方法是：**人工解决**。如果因为消费失败，而要回滚整个流程，消息系统要实现这个回滚流程其复杂度将大大提升，且很容易出现bug，估计出现bug的概率会比消费失败的概率大很多，这也是RocketMQ目前暂时没有解决这个问题的原因。在设计消息系统时，我们要衡量是否值得花这么大的代价来解决一个出现概率非常低的问题。
























