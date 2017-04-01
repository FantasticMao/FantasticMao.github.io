---
title: Ubuntu下开发环境搭建
date: 2017-02-27 20:14:35
categories: 防坑指南
---

记一次个人 PC 的 Ubuntu 16.04 LTS 重装记录，包含开发环境的搭建、日常软件的安装、几个踩过的坑。<!-- more -->

### 前言
最近在知乎上看到了 [一篇回答](https://www.zhihu.com/question/19811112/answer/132006027)，决定重装（升级？） Ubuntu......

不得不说，Ubuntu 相比于 CentOS，体验真的棒很多，尤其是在桌面应用上。

---

### sudo apt-get 安装系列
*Git*：执行 `sudo apt-get install git`。安装成功之后，使用 `git config --global $key $value` 命令，配置 Git 的全局 user.name、user.email、core.editor。

*Vim*：执行 `sudo apt-get install vim`。

*Shadowsocks*：执行 `sudo apt-get install shadowscoks`。安装成功之后，[配置 shadowsocks](/2016/12/05/Ubuntu下Shadowsocks配置/)。

*NodeJS*：执行 `sudo apt-get install nodejs nodejs-legacy`。

*NPM*：执行 `sudo apt-get install npm`。安装成功之后，执行 `npm config set registry https://registry.npm.taobao.org/`，修改 NPM 源。

*VLC*：执行 `sudo apt-get install vlc`。

*Shutter*：执行 `sudo apt-get install shutter`。

*MySQL*：执行 `sudo apt-get install mysql-server mysql-client`。安装成功之后，配置 MySQL。
1. 执行 `mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;` 语句修改 MySQL 远程访问权限。
2. 执行 `mysql> FLUSH PRIVILEGES;` 语句刷新权限。
3. 执行 `mysql> SHOW VARIABLES LIKE 'character%';` 语句查看 MySQL 字符集编码。
4. 打开 MySQL 主配置文件 `sudo vim /etc/mysql/my.conf`，如下图添加 client 和 mysqld 配置，修改默认字符集编码。
5. 执行 `service mysql restart` 重启MySQL服务。
![images](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E4%B8%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/1.png)

---

### dpkg -i 安装系列
下载 Chrome、网易云音乐、搜狗输入法、WPS、VS Code 的 deb 安装包，使用 `sudo dpkg -i $packagename` 命令安装。若因依赖关系问题导致安装失败，则使用 `sudo apt-get -f install` 修复。

*解决Chrome闪屏问题*：个人笔记本型号 Dell-Inspiron-7460，使用 Chrome 56 在线看视频时，标签栏上下的位置会频繁闪屏。参考自 [链接](https://beisongnansong.wordpress.com/2016/08/12/%E8%A7%A3%E5%86%B3ubuntu%EF%BC%88chrome%EF%BC%89%E7%9A%84%E9%97%AA%E5%B1%8F%E9%97%AE%E9%A2%98/) 解决了此问题，具体措施：
1. 新增配置文件 `sudo vim /usr/share/X11/xorg.conf.d/20-intel.conf`，贴入下图内容保存。
2. 进入 chrome://flags/ 页面，停用「加速的2D画布 」，启用「零副本光栅化处理程序 」。
3. 重新登录用户。
![images](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E4%B8%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/2.png)

---

### 配置  安装系列
下载 JDK、Tomcat、IDEA 的 tar.gz 压缩包，使用 `tar -xzf $packagename` 解压缩，并移动解压缩文件夹下至 `/usr/local/` 目录下。

*JDK*：
1. 打开环境变量配置文件 `sudo vim /etc/profile`，如下图添加 Java 环境变量。
2. 重新登录用户。
![images](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E4%B8%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/3.png)

*Tomcat*：
1. 打开环境变量配置文件 `sudo vim /etc/profile`，如下图一添加 Tomcat 环境变量。
2. 打开 Tomcat 配置文件 `sudo vim /usr/local/tomcat8/conf/server.conf`，如下图二修改 Tomcat 的 URI 默认编码。
3. 重新登录用户。
![images](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E4%B8%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/4.png)
![images](http://ogvr8n3tg.bkt.clouddn.com/Ubuntu%E4%B8%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/5.png)

*IDEA*：
1. 执行 `/usr/local/idea/bin/idea.sh`，以 **普通用户权限** 启动 IDEA，而非是管理员用户权限。
2. 执行 `sudo cp /usr/local/idea/plugins/maven/lib/maven3/conf/settings.xml ~/.m2/`，将 Maven 配置文件拷贝一份至 Maven 本地仓库中。在配置文件中，如下添加阿里的 Maven 镜像和修改默认 JDK 版本。
3. 修改 IDEA 各项设置......太多就略过了。
```xml
<mirrors>
    <mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
......
<profiles>
    <profile>
        <id>jdk-1.8</id>
        <activation>
            <jdk>1.8</jdk>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <maven.compiler.source>1.8</maven.compiler.source>
            <maven.compiler.target>1.8</maven.compiler.target>
            <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
        </properties>
    </profile>
</profiles>
```

---

### 编译  安装系列
Nginx：可执行 `sudo apt-get install nginx` 安装 Nginx，不过为在 Response Headers 中隐藏 Ubuntu 信息，还是选择编译安装吧。
1. 执行 `sudo apt-get install libpcre3 libpcre3-dev zlib1g-dev`，预先安装 Nginx 依赖包。
2. 进入 [Nginx官网](http://nginx.org/)，个人选择 1.10.3 稳定版本，执行 `wget http://nginx.org/download/nginx-1.10.3.tar.gz` 下载并解压缩。
3. 执行 `cd nginx-1.10.3/`，进入 Nginx 目录。
4. 执行 `./configure`，开始执行脚本。
5. 执行 `make`，开始编译 Nginx。
6. 执行 `sudo make install`，开始安装 Nginx。
7. 安装成功之后，Nginx 主配置文件位于 `/usr/local/nginx/conf/nginx.conf`。

*Redis*：
1. 进入 [Redis官网](http://redis.io)，个人选择 3.2.8 稳定版，执行 `wget http://download.redis.io/releases/redis-3.2.8.tar.gz` 下载并解压缩。
2. 执行 `cd redis-3.2.8/`，进入 Redis 目录。
3. 执行 `make`，开始编译 Redis。
4. 安装成功之后，redis-server 和 redis-cli 位于 `../redis-3.2.8/src`。

---

### #附

*Debian的 `apt-get` 用法*：
* `sudo apt-get update` 从所有配置源中更新软件包的信息。
* `sudo apt-get upgrade` 从配置源中升级所有已安装的软件包。
* `sudo apt-get install $packagename` 安装一个或多个软件包。
* `sudo apt-get -f install` 修复软件包之间的依赖关系。
* `sudo apt-get autoremove $packagename` 移除自动安装的软件包及其依赖。

下文节选自 `man apt` 描述部分：
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
