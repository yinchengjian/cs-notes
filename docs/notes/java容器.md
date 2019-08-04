# java容器

## Collection

![1548235808830](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548235808830.png)

### Set

### List

### Queue

## Map

![1548235826935](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548235826935.png)

## ArrayList

### 1.概览

### 2.扩容

### 3.删除元素

### 4.Fail-Fast

### 5.序列化

## Vector

### 1.同步

### 2.与ArrayList的对比

### 3.替代方案

## CopyOnWriteArrayList

### 读写分离

### 使用场景

## HahsMap

### 1.存储结构

### 2.拉链发工作原理

### 3.put

### 4.确定桶下标

### 5.扩容-基本原理

最开始就是重新设置容量，阈值，容量就是直接*2，阈值还是通过容量*因子。

接下来就是进行node的转移，第一步先生成一个新数组newNode，会去判断每个原数组上的位置是否有node，如果有并且只有一个（没有后面的链表），就直接移到新数组对应的位置（用hash&newCap-1确定数组下标）。

2。是链表，就需要两条链表，因为当数组扩容之后，原下标上的链表的新下标会不一样，一种等于原hash，另一种hash+oldCap，因为下标值的计算为（hash&（newCap-1）），所以通过判断hash&newCap是否为0，来确定是否为新下标，因为newCap为2的幂，所以是00010000这种形式，所以和hash与之后，如果为0，就是原下标，为1，就是新下标。



### 6.扩容-从新计算桶下标

### 7.计算数组容量

### 8.链表转红黑树

### 9.与HashTable的比较

## ConcurrentHahsMap

### 1.存储结构

### 2.size操作

### 3.jdk 1.8 改动

