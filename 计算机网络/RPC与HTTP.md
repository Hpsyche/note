## RPC、HTTP浅谈

### HTTP

http其实是一种网络传输协议，基于TCP，规定了数据传输的格式。现在客户端浏览器与服务端通信基本都是采用Http协议。也可以用来进行远程服务调用。缺点是消息封装臃肿。

现在热门的Rest风格，就可以通过http协议来实现。

- http的实现技术：HttpClient

### RPC

RPC是指远程过程调用，它是对不同系统间相互调用方式的一种描述，**RPC不是协议也不是一种新技术，严格意义上应该称它是一种解决方案**（概念）或技术实现的框架。**RPC框架底层一般支持多种协议，比如：HTTP、TCP、自定协议等**。

由于选择RPC分布式框架越来越流行，所以一般来说RPC指的是基于TCP的RPC，其基于原生TCP通信，速度快，效率高。早期的webservice，现在热门的dubbo、eureka、consul等，都是RPC的典型。

注意：当使用RPC框架实现服务间调用的时候，要求服务提供方和服务消费方 都必须使用统一的RPC框架，要么都dubbo，要么都cxf。

#### RPC的目标

* 实现远程调用其他计算机的服务

  要实现远程调用，肯定是通过网络传输数据。A程序提供服务，B程序通过网络将请求参数传递给A，A本地执行后得到结果，再将结果返回给B程序。这里需要关注的有两点：

  1）采用何种网络通讯协议？

  现在比较流行的RPC框架，都会采用TCP作为底层传输协议

  2）数据传输的格式怎样？

  两个程序进行通讯，必须约定好数据传输格式。就好比两个人聊天，要用同一种语言，否则无法沟通。所以，我们必须定义好请求和响应的格式。另外，数据在网路中传输需要进行序列化，所以还需要约定统一的序列化的方式。

* 像调用本地服务一样调用远程服务

  如果仅仅是远程调用，还不算是RPC，因为RPC强调的是过程调用，调用的过程对用户而言是应该是透明的，用户不应该关心调用的细节，可以像调用本地服务一样调用远程服务。所以RPC一定要对调用的过程进行封装。

![](D:\Work\TyporaNotes\note\计算机网络\pict\RPC.png)

### SpringCloud中的协议

spring cloud 默认使用http restful（至少我查阅的资料全是http），而提供RPC方案。

如今SpirngCloud中的各注册中心，如：ribbon和feign都是基于RPC框架。

微服务在本质上，就是rpc。RPC是一种开发方式，他可以基于HTTP协议（比如gRPC)，也可以基于其他协议，比如更基础的TCP，还有基于mq等。

### RPC与HTTP对比

* RPC是一种开发方式，他可以基于HTTP协议（比如gRPC)，也可以基于其他协议，比如更基础的TCP，还有基于mq等。
* 通信协议的选择只是RPC实现中的一小部分，更重要的一部分是编码协议。比如json/xml属于文本编码，还有二进制字节编码，比如protoful，thrift。http对比tcp，最诟病的就是多余的头信息，造成整个数据包体积过大。
* RPC（距离HSF）采用长连接方式进行通信。HSF的不采用直接的NIO编程，而是通过Netty框架，保证长连接和心跳。http也可以通过keep-Alive实现长连接，但是这种方式无疑给代码带来了更多的侵入。**如果客户端都采用长连接方式，面对海量客户端的请求时，服务端必然要对这些长连接管理，否则服务端肯定崩掉。**
* 负载均衡。这个就是HSF这种RPC框架的优势，http如果要做到负载均衡，还需要加nginx来搭配。
* 服务治理。显然RPC就是为此而生的，自己去维护一堆http服务是一件痛苦的事情。

### 如何选择

既然两种方式都可以实现远程调用，我们该如何选择呢？

* 速度来看，RPC要比http更快，虽然底层都是TCP，但是http协议的信息往往比较臃肿
* 难度来看，RPC实现较为复杂，http相对比较简单
* 灵活性来看，http更胜一筹，因为它不关心实现细节，跨平台、跨语言。

因此，两者都有不同的使用场景：

* 如果对效率要求更高，并且开发过程使用统一的技术栈，那么用RPC还是不错的。
* 如果需要更加灵活，跨语言、跨平台，显然http更合适

### restful

至于restful，其实他本身是一套将资源对象化的设计标准，不过目前都作为技术实现再用，本身又分为严格的和非严格的。从目前上来说restful接口可以认为是一种基于http使用json编码的RPC实现，但还是本身restful是设计规范，更多的是约束资源的访问获取手段，不应当用于复杂的函数调用。

#### 前端不只是基于http协议

最后前后端，目前javascript也有json-RPC，ajax-RPC一类的更专注于函数调用的RPC实现，可以基于HTTP，也可以基于websocket，如果目的是函数调用，你可以试用一下，会比使用restful舒服很多。

#### 选择

rpc是没有post和get之分的.

你可以把多个rpc服务想象成一个很大的项目中的某些方法，比如 user相关方法，支付相关方法，rpc 只是把他们拆分开来而已，rpc直接可以互相调用，通过客户端调用即可。

当然前端也可以直接调用rpc，但是这样不好的一点就是各个端都不统一，可能做出来的东西bug比较多。

如果提供http服务，后端可以在http中调用相关rpc，把需要的聚合在一起，返回给前端，这样就比较统一了。

**所以说，一般的选择是，前后端交互基于http协议的resuful风格，后端之间各服务相互调用基于rpc协议。**

## RPC与HTTP调用的应用场景

RPC框架提供的是面向服务的封装，它针对服务的性能效率、可用性等都做了优化（比如提供了：注册中心、服务治理、负载均衡、二进制传输、熔断、服务降级等功能），**是一套完整的解决方案**；而HTTP调用缺少这些高级特性，它只是简单的数据通信，另外HTTP API受限于HTTP协议（要带HTTP请求头），传输效率及安全性不如RPC。

### 为什么使用RPC而不是简单的HTTP API？

HTTP API一般在接口数量不多的情况下采用的，因为它使用起来简单快捷，直接利用现成的HTTP协议就可以进行数据传输。

**但对于一个大型项目，内部模块子系统众多，接口也变得很多了，在这种情况下如果再使用一个个零散的HTTP API，维护成本极高**。所以RPC框架优点就显示出来了，比如说：

- 支持长链接，减少了网络开销；
- 拥有注册中心，服务治理起来更方便；
- 有监控功能，易于定位问题；
- 对调用方来说是无感知、统一化的。

因为良好的rpc调用是面向服务的封装，针对服务的可用性和效率等都做了优化。单纯使用http调用则缺少了这些特性。

### 其他

服务器通讯原理就是一台socket服务器A,另一台socket客户端B,现在如果要通讯的话直接以流方式写入或读出。这样能实现通讯，但有个问题。如何知道更多信息？比如需要发送流大小，编码，Ip等。这样就有了协议，协议就是规范，就是发送的流中携带了很多的内容。那回到刚刚的问题。

发送的内容就是文本类型，客户端就得序列化，那么常用的就有json，xml之类

如果想把内容变得更小，那就有二进制了。把文本变成二进制传递。

说到 rpc 与http接口，不要太复杂了。rpc 协议更简单内容更小，那么来说效率是要高一点

然后rpc 是什么。就是socket 加动态代理，你去想想，为什么客户端能调用你的service .