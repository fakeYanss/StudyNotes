# 图解TCP协议中的三次握手和四次挥手

建立TCP需要三次握手才能建立，而断开连接则需要四次握手。整个过程如下图所示：

![img](http://upload-images.jianshu.io/upload_images/1641067-cfbdc82ef9f5c5c0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/546)

1.jpg

 

先来看看如何建立连接的：

![img](http://upload-images.jianshu.io/upload_images/1641067-8d52ca990ffbee0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

2.png

首先Client端发送连接请求报文，Server段接受连接后回复ACK报文，并为这次连接分配资源。Client端接收到ACK报文后也向Server段发送报文，并分配资源，这样TCP连接就建立了。

如何断开连接呢？简单的过程如下：

![img](http://upload-images.jianshu.io/upload_images/1641067-5ed8bf6c24244b4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

3.png

**注意中断连接端可以是Client端，也可以是Server端**

假设Client端发起中断请求，也就是发送FIN报文。Server端接到FIN报文后，意思是说“我client端要发给你了”，但是如果你还没有数据要发送完成，则不必急着关闭Socket，可以继续发送数据。所以所以你先发送ACK，"告诉Client端，你的请求我收到了，但是我还没准备好，请继续你等我的消息"。这个时候Client端就进入FIN_WAIT状态，继续等待Server端的FIN报文。当Server端确定数据已发送完成，则向Client端发送FIN报文，"告诉Client端，好了，我这边数据发完了，准备好关闭连接了"。Client端收到FIN报文后，"就知道可以关闭连接了，但是他还是不相信网络，怕Server端不知道要关闭，所以发送ACK后进入TIME_WAIT状态，如果Server端没有收到ACK则可以重传。“，Server端收到ACK后，"就知道可以断开连接了"。Client端等待了2MSL后依然没有收到回复，则证明Server端已正常关闭，那好，我Client端也可以关闭连接了。Ok，TCP连接就这样关闭了！

整个过程Client端所经历的状态如下：

![img](http://upload-images.jianshu.io/upload_images/1641067-71248ccd102db6f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

4.png

而Server端所经历的过程如下：

![img](http://upload-images.jianshu.io/upload_images/1641067-331cd0ae6ee641c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/529)

5.png

**注意在TIME_WAIT状态中，如果TCP client端最后一次发送的ACK丢失了，它将重新发送。TIME_WAIT状态中所需要的时间是依赖于实现方法的。典型的值为30秒、1分钟和2分钟。等待之后连接正式关闭，并且所有的资源(包括端口号)都被释放。**

1. 为什么连接的时候是三次握手，关闭的时候却是四次握手？

   因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，"你发的FIN报文我收到了"。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。

2. 为什么TIME_WAIT状态需要经过2MSL(最大报文段生存时间)才能返回到CLOSE状态？

   * 为了保证发送的最后一个ACK报文段能够到达B
   * 防止“已失效的连接请求报文段”出现在本连接中。在发送完最后一个ACK报文段后，再经过实践2MSL，就可以使本连接持续的时间内所产生的所有报文段，都从网络中消失。这样就可以使下一个新的连接中不会出现这种就得连接请求报文段。

### TCP有限状态机

TCP有限状态机如下图：

* 粗实箭头表示客户进程的正常迁移
* 粗虚线箭头表示对服务器进程的正常迁移
* 细线箭头表示异常变迁

![img](http://upload-images.jianshu.io/upload_images/1641067-133b96a90ee97aa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

6.png

 

 

 

 

 