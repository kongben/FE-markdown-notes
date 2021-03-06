# HTTP通信性能优化历史

> HTTP/1.0 --->HTTP/1.1

为了提升通信效率，HTTP/1.1新增了两个提升功能分别时持久化连接和管线化。

### 持久化连接

在HTTP/1.0时代，每次HTTP通信在请求页面时，如果有非常多的资源（js、img）必须通过TCP协议不停的握手挥手,我们现在称之为**非持久连接**。

![](https://user-gold-cdn.xitu.io/2020/3/19/170ef65f5dff071b?w=251&h=451&f=png&s=15078)

那么对应的**持久连接**就是在一个HTTP通信结束后，不关闭TCP连接，而是一直保持该连接，这样就可以在一个TCP连接中发送多个HTTP请求获取资源，极大的减少TCP通信时间，提升效率。

![](https://user-gold-cdn.xitu.io/2020/3/19/170ef65b4d67dd5a?w=299&h=409&f=png&s=11899)

### 管线化

管线化是在持久连接的基础上再次提升通信效率的方法。将一个TCP上的多个HTTP请求，不再以请求-响应顺次进行，上次请求得到响应后，才能发送下次请求。管线化技术就是**客户端在收到响应之前就顺次发送其他请求**。可以在较高时延的情况下，提升通信效率。

![](https://user-gold-cdn.xitu.io/2020/3/19/170ef655036ba501?w=240&h=253&f=png&s=7714)

而目前为止，管线化用的比较少，管线化的限制在于

+ 因为HTTP报文没有序列号，响应必须按照请求发过来的顺序，进行发送，如果顺序错乱，客户端就没办法匹配。
+ 必须使用keep-alive。
+ 不能使用诸如post这样对数据有副作用的请求方式。
+ 客户端必须做好连接会在任意时刻关闭的准备，还要准备好重发所有未完成的管道化请求。
+ 会产生head of line blocking。即前一个请求遇到了阻塞，就算后面请求已经处理完毕也需要等待前一个请求完成，才能发送所有响应，浪费多余时间。

> HTTP/1.1 --->HTTP/2.0

虽然HTTP/1.x解决了tcp复用问题，但是在多个HTTP请求中，会出现阻塞。若不使用管线化，则还是一个请求一个响应，效率还是比较低。所以新增了多路复用、HTTP头压缩、二进制传输。

### 多路复用

绕过HTTP/1.1管线化可能出现的**阻塞机制**

流

在HTTP/2.0中，HTTP通信不是按照请求-响应形式来实现的，而是通过虚拟通道-**流（stream）**。每一个完整的通信都叫做一个数据流。

+ 有序，每一个流都有特定ID。奇数为客户端发送，偶数为服务端发送
+ 双向，同一个流内，可以接受和发送消息。
+ 并行，同一个TCP中，可以发送多个流。
+ 创建、关闭，客户端和服务端都可以主动关闭当前流。

帧

流是通过多个**帧**组成。帧是最小数据单位。每个帧都是**二进制**，会标出当前帧属于那个数据流（stream ID），每个帧都有自己的标志位（flag），帧长度（length），帧类型（type，区分是header 还是data抑或是其他）。

![](https://user-gold-cdn.xitu.io/2020/3/19/170ef65048bcd871?w=258&h=374&f=png&s=12507)

通过并行发送多个帧，接受方可以根据帧的stream ID来判断是那个数据流，在根据flag排序来还原整个数据流，再将二进制数据转化为所需数据。绕过阻塞，实现了多路复用。

![](https://user-gold-cdn.xitu.io/2020/3/19/170ef64da6fd672c?w=290&h=238&f=png&s=24914)

### HTTP头压缩

众所周知，HTTP请求/响应是由状态码、HTTP头和消息主体组成。当HTTP发展到1.1后，HTTP头数据变得非常非常复杂，因此，在HTTP/2.0中也提出了将头进行压缩的**HAPCK算法**对HTTP头进行压缩。

## 总结

+ HTTP/1.0使用非持久化连接，一个TCP只能承接一个HTTP并且请求-响应必须一一对应。

+ HTTP/1.1使用持久化连接，使得一个TCP可以承接多个HTTP。

+ HTTP/1.1在持久化连接的基础上使用管道化，让请求-响应分离。但可能会产生阻塞。

+ HTTP/2.0放弃请求响应概念，使用流进行HTTP通信，使用帧传送数据。通过二进制、和增加标志位和流ID的方式，让通信双方能在并行的通信中识别出完整的数据流。

+ HTTP头部日渐繁杂，通过HPACK算法进行压缩。

![](https://user-gold-cdn.xitu.io/2020/3/19/170ef649535db5d2?w=777&h=364&f=png&s=101526)