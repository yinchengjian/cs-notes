## 线程使用

### Runnable

是一个接口，实现该接口可以新建一个线程，举个例子如下：

```java
public class runnableTest implements Runnable{
    @Override
    public void run(){
        
    }
}

public static void main(string[] args){
    runnableTest r = new runnableTest();
    Thread t = new Thread(r);
    t.start();
}
```

### Callable

接收泛型，并且返回该泛型结果

```java
public class callableTest implements Callable<Integer>{
    @Override
    public Integer call(){
        return 1;
    }
}

public static void main(string[] args){
    callableTest r = new callableTest();
    FutureTask<Integer> ft = new FutureTask<>(r);
    Thread t = new Thread(ft);
    t.start();
    ft.get();
}
```

### Thread

```java
public class MyThread extends Thread {
    public void run() {
        // ...
    }
}
public static void main(String[] args) {
    MyThread mt = new MyThread();
    mt.start();
}
```



## Executor框架

任务，任务的执行，异步的计算结果。

首先生成任务（runnable，callable），然后生成线程池，分配工作线程来执行任务，如是callable任务，可以通过future对象来接收任务的返回值。此结果可以异步获取。

![1548236613623](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548236613623.png)



![1548380988416](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548380988416.png)

#### 执行execute()方法和submit()方法的区别是什么呢？

1)execute()方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否；

2)submit()方法用于提交需要返回值的任务。线程池会返回一个future类型的对象，通过这个future对象可以判断任务是否执行成功，并且可以通过future的get()方法来获取返回值，get()方法会阻塞当前线程直到任务完成，而使用get（long timeout，TimeUnit unit）方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。

#### ThreadPoolExecutor

##### 实现方式



threadPoolExcutor是线程池实现类，所有线程池都可以通过此类来创建，例如Excutros工具类所生成的3种线程池newFixedThreadPool（固定线程池）,newCachedThreadPool（可缓存线程池）,newSigleThreadExcutor（单线程池）,都是通过new threadPoolExcutor()参数已经配好了。

```java
 /**
     * 用给定的初始参数创建一个新的ThreadPoolExecutor。

     * @param keepAliveTime 当线程池中的线程数量大于corePoolSize的时候，如果这时没有新的任务提交，
     *核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了keepAliveTime；
     * @param unit  keepAliveTime参数的时间单位
     * @param workQueue 等待队列，当任务提交时，如果线程池中的线程数量大于等于corePoolSize的时候，把该任务封装成一个Worker对象放入等待队列；
     * 
     * @param threadFactory 执行者创建新线程时使用的工厂
     * @param handler RejectedExecutionHandler类型的变量，表示线程池的饱和策略。
     * 如果阻塞队列满了并且没有空闲的线程，这时如果继续提交任务，就需要采取一种策略处理该任务。
     * 线程池提供了4种策略：
        1.AbortPolicy：直接抛出异常，这是默认策略；
        2.CallerRunsPolicy：用调用者所在的线程来执行任务；
        3.DiscardOldestPolicy：丢弃阻塞队列中靠最前的任务，并执行当前任务；
        4.DiscardPolicy：直接丢弃任务；
     */

public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

#### 线程池的拒绝策略

一共5种

- CallerRunsPolicy

- AbortPolicy

- DiscardPolicy

- DiscardOldestPolicy

- 自定义

  ![1548386593159](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548386593159.png)

#### newFixedThreadPool

提交一个任务就创建一个工作线程，

会创建一些初始线程池，然后申请就给，申请完了，就新建线程再分配，当达到上限，就要放在等待队列里，

#### newSingleThreadPool

只生成一个线程，用来完成任务。

#### newCachedThreadPool

是创建的每个线程都是有时延的，一定时间内没有被使用，就会被收回，在有任务，就会在创建工作线程，

例子：

```java
public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < 5; i++) {
        executorService.execute(new MyRunnable());
    }
    executorService.shutdown();
}
```

## 同步互斥

### synchronized



### AQS

是一个用来构建锁和同步器的框架。基于AQS可以简单高效地构造出自定义同步器和锁，例如ReentrantLock，Semaphore，CountDownLatch，CyclicBarrier，ReentrantReadWriteLock，SynchronousQueue，FutureTask等

#### 原理

核心思想是 维护了一个volatile共享变量state，通过CAS操作来改变该变量，来达到资源同步。该变量就是锁对象。

state为0时，表示未锁定状态，可以将当前请求的线程设置为有效的工作线程，并且state+1，处于锁定状态，如果再被请求，就会被挂起，AQS会维护一个虚拟双向队列，只是Node可以有pre，next，并不存在真正的队列，来实现锁的分配。

#### 共享方式

EXclusive（独占）：公平锁，非公平锁

Share（共享）：CountDownLatch，Semaphore

#### 自定义同步器

按我的理解，只需要将AQS类中的protected方法进行相应的实现即可。分为独占式，共享式

```java
isHeldExclusively()//该线程是否正在独占资源。只有用到condition才需要去实现它。
tryAcquire(int)//独占方式。尝试获取资源，成功则返回true，失败则返回false。
tryRelease(int)//独占方式。尝试释放资源，成功则返回true，失败则返回false。
tryAcquireShared(int)//共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
tryReleaseShared(int)//共享方式。尝试释放资源，成功则返回true，失败则返回false。
```

#### ReentrantLock实现

首先内部定义了一个内部抽象类来继承AQS并实现部分方法tryRelease(),isHeldExclusively()以达到统一接口，然后有定义两个实现类，公平锁，非公平锁，分别覆写剩下的抽象方法tryRequire(),，来达到目的。

#### 执行流程

*ReentranLock.lock()->syn.lock->nofairSyn.acquire()->AQS.acquire()->nofairSyn.tryAcquire()*

tryAcquire()方法：是用来尝试获取state，CAS改变state变量的，或者已经获取state，再加1.

```java
protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
```

#### newCondition

该函数是syn实现的，就是生成一个conditionOebject对象。

```java
final ConditionObject newCondition() {
     return new ConditionObject();
}
```

可以生成condition对象，用来等待通知的。

### CountDownLatch

同步器而已，只要就是多个线程协同工作，每个线程完成任务，就执行countdown()方法，使state-1，最后主线程通过await()方法尝试获取锁，当state为0时，主线程即可成功获取，并执行后续操作。

![1548323943013](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548323943013.png)

```java
public class CountdownLatchExample {

    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 10;
        CountDownLatch countDownLatch = new CountDownLatch(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("run..");
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        System.out.println("end");
        executorService.shutdown();
    }
}

```



![1548323927508](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548323927508.png)



他是一个抽象类,定义所有锁的抽象方法，主要是通过共享变量state来实现，

```java
private volatile int state;//共享变量，使用volatile修饰保证线程可见性
//返回同步状态的当前值
protected final int getState() {  
        return state;
}
 // 设置同步状态的值
protected final void setState(int newState) { 
        state = newState;
}
//原子地（CAS操作）将同步状态值设置为给定值update如果当前同步状态的值等于expect（期望值）
protected final boolean compareAndSetState(int expect, int update) {
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```

### ReenTrantLock

是一种可重入锁，也是一种排他锁。该锁有一个静态抽象类继承自AQS，主是为了将后面的公平锁和非公平锁抽象出来，用来统一实现函数。在该类初始化的时候，会指定syn的真实对象，然后在相应的锁中实现对应方法。

### 比较

## 线程间协作

### join

### wait(),notify()

### await(),signal()

## JUC

### CountdownLatch

### CyclicBarrier

### Semaphore

### FutureTask

### BlockingQueue

## java内存模型

### 原子性

### 可见性

### 有序性

## 线程安全

### CAS

### Automicinteger

### ABA

## 无同步

### 栈封闭

### 线程本地存储（ThreadLocal）

### 可重入代码

## 锁优化

### 自旋锁

### 锁消除

### 锁粗化

### 偏向锁

### 轻量级锁

4种状态：无锁状态，偏向所状态，轻度所状态，重量所状态。

synchronized

首先当同时获取一个锁资源时，会将线程存放在一个阻塞队列中，队列中的每个线程



synchronized实现
contention list 存放请求锁的线程队列
entry list 候选线程队列（并发高了之后，contention队列会被频繁访问，所以移除一部分线程到entrylist中作为候选竞争线程）
wait set
onDeck
owner
!owner
执行流程：
  每个请求锁资源的线程倒要先放在contention队列中，之后会从该队列找出一部分放到entrylist中，作为候选，然后当某一个线程被标记为ondeck，就会获取竞争锁的权利，参与锁竞争。

JDK1.5之前（重量级锁）
   一个线程a过来了，要获取共享资源，首先要获取锁对象，但是此时锁被别的线程b获取了，a不会立马进入到等待状态，他会首先进行一定时间（一般都是一个线程上下文撤换的时间）的自旋，就是相当于直接获取到锁的竞争权利，（可以看出synchronized是不公平锁，至少对于已经在等待队列的线程很不公平，他并没有获取竞争锁的资格），该段时间如果没有获取到锁，就会进入contention list中，然后该队列会按照一定的顺序来确定哪个线程获得竞争锁的权利。当获取竞争锁的权利之后，可以和当前拥有锁的线程共同竞争锁的使用权，谁抢到是谁的。如果在获取锁的情况下，进入了阻塞状态就需要放弃锁，重新排队。

JDK1.6

偏向锁的用途：
    举个例子：我们十有八九在没有竞争的情况下使用了Vector，但是却需要一直不停地加锁解锁，资源代价消耗过大，如果使用了偏向锁，代价就小了。他仅仅只需要测试一下偏向所是否偏向自己就行，代价比较加锁解锁小地多。
    但是又会有偏向锁的撤销代价比较高需要进入safepoint，即stw，如果竞争太多的话，直接一开始就关闭更好。

偏向锁执行流程：
1. 检查锁对象markword偏向锁标识是否为1，锁标志位是否为01，确认为可偏向状态。
2.可偏向，则测试线程id是否为当前线程，如果是，进入5，不是，进入3
3.通过cas竞争锁,竞争成功,修改maekword的进程id,不成功,进入4
4.cas获取锁失败,表示有竞争,当到达安全点,就会把获取到偏向锁的线程挂起,偏向锁升级为轻量级锁,比那个且释放偏向锁,(改变锁标志位为00),进入轻量级锁执行流程.
5.执行同步代码.

轻量级锁:
就是将markword拷贝到线程的锁记录中,并且修改object的markword指向锁记录的copy,然后获取锁对象,并且一直cas竞争锁,竞争不到就升级.




synchronized的执行过程： 
1. 检测Mark Word里面是不是当前线程的ID，如果是，表示当前线程处于偏向锁 
2. 如果不是，则使用CAS将当前线程的ID替换Mard Word，如果成功则表示当前线程获得偏向锁，置偏向标志位1 
3. 如果失败，则说明发生竞争，撤销偏向锁，进而升级为轻量级锁。 
4. 当前线程使用CAS将对象头的Mark Word替换为锁记录指针，如果成功，当前线程获得锁 
5. 如果失败，表示其他线程竞争锁，当前线程便尝试使用自旋来获取锁。 
6. 如果自旋成功则依然处于轻量级状态。 
7. 如果自旋失败，则升级为重量级锁。



## 实现AQS的类

首先调用lock，然后调用syn.lock，tryacquires(1)，

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

首先回去尝试获取同步状态，如果获取失败，再将该线程任务添加到同步队列中，并且阻塞它。

首先去获取同步状态，如果不成功，将自己加到同步队列中，在尝试获取，他会一直循环，来看他的前驱结点是否为head节点，因为head节点就是已经获取同步状态成功的节点，然后在尝试获取一次同步状态。

```java
private Node addWaiter(Node mode) {
    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    enq(node);
    return node;
}
```



```java
private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) { // Must initialize
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```



```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

首先获取同步状态，失败，就加入到同步队列中，首先是创建一个node节点，然后尝试加到尾部，通过cas，如果队列没人就通过cas放到head，但是有可能两个方法切换时，又有了head，所以在去cas到tail，加入队列之后，会调用acquirequeue 一直循环来获取同步状态，但是不是那种特别大消耗的，

就是说他在队列中来cas获取同步状态时，会判断自己的前驱结点是不是head，如果不是就不用去尝试获取acquire，直接在下面的if中就会直接让自己进入等待状态。

AQS



# countdownlatch

tryacquires(){

​	return getstate==0?1:-1;

}

if(tryacquires()<0){

​	doacquireshared();

}

最开始state为10，然后获取时并没有任何对state的修改，只是看是否>0,并且前驱结点是否为head，满足这两个条件就可以直接获取到同步信息，只要是变成head就会执行下面的操作，之后在释放时，或减少state-1。

conuntdownlatch.await()就会进入到一个尝试获取同步状态的队列中，然后只有当队列中没人，并且stat大于0，才能退出循环，

tryAcquireShared>0是说stat已经变为0了，也就是获取同步状态了,自旋结束。

## reentrantlock

首先reentrantlock每个同步器会有两个队列，一个同步队列，另一个是多个等待队列，首先每个线程来了之后首先会获取lock，调用链为lock->acquire->tryacquire->cas,addqueued(addwaiter(),arg),

上述过程为首先会尝试获取锁，如果失败，就将进入tryacquire中，在尝试获取锁，如果失败，就将该线程封装在node节点中添加到同步队列中，同步队列中的node会一直自旋，来检查自己的前驱结点是否为head，如果是就尝试获取锁，获取成功后，就将自己设置为head，前节点已出队列。

当该节点获取锁之后，内部调用了await之后，就会将该节点封装到另一个节点中，加入到等待队列，并且释放同步状态，被后继节点移除同步队列，然后一直自旋，等待自己再次被加入到同步队列，否则就一直等待，

当有其他线程获取锁，并且执行signal之后，就会找等待队列的头节点，改变其状态，并调用enq()添加到同步队列，并且通过中断唤醒线程，该线程被唤醒之后，会继续执行await内的方法，一直自旋获取同步状态，直到成功为止，然后再执行该线程后续方法。

## 读写锁

state高位为写锁，低位为读锁。



# 阻塞队列

内部有一个reentrantlock，并且有两个codition，notfull，nnotempty，用来解决同步添加删除操作的。



# executor

线程池参数

基本大小，最大数量，阻塞队列，丢弃策略，线程工厂

## redis持久化

rdb，在进行rdb的时候，会fork一个子进程，然后父进程用来处理请求，子进程用来写入磁盘，当全部都写完之后就会用该临时文件替换rdb文件，完成持久化操作。

写时复制策略父进程可能正在修改数据，此时会复制一份让紫禁城不受影响。

aof是将redis的写命令都记录下里，然后带系统重启在一一执行，

冗余太大怎么办？

当前aof文件超过上次aof大小的百分之多少才会重写，重写的时候只会记录与上次系统重启之后发生的改变。

## redis5种数据结构

string，

hash（对象），hset  car price 500

list，双向链表 左右都可以进出，放最新的消息rpush num 2 3 放入2和3

set，sdiff set1 set2 可以求出交并差集

sortedset zadd key score member score memeber 通过分数作为hash索引，数组+链表

## rabbitmq怎么保证生产者到mq的可靠传输？

1.事务，2.confirm 会回传一个ack值，来确认已经正确接收到了那条消息，生产者会把未被确认的消息缓存到一个数据结构中，等待后续的重传。



## LinkedHashMap

多出了四个指针，head，tail，before，after，

前两个用来表示链表的头和尾的，后两个用来连接整个链表的。循环双向链表。

首先构造的是就headtail在一起，之后每来一个就放在双向链表的尾部，每次访问也将该node移到尾部。

# 数据库

## 高性能索引

一般都是优化之前的索引而不是创建新的索引

独立的列

前缀索引

多列索引

选择合适的索引列顺序

聚簇索引

覆盖索引

索引扫描来排序

压缩索引

荣誉和重复索引

未使用的索引

