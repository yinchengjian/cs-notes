# dubbo-mesh改造历程

## 开始之前

​		拿到手的第一步，要把项目跑起来，因为我是后来才知道这个比赛的，所以已经没法报名，用阿里的机子来跑，不过还好我拿到全部项目，一个mesh-agent，两个jar包，以及一堆启动参数，但是自我感觉jar的方式来调试，比较麻烦，所以我按照jar包里的代码重写了两个module，这样清爽多了。

## 项目的整体流程

​		consumer是一个基于springboot的web服务，提供一个接口/invoke，用户访问该接口，会随机生成一个0-1024之内的数字，通过异步http传输到consumer-agent，之后consumer-agent在通过http传输到provider-agent，provider-agent通过阻塞的netty和provider进行通信，最后在provider上获取这个数字的hashcode，然后再传输给consumer，最后传输给用户

## 改造之旅

1.provider-agent和consumer-agent之间通过异步HTTP通信

2.负载均衡算法，加权随机轮询，每台机子的启动参数不一样，性能也会有所不同

3.所有通信都改为netty，除了provider和consumer，但是对于一个菜鸡，我根本不会，花了两天时间看了netty实战，感觉这本书写的挺好的，挺适合入门者，看完之后，就开始试着改一改，刚开始写的时候，不知道要怎么写，后面花了点时间梳理了下，后来发现consumer-agent和provider-agent都分别同时做为服务器和客户端，所以每一个都要实现server和agent。最后花了3天时间把基础版写出来了，兴冲冲地跑压测，发现了第一个问题，notConnectedException这个bug，这个bug是因为连接还没完成就开始写数据，之后改了一版，之后还有新的一轮问题，就是对于每一次传输数据的加解密，因为netty只支持byteBuf的格式传输，所以我们在进出站的时候要进行相应的加解密，不然netty识别不了，第一步，把consumer发过来的数据给解密，用的是httpRequestDecoder，之后发现需要给provider发送rpcRequest，我就决定在consumer-agent这层就直接不用http了，这里我把httpRequest转为了RpcRequest，在出站的时候，这时的出站是通过conusmer-client来发送数据到provider-server，然后在RpcRequestEncoder，传输到了provider-server，在provider-server也是一样，要进行入站的decoder，之后在把入站数据通过provider-client传输给provider，之后在把数据传输回去。

这里遇到的问题：主要是对于decoder，encoder的编写，有点难度，其实就是如何让之前的数据还通过来时的路传输回去，这里我是client持有server的channel完成的，这里就又埋下了一个问题，

这里的handler都梳理完了之后，我认为代码是没问题的，就压测一下，出现了另一个问题，就是bytebuf的数据溢出了，就是每次传输的数据是有限的，当传送的数据过大，就会应该分开来传，报的错是byteBuf：readIndex+len>writeIndex，这个问题我还没有解决，但是我已经有思路了，还记得上面说到的埋雷，林一个问题，发现dubbo连接池满了，这里发现我每一个连接都会新建一个channel，以及一个新的client，这就需要我把之前写的基础版代码全部重构，这里梳理了一个新的思路

consumer->consumer-server:每一个连接一个channel，但是只需要几个eventLoop来执行

consumer-server->consumer-client：这里就没有所谓的连接，数据都是直接获取的，然后可以只写一个channel用来传数据到

consumer-client->provider-server：这里只有一个连接，

provider-client->provider：这里也只有一个连接，

最重要的是需要最后传输给consumer-server的数据要和之前发送的channel对应起来，不然会出现channel1接收到了应该是channel2的数据，那么给consumer的数据就出错了

这里其实原有代码里就有提示，request.id以及rpcResponse的id就是用来区别数据的。

以上就是我到目前为止所有的改造，谢谢，太累了，歇一会，主要是手累，呜呜呜

0908进度：

代码跑通了，就是按照上面的想法实现的，但是出现了个新问题，就是发送消息会过大，导致加解密失败，

还有就是发送消息队列堆积，导致内存泄露，连接没有及时释放，OOM，原因就是加解密处理不当