# 了解Web及网络基础

## 确保可靠性的TCP协议

为了准确无误地将数据送达目标处，TCP协议采用了三次握手策略。用TCP协议把数据包送出去后，TCP一定会向对方确认是否成功发送。握手过程中使用了TCP的标志----SYN（synchronize）和ACK（acknowledgement）。

* 发送端首先发送一个带SYN标志的数据包给对方。
* 接收端收到后，回传一个带有SYN/ACK标志的数据包以示传达确认信息。
* 最后，发送端再回传一个带ACK标志的数据包，代表“握手”结束。



# 简单的HTTP协议

## 请求报文和响应报文

注意：先从客户端建立通信的，服务器端在没有接收到请求之前不会发送响应。

### 请求报文

请求报文是由**请求方法、请求URI、协议版本、可选的请求首部字段和内容实体**构成的。

如：

//请求方法、请求URI、协议版本

POST /index.html HTTP/1.1

//请求首部字段

Host:hackr.jp
Connection:keep-alive
Connection-Type:application/x-www-form-urlencoded
Content-Length:16

//内容实体

name=ueno&age=37

### 响应报文

响应报文是由**协议版本、状态码（表示成功或失败的数字的代码）、用以解释状态码的原因短语、可选的响应首部字段和内容实体**构成的。

如：

//协议版本、状态码、解释

HTTP/1.1 200 OK

//响应首部字段

Data:Tue, 19 Jan 2019 00:53:11 GMT
Connection-Type:text/html
Content-Length:16

//内容实体

<html>

...

</html>

## HTTP是不保存状态的协议

即无状态协议

注意：HTTP/1.1也是无状态协议，但引入了Cookie技术

## 告知服务器意图的HTTP方法

### GET

GET - 从指定的资源请求数据

### POST

POST - 向指定的资源提交要被处理的数据

### GET和POST的对比

|                  |                             GET                              |                             POST                             |
| :--------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  后退按钮/刷新   |                             无害                             |   数据会被重新提交（浏览器应该告知用户数据会被重新提交）。   |
|       书签       |                         可收藏为书签                         |                        不可收藏为书签                        |
|       缓存       |                           能被缓存                           |                           不能缓存                           |
|     编码类型     |              application/x-www-form-urlencoded               | application/x-www-form-urlencoded 或 multipart/form-data。为二进制数据使用多重编码。 |
|       历史       |                   参数保留在浏览器历史中。                   |                 参数不会保存在浏览器历史中。                 |
| 对数据长度的限制 | 是的。当发送数据时，GET 方法向 URL 添加数据；URL 的长度是受限制的（URL 的最大长度是 2048 个字符）。 |                           无限制。                           |
| 对数据类型的限制 |                     只允许 ASCII 字符。                      |                 没有限制。也允许二进制数据。                 |
|      安全性      | 与 POST 相比，GET 的安全性较差，因为所发送的数据是 URL 的一部分。在发送密码或其他敏感信息时绝不要使用 GET ！ | POST 比 GET 更安全，因为参数不会被保存在浏览器历史或 web 服务器日志中。 |
|      可见性      |              数据在 URL 中对所有人都是可见的。               |                   数据不会显示在 URL 中。                    |

### PUT

PUT方法用来传输文件。

由于HTTP/1.1的PUT方法自身不带验证机制，任何人都可以上传文件，存在安全问题，故一般web网站不使用该方法，但有时可能开放。

若传输成功，其响应返回状态码为204，表示：请求执行成功了，但无数据返回。

### HEAD

获取报文首部

如：

//请求

HEAD /index.html HTTP/1.1
HOST:www.hackr.jp

//响应

返回index.html有关的响应首部

### DELETE

删除文件，功能和PUT相反。（响应返回状态码也为204）

###  OPTIONS

询问支持的方法（1.0不支持）

如：

//请求

OPTIONS * HTTP/1.1
Host:www.hackr.jp

//响应

HTTP/1.1 200 ok
Allow:GET,POST,HEAD,OPTIONS

### TRACE

追踪路径（1.0不支持）

### CONNECT

使用隧道协议连接代理（1.0不支持）

## 持久连接

持久连接旨在建立1次TCP连接后进行多次请求和响应的交互

在HTTP/1.1中，所有的连接都默认是持久连接

要实现持久连接，客户端和服务器端都要支持

### 管线性

即客户端可同时并行发送多个请求，而不需要等待收到响应后再发出

### 使用Cookie的状态管理

HTTP是无状态协议，它不对之前发生过的请求和响应的状态进行管理。也就是说，无法根据之前的状态进行本次的请求处理。

因此，因此了Cookie技术，Cookie技术通过在请求和响应报文中写入Cookie信息来控制客户端的状态。

过程如下：

1. 请求报文（没有Cookie信息的状态）

GET /reader/ HTTP/1.1
Host:hackr.jp

（注意：此时首部字段中没有Cookie的相关信息）

2. 响应报文（服务端生成Cookie信息）

HTTP/1.1 200 OK
Data:Tue, 19 Jan 2019 00:53:11 GMT
<font color=red><Set-Cookie:sid=1234567890123456;。。。 ></font>
Content-Type:text/plain;charset=UTF-8

3. 请求报文（自动发送保存着的Cookie信息）

GET /reader/ HTTP/1.1
Host:hackr.jp
Cookie:sid=1234567890123456



# HTTP报文内的HTTP信息

## 发送多种数据的多部分对象集合

多部分对象集合包含的对象如下。

* multipart/form-data

  在Web表单文件上传时使用

* multipart/byteranges

  状态码206（Partial Content，部分内容）响应报文包含了多个范围的内容时使用

在HTTP报文中使用多部分对象集合时，需要在首部字段里加上Content-type。

## 内容协商返回最合适的内容

当浏览器的默认语言为英语或中文，访问相同的URI的Web页面时，则会显示对应的英文版和中文版的Web页面。这样的机制称为内容协商（Content Negotiaton)。

内容协商机制是指客户端和服务器端就响应的资源内容进行交涉、然后提供给客户端最为适合的资源。

