---
title: Ubuntu下开发环境搭建
date: 2017-02-27 20:14:35
categories: 防坑指南
---

一次个人PC的Ubuntu 16.04 LTS重装记录，包含开发环境的搭建、日常软件的安装、几个踩过的坑。<!-- more -->

### 前言
最近在知乎上看到了[一篇回答](https://www.zhihu.com/question/19811112/answer/132006027)，决定重装（升级？）Ubuntu......

不得不说，Ubuntu相比于CentOS，体验真的棒很多，尤其是在桌面应用上。
![images](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E4%B8%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/1.png)

### sudo apt-get 安装系列
* Git：执行`sudo apt-get install git`。安装成功之后，使用`git config --global $key $value`命令，设置Git的全局user.name和user.email。
* Vim：执行`sudo apt-get install vim`。
* Shadowsocks：执行`sudo apt-get install shadowscoks`。安装成功之后，[配置shadowsocks](/2016/12/05/Ubuntu下Shadowsocks配置/)。
* NodeJS：执行`sudo apt-get install nodejs && nodejs-legacy`。注意此处的`nodejs-legacy`。
* NPM：执行`sudo apt-get install npm`。安装成功之后，执行`npm config set registry https://registry.npm.taobao.org/`命令，修改NPM源。
* VLC：执行`sudo apt-get install vlc`。
* Shutter：执行`sudo apt-get install shutter`。

### dpkg -i 安装系列
下载Chrome、网易云音乐、搜狗输入法、WPS、Sublime的deb安装包，使用`sudo dpkg -i $packagename`命令安装。若因依赖关系问题导致安装失败，则使用`sudo apt-get -f install`修复。

安装成功之后：1. 开启ss，登录Chrome账号，同步浏览标签、密码等；2. 修改网易云音乐快捷键。

### 压缩包  安装系列
下载JDK、TOMCAT、IDEA、Redis、Nginx的tar.gz压缩包，使用`tar xzf $packagename`解压缩。

### 编译  安装系列
执行`sudo apt-get install gcc`，确认gcc已安装。

### 附

* Debian的`apt-get`用法
	* `sudo apt-get update` 从所有配置源中更新软件包的信息
	* `sudo apt-get upgrade` 从配置源中升级所有已安装的软件包
	* `sudo apt-get install $packagename` 安装一个或多个软件包
	* `sudo apt-get -f install` 修复软件包之间的依赖关系
	* `sudo apt-get autoremove $packagename` 移除自动安装的软件包及其依赖

	下文节选自`man apt`描述部分：
	```
	DESCRIPTION
	       apt provides a high-level commandline interface for the package
	       management system. 

	       update (apt-get(8))
		   update is used to download package information from all configured
		   sources.

	       upgrade (apt-get(8))
		   upgrade is used to install available upgrades of all packages
		   currently installed on the system from the sources configured via
		   sources.list(5).

	       full-upgrade (apt-get(8))
		   full-upgrade performs the function of upgrade but will remove
		   currently installed packages if this is needed to upgrade the
		   system as a whole.

	       install, remove, purge (apt-get(8))
		   Performs the requested action on one or more packages specified via
		   regex(7), glob(7) or exact match. 

	       autoremove (apt-get(8))
		   autoremove is used to remove packages that were automatically
		   installed to satisfy dependencies for other packages and are now no
		   longer needed as dependencies changed or the package(s) needing
		   them were removed in the meantime.

	       search (apt-cache(8))
		   search can be used to search for the given regex(7) term(s) in the
		   list of available packages and display matches.

	       show (apt-cache(8))
		   Show information about the given package(s) including its
		   dependencies, installation and download size, sources the package
		   is available from, the description of the packages content and much
		   more. 

	       list (work-in-progress)
		   list is somewhat similar to dpkg-query --list in that it can
		   display a list of packages satisfying certain criteria. 

	       edit-sources (work-in-progress)
		   edit-sources lets you edit your sources.list(5) files in your
		   preferred texteditor while also providing basic sanity checks.

	```
