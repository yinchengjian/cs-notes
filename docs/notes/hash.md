哈希函数hashcode()，哈希表

hash函数分流

hashmap:首先求出key的hashcode，然后%M得到桶的位置，每个桶后面都是一个红黑树。

## 设计RandomPool结构

填洞

可以等概率取值，

两个hashmap

每一个str进来都会有一个编号（依次增大，按顺序排的），然后random随机取一个，当删除一个之后，就将最后一个位置的str补到该位置。然后再随机取。

![1545486636289](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545486636289.png)

## 布隆过滤器

黑名单问题，100亿url都是黑名单，每个连接64字节，过滤

某个东西是否在里面，好的说成坏的。

bit类型的一个map

![1545488016945](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545488016945.png)

按位来描黑某一位，用基础类型来作为bit的数组

![1545488451430](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545488451430.png)

一个url用k个hash函数来求code，并且%m-1，把所有位置都描黑，然后下次判断url时，如果所有对应的code都是黑的，就是黑名单里的。

通过调节数组长度，以及k的个数，可以控制失误率

bit m的数值公式：p为预期失误率

![1545488833191](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545488833191.png)



![1545488964605](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545488964605.png)

k，m可能会向上取整。K，m都确定之后，P的值：

![1545489036278](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545489036278.png)



## 一致性哈希

服务器的设计

经典服务器抗压结构

前端都有一样的hash函数，分别对收到的数据求code并%3，放在后面的3个数据库中，下次取某一个数据，也是通过hash函数确定数据库取值，3个服务器特别负载均衡。

![1545489680153](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545489680153.png)

没法新增服务器，会导致以前的服务器上的数据不对了，之前%3，现在%100，数据要进行迁移。

一致性哈希可以减少代价。  **数据迁移代价很低**

用环：把hash的范围确定，将所有的服务器code传到前端，一个数据来之后，求出code，通过二分查找找到第一个比自己大的服务器code，存进去。

再加一个服务器，就将它前面的一个服务器的数据迁移。

![1545490415582](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545490415582.png)

问题：服务器比较少的时候，hash没法均分，负载不均衡。

​		即使刚开始均衡，在新加一个服务器，也会影响负载，因为之前的已经定了。

#### 虚拟节点技术解决两个问题（每个物理节点分1000个虚拟节点）

用虚拟节点（每一个有1000）去抢数据，抢到之后就分给自己所属的物理节点

#### 加机器如何数据迁移？

从它前面的虚拟节点迁移的，但还是物理节点完成的。

## 并查集 不能处理流结构

1.解决了非常快的查两个元素是否属于一个集合 issameset A:set1,B:set2

2.两个元素各自的集合合并在一起union(A,B)即(set1，set2)->set

代表节点一样就是一个节点

![1545573554311](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545573554311.png)

唯一优化（只要查某个元素的代表节点就会优化）：找某一个节点的代表节点，查找完成后结构被改写。该节点开始的路径上的节点都被打平，直接连接到代表节点

![1545573793540](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545573793540.png)

岛问题

感染函数有一个1，就感染附近

感染函数为递归，遇到越界或不等于1退出

## 现在矩阵特别大，要多cpu处理，矩阵切分

处理边界，每个岛都有自己的所属ABC，边界值也有，直接边界两两比较，都是1，就比较所属域，域相同，不用管，域不同，合并。（**并查集思想**）

![1545911151737](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545911151737.png)

## 前缀树

前缀相同，就复用，

![1545911966005](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1545911966005.png)

节点信息为该路径的字符串出现过几次，每个节点被划过多少次

可以解决：某字符串出现过几次，以及有多少字符串的前缀是它，

## 贪心（定优先级）

字典序最小的str数组拼接，比较器要有传递性，唯一性

str1+str2<str2+str1,谁小谁放前面

## 题目四

![1546171719277](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1546171719277.png)



哈夫曼编码问题

先将数组放到小根堆里，再从小根堆里拿出两个最小的，然后求出和，再放入小根堆里，循环。即可

一直找最小的，一直贪 结果是由子过程累加或累成求出来，都可以用哈夫曼编码

堆适合贪心算法





