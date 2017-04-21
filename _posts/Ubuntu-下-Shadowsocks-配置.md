---
title: Ubuntu 下 Shadowsocks 配置
date: 2016-12-05 23:15:14
categories: 防坑指南
---
Ubuntu 操作系统下的 Shadowsocks 配置，翻越防火长城。向原作者 clowwindy 致敬。<!-- more -->

### 介绍
Shadowsocks（影梭）是一个使用 socks5 协议的开源代理工具，分为服务端和客户端，访问墙外网络稳定且迅速。原作者 [clowwindy](https://github.com/clowwindy) 因在 2015 年 8 月受到某政府要求无奈停止维护并删除了项目代码。事件 [传送门](https://web.archive.org/web/20150822042959/https://github.com/shadowsocks/shadowsocks-iOS/issues/124#issuecomment-133630294)。现在 Shadowsocks 由 Github 上的 [shadowsocks组织](https://github.com/shadowsocks) 维护，向无私且伟大的开源奉献者们致敬。

### 1. 获取 Shadowsocks 账号
搜关键字「Shadowsocks账号」，搜索引擎会提供很多账号，不过账号的有效性不能保证。如大海捞针一般寻找账号，费心费神，不如自己付费，享受稳定服务（我只是分享......）。本人在 [https://crola.xyz/](https://crola.xyz/) 站点购买 Shadowsocks 服务端账号（我没有做广告......）。
![image](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E7%8E%AF%E5%A2%83%E4%B8%8BShadowsocks%E9%85%8D%E7%BD%AE/1.png)

### 2. 安装并配置 Shadowsocks
使用 `sudo apt-get install shadowsocks` 安装 Shadowsocks 客户端。结束安装之后，编辑 Shadowsocks 配置文件 `sudo vim /etc/shadowsocks/config.json`，将 **server**、**server_port**、**password** 与 Shadowsocks 服务端配置相匹配。
![image](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E7%8E%AF%E5%A2%83%E4%B8%8BShadowsocks%E9%85%8D%E7%BD%AE/2.png)
使用命令`sslocal -c /etc/shadowsocks/config.json`开启 Shadowsocks 代理。
![image](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E7%8E%AF%E5%A2%83%E4%B8%8BShadowsocks%E9%85%8D%E7%BD%AE/3.png)

### 3. 使用 Chrome 浏览器翻墙
新开一个终端，使用命令 `google-chrome --proxy-server="socks5://127.0.0.1:1080"` 让 Chrome 代理本机请求，实现 Chrome 浏览器翻墙。
**注意**：该命令应在 Chrome 未启动时执行，否则失败。
![image](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E7%8E%AF%E5%A2%83%E4%B8%8BShadowsocks%E9%85%8D%E7%BD%AE/4.png)

### 4. 配置全局 Socks 代理翻墙
第 3 步中仅单独实现了 Chrome 翻墙，局限性很大。在系统设置-->网络-->网络代理中，填写 Socks 主机 127.0.0.1 和监听端口 1080，并应用到整个系统，即可实现所有应用翻墙。
![image](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E7%8E%AF%E5%A2%83%E4%B8%8BShadowsocks%E9%85%8D%E7%BD%AE/5.png)

### #附
* 安装 Chrome 时，若由依赖关系问题导致安装失败，应当使用 `sudo apt-get -f install` 修复。
![image](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E7%8E%AF%E5%A2%83%E4%B8%8BShadowsocks%E9%85%8D%E7%BD%AE/6.png)
* 开启全局网络代理之后，退出时应 **注意关闭**，否则将无法联网。
