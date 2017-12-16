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

## HTTP 方法
- GET 获取资源
- POST 传输实体主体
- PUT 传输文件
- DELETE 删除文件
- HEAD 获得报文首部
- OPTIONS 询问支持的方法
- TRACE 追踪路径
- CONNECT 要求用 SSL/TLS 协议连接代理

## HTTP 无状态性和 Cookie
HTTP 是一种不保存状态，即无状态的协议。HTTP 协议自身不对请求和响应之间的通信状态进行保存。也就是说在 HTTP 协议这个级别，网络通讯中对于发送的任何请求或响应都不会做持久化处理。

使用 HTTP 协议，每当有新的请求发送时，就会有对应的新的响应产生。协议本身并不保存之前的一切请求或响应报文的信息。这是为了更快地处理大量事务，确保协议的可伸缩性，而特意把 HTTP 协议设计成如此简单。

可是随着 Web 的不断发展，因无状态而导致业务处理变得棘手的情况增多了。HTTP 协议为了实现期望保持状态的功能，于是引入了 [Cookie](https://zh.wikipedia.org/wiki/Cookie) 技术。

Cookie 技术通过在请求和响应报文中写入 Cookie 信息来控制客户端的状态，它会根据从服务端发送的响应报文内的一个叫做 Set-Cookie 的首部字段信息，通知客户端保存 Cookie 值。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入 Cookie 值后发送出去。此时，服务端发现客户端发送过来的 Cookie 值后，会去检查究竟是从哪一个客户端发来的请求，然后对比服务器上的记录，最后得到请求之前的状态信息。

## HTTP 持久连接
在 HTTP 协议的初始版本中，每进行一次 HTTP 通信就要建立和断开一次 TCP 连接。这样频繁地建立和断开无谓的 TCP 连接，会增加网络通信的开销。于是，HTTP 1.1 版本提出了 [HTTP 持久连接](https://zh.wikipedia.org/wiki/HTTP持久连接)（HTTP persistent connection，也称作 HTTP keep-alive 或 HTTP connection reuse），它可以解决上述 TCP 连接的问题。HTTP 持久连接是使用同一个 TCP 连接来发送和接收多个 HTTP 请求／响应，而不是为每一个新的请求／响应打开新的 TCP 连接。

在 HTTP 1.0 版本中，没有官方的 keep-alive 操作。为了实现持久连接，通常的操作是在现有版本的协议上添加一个标记。比如，如果浏览器支持的话，它会在请求头中添加 `Connection: Keep-Alive`，然后当服务端接收到请求，作出响应的时候，它也会在响应头中添加 `Connection: Keep-Alive`。这样做，HTTP 请求就不会中断 TCP 连接，而是保持连接。当客户端发送另一个 HTTP 请求时，它会使用同一个 TCP 连接。这种情况会一直持续到客户端或服务端的任意一方，在明确提出断开连接之后终止。

在 HTTP 1.1 版本中，所有的连接默认都是持久连接，除非特殊声明不支持。

使用多个连接和使用持久连接的对比图（图片来源自维基百科）：
![images](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/HTTP_persistent_connection.svg/450px-HTTP_persistent_connection.svg.png)

Wireshark 抓包示意图：
![images]()

## HTTP 管线化
HTTP 持久连接使多个请求以管线化的方式发送成为可能。[HTTP 管线化](https://zh.wikipedia.org/wiki/HTTP管线化)（HTTP pipelining）可以在单个 TCP 连接上并行发送多个 HTTP 请求，且不需要在发送过程中等待服务端的响应。

HTTP 管线化机制必须通过 HTTP 持久连接才能实现，并且只有 GET 和 HEAD 请求才可以进行管线化，非幂等性的方法请求不会被管线化。

使用非管线化和管线化的对比图（图片来源自维基百科）：
![images](https://upload.wikimedia.org/wikipedia/commons/thumb/1/19/HTTP_pipelining2.svg/640px-HTTP_pipelining2.svg.png)

PS：HTTP 持久连接和 HTTP 管线化会使客户端难以确认服务端返回的响应的先后顺序。为了解决这个问题，HTTP 1.1 版本中引入了分块传输编码。它定义了一个 `last-chunk` 比特位，可以用来设置每一个响应的结束标识，客户端也因此可以得知下一个响应的开始。

## HTTP 压缩
HTTP 在传输数据时可以按照数据原貌直接传输，也可以在传输过程中通过编码提升传输速率。[HTTP 压缩](https://zh.wikipedia.org/wiki/HTTP压缩) 可以在传输实体时编码内容，从而使应用能有效地处理大量的访问请求。

Wireshark 抓包示意图：
![images]()

## HTTP 内容协商
https://zh.wikipedia.org/wiki/内容协商

HTTPS