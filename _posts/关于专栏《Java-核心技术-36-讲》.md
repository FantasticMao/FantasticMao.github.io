---
title: 关于专栏《Java 核心技术 36 讲》
date: 2018-05-09 12:39:42
categories: 编程
tags:
- Java
---
最近我在极客时间上订阅了一个专栏 —— [《Java 核心技术 36 讲》](https://time.geekbang.org/column/intro/82)。<!-- more -->这是一个以 Java 经典面试题为切入点，来讲解和剖析相关领域知识的专栏。大致看了专栏的目录，发现其中涉及的知识点，即包含自己完全未接触过的知识点（广度不够），也包含接触过但仍然一知半解的知识点（深度不够），也包含相当熟悉但却回答不上的知识点（体系化不够）。

哎，革命尚未成功，同志仍需努力。

对于学习技术这件事，记一些想法：要更侧重于通过阅读书籍／文档／手册／RFC 等方式，而不是仅仅通过看视频／听分享／听演讲等方式获取新知识、学习新技术。记几个观点：学习需要自我驱动、学习的资料需要官方化、学习的知识需要体系化。

闲言少叙，开始摘记吧。

---

# 谈谈你对 Java 平台的理解

Java 是一种被广泛使用的面向对象编程语言，支持面向对象的封装、多态、继承三大特性。Java 的基本语法类似于 C 语言，相对规范和严谨（区别于 PHP、Python 等弱类型语言），支持 Annotaion、Generic、Lambda、Method Reference 等等。除此之外，Java 的核心类库和第三方库也十分丰富，核心类库包括 IO／NIO、Collection、Netword、Concurrent、Security、Date／Time 等等，第三方库包括 Spring Framework、Netty、Hibernate、Jackson、Guava 等等。

Java 作为一个老牌且成熟的编程语言，其发展多年的虚拟机也经受了业界苛刻的考验。JVM（Java Virtual Machine）不仅支持了 Java 宣传标语 —— Write Once, Run Anywhere —— 的跨平台特性，并且提供了丰富的垃圾收集机制来自动分配和回收内存。其中，常用的垃圾收集器如 Serial、Parallel、CMS、G1 等。另外，JVM 除了支持 source code -> byte code -> machine code 的编译 & 解释运行方式，还支持 JIT（Just In Time）编译方式，能够极大提高 Java 程序的运行性能。最后，JVM 作为一个强大的虚拟机，还支持运行例如 Clojure、Scala、Groovy、JRuby、Jython 等等大量的符合 JVM 字节码规范的语言。

由此衍生的问题：谈谈你对 Spring Framework 的理解

---

# Exception 与 Error 有什么区别
