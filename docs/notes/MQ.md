## 为什么使用消息队列？

解耦，异步，削峰



### 解耦

首先没有mq的场景特别麻烦

![1550757474115](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1550757474115.png)

每次不同的系统上线都有可能会去修改部分系统已达到数据传递的功能，而且消息源还需要去考虑每个接收者的情况，挂了怎么办，访问超时怎么办

### 异步

延时比较高，因为他会调用其它系统接口，导致每个耗时加在一起比较大，

### 削峰

秒杀每秒请求5000sql，mysql被打死，系统崩溃。但是低谷期正常每秒50请求，毫无压力。

![1550797822386](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1550797822386.png)

## mq有什么优点和缺点？

优点：解耦，异步，削峰。

缺点：系统可用性降低，系统复杂度提高，一致性问题（有某一个子系统在mq获取数据之后执行失败了，但是用户获取到的是成功，结果不一致。）

问题：

- 重复数据，
- 数据缺失，
- 数据顺序变乱，
- 数据无人消费，mq积压大量数据，磁盘快满了。

## kafka，activemq，rabbitmq，rocketmq都有什么优缺点？

activemq偶尔丢消息，维护少，社区活跃度低。用于小规模吞吐量。主要用于解耦和异步。

rabbitmq erlang语言开发，性能很好，延时低，即使消息从写进mq到消费出来时间比较小。后台管理界面很棒。erlang没人精通。

rocketmq 10w级 topic几百几千，对性能影响比较小。支持分布式

kafka吞吐量高，易扩展，适合大数据实时计算，日志采集。

### 如何保证消息队列的高可用？

### 如何保证消息不被重复消费？

保证幂等性？

一个数据重复来了很多次，要保证数据库中的记录不会发生改变，不会出错。

### 如何保证消息可靠性传输？

生产者到mq的过程中丢失了，

1.没到，

2.到了内部出错，没保存

3.mq收到消息了，暂存在内存中，但是自己挂了，数据就没了

4.消费者拿到了数据，但是没有来得及处理，自己挂了。

mq丢数据，消费时丢数据。



##### 生产者搞丢了，没发到mq，

1.rabbitmq有事务功能，就像数据库一样，出错之后，可以回滚，既可以重复发送。

问题：事务机制是同步的，生产者发送消息会同步阻塞等待运行结束。耗时大。

2.confirm机制，把channel设置为confirm模式，发消息之后就不用管了，如果接受失败，就会通知你重发。成功或者不成功都会回调相应的函数来实现相应的功能，如通知接收成功，通知接收失败，重新发送。

#### rabbitmq自己搞丢了数据？

消息要持久化到磁盘上，首先queue设置为持久化，然后设置消息的deliveryMode为2，持久化消息。

#### 消费者搞丢数据？

问题：打开消费者autoack机制，没消费完就发送确认信息，然后消费者挂了，

关闭autoack，自己处理完消息，发送ack。

#### 保证消息的顺序性？

mysql biblog，增删改记录的日志，要数据同步。顺序不能乱。

将需要保存顺序性的消息放在同一个队列中，然后消费者一次消费即可。

#### 消息队列积压？延时过期失效，满了怎么办

一个时将之前的mq通过一个临时消费者写入到临时的大的mq中，就可以暂缓压力，

#### 开发消息中间件，怎么设计架构？

可扩展，持久化，高可用（节点挂了，在选一个leader），0丢失





## rabbitmq

exchange，queue，routing key,binding key,(分别为本次消息目标key，由exchange到queue的key（一个queue可以有多个）)

exchange:

direct（routingkey==bingdingkey）,topic(*,#),fanout(all),header

exchangeDeclare(name,type,durable,autodelete,internal,argument)

queuebing()

channel.basicPublish(exchangename,routingkey,mandatory, ,messagebodybyte)



![1552042875732](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1552042875732.png)

