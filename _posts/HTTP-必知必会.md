---
title: HTTP 必知必会
date: 2017-12-15 22:36:26
categories: 网络基础
tags:
- HTTP
---
摘自《图解 HTTP》，并参考自维基百科。<!-- more -->

![images](http://ogvr8n3tg.bkt.clouddn.com/HTTP%E5%BF%85%E7%9F%A5%E5%BF%85%E4%BC%9A/1.png)

HTTP 协议和 TCP/IP 协议族内的其它众多协议相同，用于客户端和服务端之间的通信。请求访问文本或图像资源的一端被称为客户端，而提供资源响应的一端被称为服务端。在两台计算机之间使用 HTTP 协议通信时，在一条通信线路上必定有一端是客户端，另一端是服务端。

HTTP 协议规定，请求从客户端先发出，最后服务端响应该请求并返回。换句话说，肯定是先从客户端开始建立通信的，服务端在没有接收到请求之前不会发送响应。

# 协议概述

## 发展历史

## HTTP 报文

### 报文结构

### 首部字段

### 请求方法
更全面地了解 Request Methods，推荐阅读：[RFC 7231 - Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content#page-21](https://tools.ietf.org/html/rfc7231#page-21)

### 响应状态码

### MIME Type

---

# 实现机制

## 无状态性和 Cookie
HTTP 是一种不保存状态，即无状态的协议。HTTP 协议自身不对请求和响应之间的通信状态进行保存。也就是说在 HTTP 协议这个级别，网络通讯中对于发送的任何请求或响应都不会做持久化处理，协议本身并不保存之前的一切请求或响应报文的信息。这是为了更快地处理大量事务，确保协议的可伸缩性，而特意把 HTTP 协议设计成如此简单。

随着 Web 的不断发展，因无状态而导致业务处理变得棘手的情况增多了。HTTP 协议为了实现期望保持状态的功能，于是引入了 Cookie 技术。

[Cookie](https://en.wikipedia.org/wiki/HTTP_cookie) 技术通过在请求和响应报文中写入 Cookie 信息来控制客户端的状态，它会根据从服务端发送的响应报文内的一个叫做 `Set-Cookie` 的首部字段，通知客户端保存 Cookie 值。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入 Cookie 值后发送出去。此时，服务端发现客户端发送过来的 Cookie 值后，会去检查究竟是从哪一个客户端发来的请求，然后对比服务器上的记录，最后得到请求之前的状态信息。

## HTTP 内容协商
一个 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) 可能对应多种不同表现形式的资源，例如不同的语言、不同的媒体类型等等。[HTTP 内容协商](https://en.wikipedia.org/wiki/Content_negotiation)（HTTP content negotiation）可以对同一个 URI 提供多个不同版本的资源，以便客户端能指定和获取最适合它们版本的资源。HTTP 协议提供了以下几种不同的内容协商机制：server-driven、agent-driven、transparent、其它混合的组合。

server-driven 服务端驱动（或称主动）内容协商是根据在服务端上执行的算法来选择资源的最合适的表现形式。这通常是基于客户端所提供的可接受标准来执行的。具体来说，当客户端向服务端发送请求时，在请求头的 `Accept` 字段中列出可接受的媒体类型和相关的质量因素，而服务端则按照客户端所提供的这些参数，选择其最合适的资源版本返回响应。

Wireshark 抓包示意图：
![images]()

agent-driven 用户代理驱动（或称被动）内容协商是根据在客户端上执行的算法来选择资源的最合适的表现形式。这通常是基于服务端所提供的资源的表现形式列表和元数据，以此来执行的。

其它内容协商机制：略。

## HTTP 持久连接
在 HTTP 协议的初始版本中，每进行一次 HTTP 通信就要建立和断开一次 TCP 连接。这样频繁地建立和断开无谓的 TCP 连接，会增加网络通信的开销。于是，HTTP 1.1 版本提出了 [HTTP 持久连接](https://en.wikipedia.org/wiki/HTTP_persistent_connection)（HTTP persistent connection，也称作 HTTP keep-alive 或 HTTP connection reuse），它可以解决上述 TCP 连接的问题。HTTP 持久连接是使用同一个 TCP 连接来发送和接收多个 HTTP 请求／响应，而不是为每一个新的请求／响应打开新的 TCP 连接。

在 HTTP 1.0 版本中，没有官方的 keep-alive 操作。为了实现持久连接，通常的操作是在现有版本的协议上添加一个标记。比如，如果浏览器支持的话，它会在请求头中添加 `Connection: Keep-Alive`，然后当服务端接收到请求，作出响应的时候，它也会在响应头中添加 `Connection: Keep-Alive`。这样做，HTTP 请求就不会中断 TCP 连接，而是保持连接。当客户端发送另一个 HTTP 请求时，它会使用同一个 TCP 连接。这种情况会一直持续到客户端或服务端的任意一方，在明确提出断开连接之后终止。

在 HTTP 1.1 版本中，所有的连接默认都是持久连接，除非特殊声明不支持。

使用多个连接和使用持久连接的对比图（图片来源自维基百科）：
![images](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/HTTP_persistent_connection.svg/450px-HTTP_persistent_connection.svg.png)

Wireshark 抓包示意图：
![images]()

## HTTP 管线化
HTTP 持久连接使多个请求以管线化的方式发送成为可能。[HTTP 管线化](https://en.wikipedia.org/wiki/HTTP_pipelining)（HTTP pipelining）可以在单个 TCP 连接上并行发送多个 HTTP 请求，且不需要在发送过程中等待服务端的响应。管线化机制可以提升动态加载 HTML 页面的速度，特别是在高延迟的网络环境下，如连接卫星的网络环境。但对于正常宽带连接的网络环境，提升效果却不是很明显。因为对于客户端并行发送的网路请求，服务端还是需要以相同的顺序响应，这就有可能造成了 HOL blocking（Head-of-line blocking，队头阻塞）。

非幂等性的请求，如 POST 请求，不应该被管线化。GET 和 HEAD 请求在任何情况下都可以被管线化。其它幂等性的请求，如 PUT 和 DELETE 请求，取决于当前请求是否依赖其它请求，来决定其是否可以被管线化。

HTTP 管线化需要客户端和服务端的同时支持。虽然使用 HTTP 1.1 协议可以确认服务端是支持管线化机制的，但这并不意味着服务端必须返回管线化的响应。如果客户端选择了非管线化的请求，服务端则会正常返回非管线化的响应。

使用非管线化和管线化的对比图（图片来源自维基百科）：
![images](https://upload.wikimedia.org/wikipedia/commons/thumb/1/19/HTTP_pipelining2.svg/640px-HTTP_pipelining2.svg.png)

PS：在理想情况下，HTTP 响应报文是作为整包且有序地发送给客户端的，并且服务端会在响应报文中使用 `Content-Length` 首部字段标志响应实体的长度，因此客户端可以依据 `Content-Length` 值来计算当前响应实体的结束和下一个响应实体的开始。而使用 HTTP 持久连接和 HTTP 管线化，则会使客户端难以确认服务端返回的响应的先后顺序，以至于 `Content-Length` 无法被正常使用。为了解决这个问题，HTTP 1.1 版本引入了 [分块传输编码](#HTTP-分块传输编码)。它定义了一个 `last-chunk` 比特位，可以用来设置每一个响应的结束标识，客户端也由此可以得知每一个响应实体的结束状态了。

## HTTP 压缩
[HTTP 压缩](https://en.wikipedia.org/wiki/HTTP_compression)（HTTP compression） 是一种在客户端和服务端之间使用压缩内容传输的数据传输机制，它可以充分利用带宽，提升网络传输速率。

客户端在发送请求时，通过在请求头中添加 `Accept-Encoding` 字段，告知服务端其所支持的压缩方式。服务端在响应请求时，通过在响应头中添加 `Content-Encoding` 或 `Transfer-Encoding` 字段，告知客户端其所使用的压缩方式。常见的压缩方式，举例如下：
- gzip：基于 GNU zip 的压缩方式；
- deflate：基于 deflate 算法的压缩方式；
- compress：基于 UNIX compress 的压缩方式（不推荐大多数应用使用）；
- identity：默认值，不压缩内容；
- and so on ......

Wireshark 抓包示意图：
![images]()

## HTTP 分块传输编码
[HTTP 分块传输编码](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)（Chunked transfer encoding）是一种允许服务端将响应的数据分块传输给客户端的数据传输机制。在分块传输编码中，数据流被分成一系列不重叠的「数据块」，以字节为单位被发送，且彼此独立地被客户端接收。

在一次 HTTP 响应的报文中，通过使用 `Transfer-Encoding:chunked` 指示当前响应是分块传输编码的，并以传输一个 0 字节的「数据块」为结束标识。

Wireshark 抓包示意图：
![images]()

## HTTP 缓存
[HTTP 缓存](https://en.wikipedia.org/wiki/Web_cache)（HTTP cache）是一种为了减少请求的带宽延迟，从而对页面进行临时存储的技术。通过缓存技术，可以使 HTTP 请求在满足特定条件的情况下，从缓存中直接获取响应数据，提升页面加载速度和减少对服务端的压力。HTTP 协议定义了三种控制缓存的基本机制：freshness、validation、invalidation。

Freshness 机制允许一个 Response 在不需要源服务器重新校验的情况下，可以被直接用作于响应数据，并且 Response 的新鲜程度可以由客户端和服务端同时控制。例如，`Expires` 响应头指定缓存的失效日期，`Cache-Control: max-age` 指令告知缓存的有效时间（单位为秒）。

Validation 机制可以用于校验一个被缓存的 Response 在过时之后，是否依旧有效。例如，如果响应头中包含 `Last-Modified` 字段，那么则可以使用一个包含 `If-Modified-Since` 请求头字段的条件请求，来查看缓存是否已经被更新。除此之外还有 ETag（entity tag）机制，它同时支持强校验和弱校验。

Invalidation 机制通常是针对使用缓存技术的副作用。例如，一个关联着缓存数据的 URL，若被以 PUT/POST/DELETE 方法请求了，那么其缓存数据就会失效。

更全面地了解 HTTP 缓存机制，推荐阅读：[RFC 7234 - Hypertext Transfer Protocol (HTTP/1.1): Caching](https://tools.ietf.org/html/rfc7234)。<!-- [浅谈浏览器http的缓存机制](http://www.cnblogs.com/vajoy/p/5341664.html) --> <!-- [What's the difference between Cache-Control: max-age=0 and no-cache?](https://stackoverflow.com/questions/1046966/whats-the-difference-between-cache-control-max-age-0-and-no-cache/1383359#1383359) -->

---

# 安全问题
HTTP 协议的主要不足之处，举例如下：
- 通信使用明文，内容可能会被窃听；
- 不验证通信方的身份，因此有可能遭遇伪装；
- 无法证明报文的完整性，所以有可能遭遇篡改。

## 被窃听

## 被伪装

## 被篡改

---

# 其它要点

## HTTPS

## HTTP2

## REST

## WebSocket