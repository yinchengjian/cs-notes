# MYSQL

## B+ Tree原理

是一种平衡树，所有叶子节点上保存的是数据，并且都在同一层，通过指针连接在一起，支持顺序访问

## 聚簇索引

就是索引和数据保存在同一个结构中，叶子节点存储数据行

## B+ Tree的优点

和其他平衡树相比，更加扁平，因为出度更大，树的高度会小很多，平均查找性能比较高

他把数据放在叶子节点中，叶子节点和内存的一页一样，所以每次都会读取一整个页，对于顺序访问更加友好

## MYSQL索引

B+Tree索引

不必全局遍历，只需搜索即可，可以用于分组和排序

哈希索引

全文索引

空间数据索引

## 存储引擎比较

MYISAM：不支持事务，不支持行级锁

INNODB：自适应哈希索引，可预测读，热备份，标准级别：可重复读，通过MVCC和NEXT-key lock来防止幻读

## 索引优化

独立的列：索引单独放在条件的一端

多列索引：查询条件变成多列索引比多个单列索引要好

索引列的顺序：要建立区分度高的索引，并且放在最前面，便于快速查找到数据

前缀索引：对于比较大的列值，更加节省空间

覆盖索引：避免回表查询

## 索引优点

减少扫描数据行

避免服务器在进行分组排序，以及创建临时表

把随机IO变成了顺序IO

## 索引使用条件

一般是中型数据库使用，大型的数据库用分区

## 使用Explain进行分析

通过explain来查看select的查询情况，需要注意的select_type，key，row

## 优化数据访问

减少列的返回，减少行的返回，缓存，覆盖索引

## 重构查询方式

切分大查询，分解连接插叙

## 切分

水平切分，垂直切分

## 复制

主从复制，读写分离

有3个线程，一个写日志，一个主从同步，一个从节点重现

