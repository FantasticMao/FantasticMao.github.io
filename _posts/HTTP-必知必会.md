---
title: HTTP 必知必会
date: 2017-12-15 22:36:26
categories: 编程
tags:
- HTTP
---
摘自《图解 HTTP》，并参考自维基百科。<!-- more -->

## HTTP 协议用于客户端和服务端之间的通信
HTTP 协议和 TCP/IP 协议族内的其它众多协议相同，用于客户端和服务端之间的通信。请求访问文本或图像资源的一端被称为客户端，而提供资源响应的一端被称为服务端。在两台计算机之间使用 HTTP 协议通信时，在一条通信线路上必定有一端是客户端，另一端是服务端。

HTTP 协议规定，请求从客户端先发出，最后服务端响应该请求并返回。换句话说，肯定是先从客户端开始建立通信的，服务端在没有接收到请求之前不会发送响应。

## URI 和 URL
https://zh.wikipedia.org/wiki/统一资源定位符

https://zh.wikipedia.org/wiki/统一资源标志符

URI：Uniform Resource Identifier，统一资源标识符

URL：Uniform Resource Locator，统一资源定位符

## HTTP 报文
略

## HTTP Method
- GET 获取资源
- POST 传输实体主体
- PUT 传输文件
- DELETE 删除文件
- HEAD 获得报文首部
- OPTIONS 询问支持的方法
- TRACE 追踪路径
- CONNECT 要求用 SSL/TLS 协议连接代理

## HTTP 的无状态性
HTTP 是一种不保存状态（即无状态）的协议。HTTP 协议自身不对请求和响应之间的通信状态进行保存，也就是说在 HTTP 这个级别，协议对于发送的请求或响应都不做持久化处理。

使用 HTTP 协议，每当有新的请求发送时，就会有对应的新响应产生。协议本身并不保存之前一切的请求或响应报文的信息。这是为了更快地处理大量事务，确保协议的可伸缩性，而特意把 HTTP 协议设计成如此简单。

可是随着 Web 的不断发展，因无状态而导致业务处理变得棘手的情况增多了。HTTP 协议为了实现期望的保持状态的功能，于是引入了 [Cookie](https://zh.wikipedia.org/wiki/Cookie) 技术。Cookie 技术通过在请求和响应报文中写入 Cookie 信息来控制客户端的状态。

Cookie 技术会根据从服务端发送的响应报文内的一个叫做 Set-Cookie 的首部字段信息，通知客户端保存 Cookie 值。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入 Cookie 值后发送出去。服务端发现客户端发送过来的 Cookie 后，会去检查究竟是从哪一个客户端发来的连接请求，然后对比服务器上的记录，最后得到请求之前的状态信息。

## HTTP 持久连接
HTTP 协议的初始版本中，每进行一次 HTTP 通信就要建立和断开一次 TCP 连接。比如，使用浏览器访问一个包含多张图片的 HTML 页面，在发送请求访问页面资源的同时，也会请求该 HTML 页面包含的其它资源。因此，每次的请求都会造成无谓的 TCP 连接建立和断开，增加通信量的开销。

HTTP/1.1 提出的 [HTTP 持久连接](https://zh.wikipedia.org/wiki/HTTP持久连接)（HTTP persistent connection，也称作 HTTP keep-alive 或 HTTP connection reuse），可以解决上述 TCP 连接的问题。持久连接的特点是，只要任意一端没有明确提出断开连接，则就保持 TCP 的连接状态。并且 HTTP/1.1 中所有的连接默认都是持久连接。

持久连接的好处在于减少了 TCP 连接的重复建立断开造成的额外开销，减轻服务端的负载。

## HTTP 管线化
HTTP 持久连接使多个请求以管线化的方式发送成为可能。[HTTP 管线化](https://zh.wikipedia.org/wiki/HTTP管线化)（HTTP pipelining）可以使多个 HTTP 请求并行发送，而不需要一个接一个地等待响应。比如，当请求一个包含 10 张图片的 HTML Web 页面，与挨个连接相比，使用持久连接可以让请求更快结束，而使用管线化技术则可以比持久连接还要快。请求数越多，时间相差就越明显。

HTTP 管线化机制必须通过 [HTTP 持久连接](#HTTP-持久连接) 才能实现，并且只有 GET 和 HEAD 请求才可以进行管线化，非幂等性的方法请求不会被管线化。

## HTTP 压缩
HTTP 在传输数据时可以按照数据原貌直接传输，也可以在传输过程中通过编码提升传输速率。[HTTP 压缩](https://zh.wikipedia.org/wiki/HTTP压缩) 可以在传输实体时编码内容，从而使应用能有效地处理大量的访问请求。

## HTTP 内容协商
https://zh.wikipedia.org/wiki/内容协商

HTTPS