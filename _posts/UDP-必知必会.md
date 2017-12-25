---
title: UDP 必知必会
date: 2017-12-23 15:39:28
categories: 编程
tags:
- 网络基础
- UDP
---
摘自[《计算机网络》](https://book.douban.com/subject/26960678/) 第 5 章 传输层，并参考自维基百科。<!-- more -->

# 协议概述
[UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol)（User Datagram Protocol，用户数据报协议）是 OSI 参考模型中一种无连接的传输层协议，提供面向事务的简单不可靠的数据传送服务，是 TCP/IP 协议族的核心成员之一，由 David P. Reed 于 1980 设计，并在 [RFC-768](https://tools.ietf.org/html/rfc768) 中正式定义。

UDP 使用最小协议机制的简单无连接通信模型，可以使分组交换的计算机在 IP 网络上使用数据报通信。它提供 checksum 用于保证数据完整性，提供 port number 用于区分源地址和目标地址不同功能的应用进程。UDP 适用于对错误校验和错误纠正要求不高的应用场景，如多媒体应用、IP 电话等等。UDP 在通讯时不需要像 TCP 一样进行握手，因此减少了网络通信的开销和发送数据之前的延迟，但它也是不可靠的传输协议，即不保证数据可靠的交付和时序。

# 报文结构
报文格式
\-\-\-\-\-\-
![iamges](http://ogvr8n3tg.bkt.clouddn.com/UDP%E5%BF%85%E7%9F%A5%E5%BF%85%E4%BC%9A/1.png)

首部字段
\-\-\-\-\-\-
Source Port：可选字段，表示发送进程的端口，在需要对方回信时选用。缺省值为全 0；
Destination Port：必选字段，表示接收进程的端口；
Length：包含 header 和 data 的用户数据报文的长度，最小值是 8；
Checksum：用于校验用户数据报文在传输过程中是否有错。

# 主要特点
无连接

尽最大努力交付

面向报文

无拥塞控制

支持一对一、一对多、多对多的交互通信

首部开销小