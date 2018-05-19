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

在 Java 标准异常中，`Throwable` 类表示任何可以被作为异常抛出的类，`Exception` 类和 `Error` 类都继承了 `Throwable` 类。

`Error` 类表示编译错误和系统错误，是在程序正常运行情况下，不大可能会出现的异常，例如 `OutOfMemoryError`、`StackOverflowError`。

`Exception` 类则表示可以被作为异常抛出的基本类型，是在程序正常运行情况下的可预料错误。`Exception` 类的异常分为 **受检查异常**（checked）和 **不受检查异常**（unchecked）。受检查异常必须在程序编译期被显示捕获和被正确处理，例如 `IOException`、`SQLException`。不受检查异常需继承于 `RuntimeException` 类，会在程序运行时被自动抛出，不需要被显示捕获，例如 `NullPointerException`、`ArrayIndexOutOfBoundsException`、`IllegalArgumentException`。

编写异常处理代码的最佳实践：
- 不要掩盖／生吞异常；
- Throw Early, Catch Late；
- 在抛出异常信息时，避免泄漏敏感信息；
- 在知道如何处理异常的情况下，再捕获并处理异常；
- 尽量不要 try 整段代码块，也要避免使用异常控制代码流程；
- 不要捕获类似 `Exception` 类的通用异常，而是应该捕获特定异常；
- 受检查异常被抛出后，往往不能被恢复，并且对函数式编程和三元表达式十分不友好；
- JDK 7 支持 multiple-catch 语法和基于 `java.lang.AutoCloseable` 接口的 try-with-resource 语法；
- 当程序未被中断时，finally 语句都会被正常执行，并且通常被用于清理系统资源。不应在 finally 语句中 return 方法的执行结果，因为这会导致异常信息的丢失；
- 可以通过 `java.lang.Throwable#Throwable(String, Throwable)` 构造方法，捕获一个异常的同时抛出另一个异常，并且保留原始异常的堆栈信息。Spring Data 正是通过这种方式，将不同 ORM 框架中的不同异常封装成了一个通用的异常结构体系，详情可见 [官方文档](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#orm-introduction) 描述。

---

# 谈谈 final、finally、finalize 有什么不同

finally 关键字是在 try-finally 或 try-catch-finally 语句块中，保证 Java 代码必须被执行的一种机制。另外，若在 finally 语句块中需要关闭实现了 `java.lang.AutoCloseable` 接口的资源，则可以使用 JDK 7 中提供的 try-with-resource 语法简化代码。

需要额外注意的是，以下这段示例代码的 finally 语句块并不会被执行：
```java
try {
    // do something
    System.exit(1);
} finally {
    System.out.println("Print from finally");
}
```

finalize() 是基础类 `java.lang.Object` 的一个方法，它的设计目的在于保证对象被垃圾收集之前，完成特定资源的回收。然而 JVM 在垃圾收集时，需要对实现了 finalize() 的对象进行特殊处理，所以 finalize() 本质上已经成为了垃圾收集的阻碍者，它可能导致对象需要经过多轮垃圾收集周期才能被回收。现在，finalize 机制已经不被推荐使用，并且在 JDK 9 中被标记为 `@Deprecated`。
