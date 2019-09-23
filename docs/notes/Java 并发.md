# Java 并发

## 线程状态转换

当线程被创建出来，就进入了就绪状态，等待CPU时间片，CPU执行，当需要的资源是临界资源，并且被占用，只能进入阻塞状态，等待资源的释放，并且让出CPU，等资源空闲，再次进入就绪状态，等待CPU时间片，最终执行完成，进入销毁状态

## Java线程

Thread：抽象类，需要实现run方法

Runnable：接口，无返回值

Callable：接口，有返回值

Daemon：守护线程，是在后台提供服务的线程，当所有非守护线程都停止，程序就终结了，会杀死守护线程

Sleep：休眠当前线程

Yield：可以让出CPU

## 线程池

参数：coreSize，maxSize，blockQueue，abort policy

流程：任务来了，分配至coreSize，然后再来任务放置blockQueue，知道maxSize，之后的任务就会执行abort policy

## 中断

## 同步互斥

## J.U.C

## Java线程模型

## 线程安全

## 锁优化

自旋锁

偏向锁

轻量级锁



