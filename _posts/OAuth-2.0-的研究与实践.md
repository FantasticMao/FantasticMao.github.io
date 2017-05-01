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
随着互联网行业的蓬勃发展，网络站点和应用APP的越来越多，用户在不同应用之间的使用上出现了一个问题：为了使用不同厂商的应用，如「贴吧」、「淘宝」和「QQ」，用户必须为每个应用单独注册帐号。在注册少数常用应用的帐号之后，用户还必须为一些低频使用的应用注册帐号。这繁琐应用使用现状和非人性化的用户体验，使得互联网领域中需要出现一个可以连接不同应用用户体系的可靠可行的规范协议，这就是 OAuth 协议。

> OAuth 开始于 2006 年 11 月，当时布莱恩·库克正在开发 Twitter 的 OpenID 实现。与此同时，社交书签网站 Ma.gnolia 需要一个解决方案允许使用 OpenID 的成员授权 Dashboard 访问他们的服务。这样库克、克里斯·梅西纳和来自 Ma.gnolia 的拉里·哈尔夫与戴维·雷科尔顿会面讨论在 Twitter 和 Ma.gnolia API 上使用 OpenID 进行委托授权。他们讨论得出结论，认为没有完成 API 访问委托的开放标准。

> 2007 年 4 月，成立了 OAuth 讨论组，这个由实现者组成的小组撰写了一个开放协议的提议草案。来自 Google 的德维特·克林顿获悉 OAuth 项目后，表示他有兴趣支持这个工作。2007 年 7 月，团队起草了最初的规范。随后，Eran Hammer-Lahav 加入团队并协调了许多 OAuth 的稿件，创建了更为正式的规范。2007 年 10 月, OAuth 核心 1.0 最后的草案发布了。

> 2008 年 11 月，在明尼阿波利斯举行的互联网工程任务组第 73 次会议上，举行了 OAuth 的起始会议讨论将该协议纳入 IETF 做进一步的规范化工作。这个会议参加的人很多，关于正式地授权在 IETF 设立一个 OAuth 工作组这一议题得到了广泛的支持。

> 2010 年 4 月，OAuth 1.0 协议发表为 RFC-5849，一个非正式 RFC。

> 2012 年 10 月 OAuth 2.0 正式发布，它作为 OAuth 协议的下一版本但不向下兼容 OAuth 1.0 协议。OAuth 2.0 协议关注客户端开发者的简易性，同时为移动设备、Web应用、桌面应用等多种客户端提供认证授权服务。

目前，国内外各大主流互联网公司如 Facebook、Google、腾讯、新浪等，均已支持 OAuth 2.0 协议。通过在其开发者平台上提供基于 OAuth 2.0 的开放授权 OpenApi ，由客户端开发者调用，便可以向市场上的其它中小型应用分享其海量的用户数据。通过这种方式，不同厂家不同类型的应用只要遵循 OAuth 2.0 协议流程，就可以引入各大主流网站的用户资源，简化登录流程，优化用户体验，从而提高应用的可用性和用户的活跃度。

本课题着手于 RFC-6479 规范文档，研究 OAuth 2.0 协议的工作原理，并参考各大主流网站 OAuth 2.0 协议的实现，付诸实践证实 OAuth 2.0 协议的可用性和可靠性，期望总结并得出一套开发 OAuth 2.0 应用的最佳实践。

## 本文的工作
基于以上背景和意义，本文将做以下工作：
1. 阅读 RFC-6479 规范文档，提炼 OAuth 2.0 基本概念、主要流程、关键步骤、注意事项等内容；
2. 比较各大主流网站的 OAuth 2.0 实现，总结并得出 OAuth 2.0 协议的最佳实践；
3. 设计并实现一个基于 OAuth 2.0 协议的认证授权应用，证实 OAuth 2.0 协议的可用性和可靠性；
4. 基于第三步中的应用，引入 HTML5 规范中的 WebSocket 技术，优化标准 OAuth 2.0 应用中的登录流程。

---

# OAuth 2.0 协议

## 协议简介
> The OAuth 2.0 authorization framework enables a third-party application to obtain limited access to an HTTP service, either on behalf of a resource owner by orchestrating an approval interaction between the resource owner and the HTTP service, or by allowing the third-party application to obtain access on its own behalf.

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
* User Agent：在计算机科学中，用户代理指的是代表用户行为的软件（软件代理程序）所提供的对自己的一个标识符。例如，一个电子邮件阅读器就是一个电子邮件客户端，而在会话发起协议中，用户代理的术语指代的是一个通信会话的所有两个终端。
* Protected Resource：受保护资源，是用户存储在 Resource Server 上的数据。OAuth 2.0 协议允许第三方应用通过访问令牌，在一定范围和有效期内受限制地访问用户存储在 Resource Server 上的用户私密数据。
* Authorization Grant：授权许可，是一个代表 Resource Owner 授权的凭证，由 Client 在获取访问令牌时使用。OAuth 2.0 协议规范中定义了四种授权类型：授权码、隐式授权、用户密码凭据和客户端凭据。
* Authorization Endpoint：授权端点，由 Resource Owner 来获取授权许可。Authorization Server 必须首先验证Resource Owner 的身份。
* Redirection Endpoint：重定向端点，由 Authorization Server 在与 Resorce Owner 交互完成之后，重定向用户代理时使用。
* Token Endpoint：令牌端点，是由 Client 使用，通过展示其授权许可或者刷新令牌从而获取访问令牌。令牌终端可以由所有类型的授权许可使用，除了隐式授权类型。
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
含有刷新令牌的协议流程如图 3.4.2 所示，流程包括如下步骤：
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
在启动 OAuth 2.0 协议之前，客户端开发者需要在授权服务器上注册客户端信息，并向授权服务器提交客户端类型、客户端回调 URI 和其它如应用名称、站点、描述、商标等必要的客户端信息数据。客户端注册的具体形式，协议规范中没有描述，不过通常是以一个简单的 HTML 表单形式提交注册信息。

OAuth 2.0 协议基于客户端的安全性，定义了两种客户端类型：机密类型和公有类型。协议中涉及的客户端大体分为以下几类：
1. web applicaiton：web 应用，是一种运行在 web 服务器上的机密类型的客户端。用户通过用户代理渲染的 HTML 界面来使用应用。颁发给此类型的客户端的凭据被存储在 web 服务上，并且不会被暴露给用户。
2. user-agent-based application：基于用户代理的应用，是一种公有类型客户端。其客户端代码是可从 web 服务器上任意下载，并能在用户的用户代理设备上（如 web 浏览器）任意运行。
3. native application：本地应用，是一种安装并执行在用户的设备上的公有类型客户端。

在客户端注册成功之后，作为客户端认证的需要，授权服务器应授予其一个全局唯一的客户端标识符（公钥）和一个客户端密码（密钥）。客户端标识符是不敏感的，可以暴露在请求 URI 中，因此授权服务器不能仅以客户端标识符来认证客户端。

### 认证授权
为了获取访问令牌，客户端需要向用户获取授权。用户授权以授权许可的形式，向客户端授予访问受保护资源的权限。OAuth 2.0 协议中定义了四种授权许可：授权码、隐式授权、用户密码凭据和客户端凭据。

#### Authorization Code Grant
授权码可用于获取访问令牌和刷新令牌，并且为机密类型的客户端进行了优化。由于 OAuth 2.0 是基于 HTTP 重定向实现的协议，所以要求客户端必须能够与用户的用户代理（通常是 web 浏览器）进行交互，并且能接收授权服务器通过重定向返回的传入请求（incoming requests）。
```
+----------+
| Resource |
|   Owner  |
|          |
+----------+
     ^
     |
    (B)
+----|-----+          Client Identifier      +---------------+
|         -+----(A)-- & Redirection URI ---->|               |
|  User-   |                                 | Authorization |
|  Agent  -+----(B)-- User authenticates --->|     Server    |
|          |                                 |               |
|         -+----(C)-- Authorization Code ---<|               |
+-|----|---+                                 +---------------+
  |    |                                         ^      v
 (A)  (C)                                        |      |
  |    |                                         |      |
  ^    v                                         |      |
+---------+                                      |      |
|         |>---(D)-- Authorization Code ---------'      |
|  Client |          & Redirection URI                  |
|         |                                             |
|         |<---(E)----- Access Token -------------------'
+---------+       (w/ Optional Refresh Token)
Note: The lines illustrating steps (A), (B), and (C) are broken into
two parts as they pass through the user-agent.
                        图 3.5.3.1
```
基于授权码形式的授权许可流程如图 3.5.3.1 所示，流程包括如下步骤：
1. (A) Client 通过将 Resource Owner 的用户代理重定向到授权端点来启动流程。Client 使用包括 client identifier,requested scope,local state,redirection URI 等参数发送给 Authorization Server，Authorization Server 将在授权/拒绝访问权限之后，发回用户代理给 Client。
2. (B) Authorization Server 认证 Resource Owner 并与其建立连接，不论认证的结果是授权或拒绝。
3. (C) 假设 Resource Owner 已经授权允许访问，则 Authorization Server 使用 Client 之前提供（在请求参数中或注册时填写的）的重定向 URI ，将用户代理重定向到 Client。重定向 URI 中包含了授权码和由 Client 提供的 local state。
4. (D) Client 使用上一步接收的授权码，向 Authorization Server 的令牌端点请求访问令牌。请求过程中，CLient 将被 Authorization Server 认证。
5. (E) Authorization Server 认证 Client，验证其授权码，并确认其重定向 URI 与第三步中的 URI 匹配。若验证有效，则 Authorization Server 响应访问令牌给 Client，可以附加上刷新令牌。

其中，客户端在成功获取授权码之后，使用「application/x-www-form-urlencoded」编码形式，携带 grant_type、code、redirect_uri、client_id 四个参数，向授权服务器请求访问令牌，例如：
```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencode

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```

#### Implicit Grant
隐式授权可用于获取访问令牌，并且为已知重定向 URI 的公有类型客户端进行了优化。使用隐式授权的客户端通常都是在浏览器中实现的一些脚本语言，如 JavaScript。
```
+----------+
| Resource |
|  Owner   |
|          |
+----------+
     ^
     |
    (B)
+----|-----+          Client Identifier     +---------------+
|         -+----(A)-- & Redirection URI --->|               |
|  User-   |                                | Authorization |
|  Agent  -|----(B)-- User authenticates -->|     Server    |
|          |                                |               |
|          |<---(C)--- Redirection URI ----<|               |
|          |          with Access Token     +---------------+
|          |            in Fragment
|          |                                +---------------+
|          |----(D)--- Redirection URI ---->|   Web-Hosted  |
|          |          without Fragment      |     Client    |
|          |                                |    Resource   |
|     (F)  |<---(E)------- Script ---------<|               |
|          |                                +---------------+
+-|--------+
  |    |
 (A)  (G) Access Token
  |    |
  ^    v
+---------+
|         |
|  Client |
|         |
+---------+
Note: The lines illustrating steps (A) and (B) are broken into two
parts as they pass through the user-agent.
                        图 3.5.3.2
```
基于隐式授权形式的授权许可流程如图 3.5.3.2 所示，流程包括如下步骤：
1. (A) Client 通过将 Resource Owner 的用户代理重定向到授权端点来启动流程。Client 使用包括 client identifier,requested scope,local state,redirection URI 等参数发送给 Authorization Server，Authorization Server 将在授权/拒绝访问权限之后，发回用户代理给 Client。
2. (B) Authorization Server 认证 Resource Owner 并与其建立连接，不论认证的结果是授权或拒绝。
3. (C) 假设 Resource Owner 已经授权允许访问，则 Authorization Server 使用 Client 之前提供（在请求参数中或注册时填写的）的重定向 URI ，将用户代理重定向到 Client。重定向 URI 参数中包含了访问令牌。
4. (D) 用户代理通过请求 web 站点的客户端资源来跟随重定向指令，并且用户代理会保存重定向 URI 中的数据在本地。
5. (E) web 站点返回一个可以获取整个重定向 URI 的网站页面，并且提取在 URI 片段中的访问令牌。
6. (F) 用户代理在本地执行 web 站点上的脚本，提取访问令牌。
7. (G) 用户代理将访问令牌传递给 Client。

#### Resource Owner Password Credentials Grant
用户密码凭据形式的授权许可适用于用户信息的客户端，如操作系统级别的设备或已被高度授权的应用。使用此授权类型的时候，授权服务器应当特别小心，只有当其它形式的授权不能用的时候，才允许使用它。
```
+----------+
| Resource |
|  Owner   |
|          |
+----------+
     v
     |    Resource Owner
    (A) Password Credentials
     |
     v
+---------+                                  +---------------+
|         |>--(B)---- Resource Owner ------->|               |
|         |         Password Credentials     | Authorization |
| Client  |                                  |     Server    |
|         |<--(C)---- Access Token ---------<|               |
|         |    (w/ Optional Refresh Token)   |               |
+---------+                                  +---------------
                        图 3.5.3.3
```
基于用户密码凭据形式的授权许可流程如图 3.5.3.3 所示，流程包括如下步骤：
1. (A) Resource Owner 向 Client 提供用户名和密码凭据。
2. (B) Client 通过获取的用户密码凭据，向 Authorization Server 获取访问令牌。
3. (C) Authorization Server 认证客户端，并验证其用户密码凭据，若认证成功，则颁发访问令牌。

#### Client Credentials Grant
当客户端是在其控制之下获取受保护资源的时候，或受控制于用户的时候，客户端可以仅仅使用其凭据获取访问令牌。客户端凭据类型的授权许可只能由机密类型客户端使用。
```
+---------+                                  +---------------+
|         |                                  |               |
|         |>--(A)- Client Authentication --->| Authorization |
| Client  |                                  |     Server    |
|         |<--(B)---- Access Token ---------<|               |
|         |                                  |               |
+---------+                                  +---------------+
                        图 3.5.3.4
```
基于客户端凭据形式的授权许可流程如图 3.5.3.4 所示，流程包括如下步骤：
1. (A) Client 向 Authorization Server 认证，并向令牌端点请求访问令牌。
2. (B) Authorization Server 认证 Client 身份，若认证成功，则颁发访问令牌。

### 颁发令牌
若 Client 请求访问令牌通过验证和认证，则 Authorization Server 颁发访问令牌和可选的刷新令牌。成功返回访问令牌的响应如下所示：
```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":"example",
  "expires_in":3600,
  "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
  "example_parameter":"example_value"
}
```

## 注意事项
注意事项：
* Resource Server 和 Authorization Server 应共同限制访问令牌中指定的访问范围和有效期。
* Authorization Server 颁发刷新令牌给 Client 是可选的。
* Authorization Server 必须支持 HTTP 基本认证（Base64编码 username:password 字符串），且 Authorization Server 必须对 Client 认证做次数限制。
* Client 请求访问令牌过程中，Authorization Server 必须验证其授权许可，以及请求中的回调 URI 和注册时填写的回调 URI 是否匹配。
* 访问令牌可以表示一个取回授权信息的标识符，或者可以自包含相关授权信息（token字符串携带一些数据和签名）。
* 刷新令牌只与 Authorization Server 交互，永远不会被发送给 Resource Server。
* Authorization Server 必须为持有密钥的 Client 支持 HTTP Basic 的认证方案。
* 在协议流程中的请求必须使用 TLS。
* Client 获取访问令牌的请求必须使用 HTTP POST。

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