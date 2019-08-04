分布式缓存

缓存如何使用的，使用不当的后果

为什么用缓存，不用行不行，用了之后可能会有什么不好的后果

#### 为什么用？

高性能，高并发

耗时太高

高并发容易出错。

秒杀页面的菜有很多，一天中被浏览100w次。每次查询2秒。

每次查询同一数据都是相同时间，

加上缓存，每次改数据库时，也更新缓存。

为什么缓存可以支持高并发，缓存是走内存，比较强。

高并发（3/4在缓存，1/4在db）

#### 缓存问题：

缓存同步（双写不一致），缓存雪崩，缓存穿透，缓存并发竞争。

#### redis和memcached区别

redis数据类型比较丰富，redis有原生支持cluster集群。

#### redis线程模型

文件事件处理器

单线程nio

#### redis是单线程为什么还可以支撑高并发？

线程模型

#### redis哪些数据类型，分别那些场景使用比较有用？

string(普通的getset，kv缓存) 

hash 

key=150

value={

​	'id':150,

​	'name':"zhangsan"

}

hash用来存对象，后续可以只改某一个字段。

list 

有序列表，微博粉丝。

key=da v

value=[zangshan,lisi,wangwu]

lrange命令可以指定读取范围，

set

无序去重。交集，并集，差集。共同好友

sortset

排序的set，可以排序。进去可以给分数，按分数排序，

zadd board score username

zrange board 0 3

zrank board zhangsan

#### 过期策略

缓存放不下太多东西，是基于内存，内存是有限的。

redis过期时间已经到了，但是内存还是被占用了

定期删除+惰性删除

随机抽取一些key删除，当你查到某个key，发现有过期时间，并且过期了，就会删除他。

如果太多过期key没有被删除，内存快要耗尽了。有自己的内存淘汰机制：

noeviction

allkeys-lru:移除掉最近最少未使用的key  

random

volatile-lru：过期时间的key中删

#### 如何保证redis高并发高可用？主从复制原理介绍下，哨兵原理介绍下？



写多读少用异步，消息中间件

一般都是读写分离，读高并发，水平扩容增加redis salve

主从机构->读写分离->高并发

#### redis replication

断点复制，



redis缓存雪崩和缓存穿透，

