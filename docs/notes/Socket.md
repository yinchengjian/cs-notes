# Socket

## IO模型

IO流程：

- 等待数据流写入内核缓冲区
- 将数据从内核写入用户缓冲区

首先等待网络数据的传输，当所有数据准备完全，数据就到达了内核缓冲区，再将数据冲内核写入用户缓冲区

## IO五大模型

阻塞IO：revfrom系统调用访问内核数据是否到达，到达开始copy，copy完成结束

非阻塞IO：revfrom系统调用数据是否准备完成，没有就返回，过一段时间在询问，

IO多路复用：select线程一直轮询所有连接，知道某个连接数据准备妥当，执行revfrom，最后返回，在重新轮询

信号驱动IO：signaction系统调用，立即返回，数据准备妥当，内核发送信号给用户，用户执行revfrom获取数据

异步IO：aio-read系统调用，立即返回，待到数据copy至用户缓冲区，发送信号给用户

## IO多路复用

select/poll/epoll

### select

文件描述符：fd_set类型数组，有大小限制，每次轮询，都会把文件描述符从用户态copy到内核态

### poll

文件描述符：pollfd类型数组，大小无限制，可以有更多事件类型，并且可以重复利用pollfd，可以修改pollfd

### epoll

文件描述符epoll-ctl会把fd复制到内核态，并且存储在内核态的红黑树中，并且可以改变fd状态