---
title: OAuth 2.0 的研究与实践
date: 2017-04-26 11:07:48
categories: 毕业设计
---
# 摘要
OAuth 2.0 是由互联网工程任务组于 2012 年 10 月发表在 RFC-6749 规范文档中定义的认证授权协议，是一种基于 HTTP 重定向实现的开放授权框架。它允许用户让第三方应用访问该用户在某一网站上存储的私密的资源（如照片，视频，联系人列表），而无需将用户名和密码提供给第三方应用。本文先简单介绍 OAuth 2.0 协议基本概念、主要流程和关键步骤，讨论了在开发 OAuth 2.0 应用时的注意事项，接着在充分阅读和理解 RFC-6749 规范和参考国内外主流网站的 OAuth 2.0 实现之上，设计并实现了一个基于 OAuth 2.0 协议的开放授权应用，证实 OAuth 2.0 协议的可用性和可靠性，并指出了常规 OAuth 2.0 应用在登录流程中的不足之处，给出优化方案，给予实践证明。
<!-- more -->

# 绪论

## 背景与意义
随着互联网行业的蓬勃发展，网络站点和应用APP的越来越多，用户在不同应用之间的使用上出现了一个问题：为了使用不同厂商的应用，如「贴吧」、「淘宝」和「QQ」，用户必须为每个应用单独注册帐号。在注册少数常用应用的帐号之后，用户还必须为低频使用的几十上百的应用注册帐号。这极其繁琐和非人性化的使用现状，使得互联网领域中必须尽快出现一个可以连接不同应用用户体系的可靠可行的规范协议，这就是 OAuth 协议。

在互联网工程任务组第73次会议上，举行了OAuth的起始会议，讨论了将OAuth协议纳入IETF做进一步的规范化工作。OAuth 协议第一版在 2010 年 4 月发表为 RFC-5849，但 RFC-5849 仅是一个非正式的 RFC。2012 年 10 月 OAuth 2.0 正式发布，它作为 OAuth 1.0 协议的下一版本但不向下兼容 OAuth 1.0 协议。OAuth 2.0 协议关注客户端开发者的简易性，同时为移动设备、Web应用、桌面应用等多种客户端提供认证授权服务。

目前，国内外各大主流网站均已支持 OAuth 2.0 协议，国外的Facebook、Google、Twitter，国内的腾讯、新浪、豆瓣等等的诸多大型互联网公司都已在其开发者平台上提供了基于 OAuth 2.0 的开放授权 OpenApi。这意味着市场上的其它中小型应用便可以通过遵循 OAuth 2.0 协议流程，引入这些大型互联网公司的海量用户资源，以此吸引更多用户，从而提高应用。

本课题着手于 RFC-6479 规范文档，充分理解 OAuth 2.0 协议的原理，并付诸实践。。。。。。。。。。。。。。。旨在证实 OAuth 2.0 协议的可用性和可靠性，并指出一个常规 OAuth 2.0 应用中存在的不足之处，并给予可行的优化方案。

## 本文的工作
基于以上背景和意义，本文将做以下工作：
1. 阅读 RFC-6479 规范文档，提炼 OAuth 2.0 基本概念、主要流程、关键步骤、注意事项等内容；
2. 比较各大主流网站的 OAuth 2.0 实现，总结并得出 OAuth 2.0 协议的最佳实践；
3. 设计并实现一个基于 OAuth 2.0 协议的认证授权应用，证实 OAuth 2.0 协议的可用性和可靠性；
4. 基于第三步中的应用，引入 HTML5 规范中的 WebSocket 技术，优化标准 OAuth 2.0 应用中的登录流程。

---

# OAuth 2.0 协议

## 协议简介
OAuth 2.0 授权框架允许第三方应用，代表「资源所有者」通过合法地协调「资源所有者」和 HTTP 服务之间交互，或者仅代表「第三方应用」本身，对 HTTP 服务进行有限地访问。它允许用户让第三方应用访问该用户在某一网站上存储的私密的资源（如照片，视频，联系人列表），而无需将用户名和密码提供给第三方应用。

OAuth 2.0 协议允许用户提供一个令牌，而不是用户名和密码来访问他们存放在特定服务提供者的数据。每一个令牌授权一个特定的网站（例如，视频编辑网站)在特定的时段（例如，接下来的2小时内）内访问特定的资源（例如仅仅是某一相册中的视频）。这样，OAuth 2.0 协议让用户可以授权第三方网站访问他们存储在另外服务提供者的某些特定信息，而非所有内容。

## 协议角色
OAuth 2.0 协议中定义了以下四个角色：
* Resource Owner：资源所有者，是一个能授予访问「受保护资源」权限的实体。当资源所有者是人的时候，它被称为终端的用户。
* Resource Server：资源服务器，是一个托管了受保护资源，能接收和响应使用「访问令牌」访问受保护资源请求的服务器。
* Client：客户端，是代表 Resource Owner，使用其「授权许可」获取受保护资源的第三方应用。术语 Client 并非特指某一特定的的实现点，服务端程序或桌面应用或移动设备，都可以是 OAuth 2.0 协议流程中的客户端。
* Authorization Server：授权服务器，是在 Client 认证成功，并获取了 Resource Owner 的授权之后，颁发访问令牌给 Client 的服务器。

## 术语解释
OAuth 2.0 协议中包含了以下关键术语：
* Protected Resource：受保护资源
* Authorization Grant：授权许可，是一个代表 Resource Owner 授权的凭证，由 Client 在获取 Access Token 时使用。OAuth 2.0 协议规范中定义了四种授权类型：授权码、隐式授权、用户密码凭据和客户端凭据。
    * Authorization Code：授权码，是通过使用 Authorization Server 作为 Client 和 Resource Owner 之间的中介获得的。Client 不是直接向 Resource Owner 请求授权，而是将 Resource Owner 引导到 Authorization Server，从而将 Resource Owner 转发给具有授权码的 Client。
    * Implicit：隐式授权
    * Resource Owner Password Credentials：用户密码凭据
    * Client Credentials：客户端凭据
* Access Token：访问令牌，是用于获取受保护资源的凭据，是一个代表了颁发给 Client 的授权信息的字符串。访问令牌对客户端来说通常是加密且非透明的。访问令牌由 Resource Owner 授予，指定了请求访问的范围和有效期。Resource Server 和 Authorization Server 应共同限制访问令牌的访问行为。
* Refresh Token：刷新令牌，是用于获取访问令牌的凭据，由 Authorization Server 颁发给 Client。刷新令牌是在当访问令牌失效或过期之后，由 Client 使用获取新的访问令牌时使用。刷新令牌也可以获取额外的相同或更窄访问范围的访问令牌时使用。Authorization Server 是可选地选择是否颁发刷新令牌给 Client。与访问令牌不同，刷新令牌只与 Authorization Server 交互，并且永远不会被发送给 Resource Server。

## 主要流程

### 基本流程
```
+--------+                               +---------------+
|        |--(A)- Authorization Request ->|   Resource    |
|        |                               |     Owner     |
|        |<-(B)-- Authorization Grant ---|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(C)-- Authorization Grant -->| Authorization |
| Client |                               |     Server    |
|        |<-(D)----- Access Token -------|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(E)----- Access Token ------>|    Resource   |
|        |                               |     Server    |
|        |<-(F)--- Protected Resource ---|               |
+--------+                               +---------------+
                        图 3.4.1
```
OAuth 2.0 协议中四个角色的交互流程如图 3.4.1 所示，基本流程包括如下步骤：
1. (A) Client 向 Resource Owner 请求授权许可。授权请求可以如图所示直接发送给 Resource Owner，但最好是间接地通过 Authorization Server 请求授权。
2. (B) Client 接收到一个代表 Resource Owner 授权的授权许可，通常以协议规范中定义的四种许可类型之一表示。授权许可的类型由 Client 请求授权和 Authorization Server 所支持的类型决定。
3. (C) Client 向 Authorization Server 认证身份，并通过展示授权许可请求访问令牌。
4. (D) Authorization Server 认证 Client 身份并验证其授权许可，若认证成功则颁发访问令牌。
5. (E) CLient 通过展示访问令牌向 Resource Server请求受保护资源。
6. (F) Resource Server 验证请求中访问令牌，若验证成功则响应 Client 请求。

### 刷新令牌流程
```
+--------+                                           +---------------+
|        |--(A)------- Authorization Grant --------->|               |
|        |                                           |               |
|        |<-(B)----------- Access Token -------------|               |
|        |               & Refresh Token             |               |
|        |                                           |               |
|        |                            +----------+   |               |
|        |--(C)---- Access Token ---->|          |   |               |
|        |                            |          |   |               |
|        |<-(D)- Protected Resource --| Resource |   | Authorization |
| Client |                            |  Server  |   |     Server    |
|        |--(E)---- Access Token ---->|          |   |               |
|        |                            |          |   |               |
|        |<-(F)- Invalid Token Error -|          |   |               |
|        |                            +----------+   |               |
|        |                                           |               |
|        |--(G)----------- Refresh Token ----------->|               |
|        |                                           |               |
|        |<-(H)----------- Access Token -------------|               |
+--------+           & Optional Refresh Token        +---------------+
                        图 3.4.2
```
含有刷新令牌的协议流程如图 3.4.2所示，流程包括如下步骤：
1. (A) Client 向 Authorization Server 请求认证，并使用授权许可获取访问令牌。
2. (B) Authorization Server 认证 Client 身份并验证其 Authorization Grant，若认证成功则颁发访问令牌和刷新令牌。
3. (C) CLient 通过展示访问令牌向 Resource Server请求受保护资源。
4. (D) Resource Server 验证请求中访问令牌，若验证成功则响应 Client 请求。
5. (E) 重复（C）和（D）步骤直到访问令牌失效。若 CLient 得知访问令牌已经失效则跳转到（G），否则就再次请求访问受保护资源。
6. (F) 访问令牌失效之后，Resource Owner 返回一个无效令牌的错误描述。
7. (G) Client 通过展示刷新令牌，向 Authorization Server 请求一个新的访问令牌。客户端认证需要基于客户端的类型和授权服务器的认证策略。
8. (H) Authorization Server 认证 Client 身份并验证其刷新令牌，若认证成功则颁发一个新的访问令牌和一个可选的刷新令牌。

## 关键步骤

### 客户端注册

### 客户端认证

### 授权

## 注意事项
注意事项：
* Resource Server 和 Authorization Server 应共同限制访问令牌中指定的访问范围和有效期。
* Authorization Server 颁发刷新令牌给 Client 是可选的。
* 刷新令牌只与 Authorization Server 交互，永远不会被发送给 Resource Server。

* 客户端从资源所有者获得授权许可(步骤1和2所示)的更好方法是使用授权服务器作为中介。
* 访问令牌所限制客户端的访问时间和访问范围，由资源服务器和认证服务器共同限制。
* 访问令牌可以表示一个取回授权信息的标识符，或者可以自包含相关授权信息（token字符串携带一些数据和签名）。
* 刷新令牌是可选的，有授权服务器决定，且刷新令牌只与授权服务器交互，与资源服务器无交互
* 客户端注册时，必须指定 **客户端类型** 和 **客户端回调域名** 以及可选的一些应用信息（如应用名称、描述、logo等）。
* 授权服务器必须支持 HTTP 基本认证（Base64编码 username:password 字符串），且授权服务器必须对客户端认证做次数限制。

---

# OAuth 2.0 应用实现

## 基本思路
a. 对比主流应用对 OAuth 2.0 协议的支持情况
b. 

## 实现方案

### 结构体系

#### 服务端结构体系

#### 客户端结构体系

## 实现细节
伪代码/流程图

## 运行效果

## 存在的问题
明确、充分

---

# OAuth 2.0 应用优化

## 产生原因

## 优化方案

### WebScoket 技术简介
WebSocket 是一种在单个 TCP 连接上进行双全工通讯的协议。WebSocket 通信协议于 2011 年被 IETF 定为标准 RFC-6455，并被 RFC-7936 所补充规范。WebSocket API 也被 W3C 定为标准。

WebSocket 作为一种持久化协议，区别于非持久的 HTTP 协议。它相比于其它的信息传递方式，如长连接 long poll 和 Ajax 轮询，具有：
1. 较少的控制开销
2. 更强的实时性
3. 保持连接状态
4. 更好的二进制支持
5. 可以支持扩展
6. 更好的压缩效果

### 引入 WebSocket 技术

## 实现细节

## 测试效果

---

# 结束语
是对本课题工作的总结，与摘要中的内容对应，切勿写个人感言！！！

# 致谢