# IO 模型

在开始介绍IO模式之前，有一些基础的概念需要先解释下



## 同步、异步、阻塞、非阻塞

### 同步vs异步

关注的是消息通信机制

- 同步是在发出一个调用时，在没有得到结果之前，该调用就不返回，但是一旦调用返回，就可以直接获取结果，也就是说调用方在主动等待这个调用的结果
- 异步则是相反，在调用之后立即返回，但是没有返回结果，然后被调用者之后通过状态，通知来告诉调用者，或者通过回调函数来处理这个调用

### 阻塞vs非阻塞

关注的是线程在等待调用返回时的状态

- 阻塞就是在调用返回之前被挂起，无法进行后续操作，之后该线程得到了调用结果才会返回
- 非阻塞是指在调用结果返回之前，当前线程可以执行后续操作，而不会被挂起

###  用户空间vs内核空间

操作系统将内存空间分为两部分，一部分为内核空间，另一部分为用户空间。内核空间存储的代码和数据拥有更高的权限，内存访问的相关硬件会在程序执行的时候进行访问控制，是的用户程序不能直接读写内存空间的内存。

### 进程切换

- 当一个进程在运行时，中断和系统调用都会使CPU的控制权从当前进程转移到操作系统内核
- 进程切换时，操作系统内核会保留被切换的进程的上下文，然后取出另一个进程的上下文，并且把CPU的控制权转让给这个进程。

### 系统调用

系统调用就是应用程序调用操作系统提供的接口来实现某些操作，比如硬盘，网络接口设备的读写

### 进程阻塞

当进程发起一个系统调用时，这个调用没有立即返回，而是等待了一段时间后才能返回结果，而这个时候内就会将该进程挂起，确保它不会占用CPU资源

磁盘I/O与网络I/O

客户端请求到达网卡，之后数据进入内核缓冲区，再从内核缓冲区复制到用户缓冲区

## 5种I/O模型

阻塞IO

非阻塞IO

IO多路复用

信号驱动式IO

异步IO

### 阻塞IO

应用程序进行recvfrom系统调用时，会被阻塞，直到数据返回并且复制到用户程序的缓冲区才会返回

 ![img](https://user-images.githubusercontent.com/16413289/57979103-62309e00-7a4b-11e9-8d6a-75b569092be4.png) 

IO的两个阶段：

数据准备，很多时间数据在一开始并没有全部到达，这个时候就需要内核等待足够的数据到来

数据复制，当数据都准备好了之后，就需要把数据复制到用户缓冲区中

### 非阻塞IO

如图所示，当用户程序调用recvfrom系统调用时，如果数据未准备好，就会返回错误信息，这样程序就不会阻塞

 ![img](https://user-images.githubusercontent.com/16413289/57979508-1aad1080-7a51-11e9-8185-a02cbf5df4b5.png) 



需要频繁地轮询状态，并且在数据复制阶段还是会阻塞

### IO多路复用

应用程序会先调用select/poll/epoll系统函数等待某个连接变得可读，在调用recvfrom从连接上读取数据，其实该种模型在两个阶段都会block，但是与众不同的是第一步阻塞是在等待多个连接上有读写时间发生，明显提高了效率。

![img](https://user-images.githubusercontent.com/16413289/57979498-079a4080-7a51-11e9-9843-88d8a8c18d14.png) 



select：存储文件描述符的数组是有限的，1024个，并且每次都需要把文件描述符复制到内核空间，timeout参数进度1ns

poll：文件描述符可以重复利用，并且支持更多的事件类型，但是通用性不太强，timeout参数精度1ms

epoll：在内核缓冲区维护了一个红黑树，用来保存文件描述符，两种工作模式，一种是事件到达之后通知应用程序，程序可以不应答，下次在通过epoll_wait在通知进程，另一种是只通知一次，之后不再提醒

### 信号驱动式IO

应用程序负责创建sigaction信号处理程序，然后返回，等待数据准备好，内核会向用户程序发送信号，用户程序再通过recvfrom来获取数据

 ![img](https://user-images.githubusercontent.com/16413289/57979514-2c8eb380-7a51-11e9-8e66-b038f44def80.png) 



第一个信号没处理完， 下面的会被阻塞

### 异步IO

aio_read告诉内核启动某个操作，并且在操作完成之后通知用户程序，包括数据的复制

 ![img](https://user-images.githubusercontent.com/16413289/57979574-50062e00-7a52-11e9-8d58-758167d330df.png) 



系统比较复杂，并且window不支持，linux下不够完善
