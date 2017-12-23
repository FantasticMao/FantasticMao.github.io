---
title: UDP 必知必会
date: 2017-12-23 15:39:28
categories: 编程
tags:
- 网络基础
- HTTP
---
摘自[《计算机网络》](https://book.douban.com/subject/26960678/)，并参考自维基百科。<!-- more -->

# 协议概述
在计算机网络中，[UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol)（User Datagram Protocol，用户数据报协议）是 TCP/IP 协议族的核心成员之一，由 David P. Reed 于 1980 设计，并在 [RFC-768](https://tools.ietf.org/html/rfc768) 中正式定义。

UDP 是一个简单的无连接协议，可以使分组交换的计算机在 IP 网络上使用数据报文通信。

# 报文结构
```
  0      7 8     15 16    23 24    31
 +--------+--------+--------+--------+
 |     Source      |   Destination   |
 |      Port       |      Port       |
 +--------+--------+--------+--------+
 |                 |                 |
 |     Length      |    Checksum     |
 +--------+--------+--------+--------+
 |
 |          data octets ...
 +---------------- ...

      User Datagram Header Format
```

- Source Port
- Destination  Port
- Length
- Checksum

# 主要特点
1. 无连接

2. 尽最大努力交付

3. 面向报文

4. 无拥塞控制

4. 支持一对一、一对多、多对多的交互通信

5. 首部开销小