---
title: Java泛型的擦除
date: 2017-01-02 20:12:26
categories: Java
---
<blockquote class="blockquote-center">因为只有知道了某个技术不能做到什么，你才能更好地做到所能做的。——《Java编程思想》</blockquote>

本篇记录Java的一个残缺实现，确切地说是Java SE5为向后兼容而采取的折中实现：泛型的擦除。<!-- more -->参考文献：《Java编程思想》15章。

## 引言
泛型顾名思义*泛化的类型*，实现了*参数化类型*的概念，使代码可以应用于多种类型。泛型可以简单地指定容器持有的类型，如`List<String> list = new ArrayList<>();`，这也是笔者早期唯一知道的泛型用法。
![image](http://ogvr8n3tg.bkt.clouddn.com/Java%E6%B3%9B%E5%9E%8B%E7%9A%84%E6%93%A6%E9%99%A4/1.png)

## 语法

### 泛型类/接口
定义泛型类时，需在类名后添加一个*类型参数*，并用尖括号括住。然后在实例化类时，使用*具体类型*替换*类型参数*。在泛型类中，*泛型参数*代表的就是实例化类时的*具体类型*，如下是一个泛型类：
```java
public class Generic<T> {
    private T t;

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }

    public static void main(String[] args) {
        Generic<String> generic = new Generic<>();
        generic.setT("item");
        String s = generic.getT();
    }
}
```
泛型接口的定义与泛型类并没什么区别。

### 泛型方法
泛型类对类型的泛化应用于整个类之上，而泛型方法则仅限于当前方法。书中提供了一个编写泛型代码的基本原则：**无论何时，只要你能做到，你就应该尽量使用泛型方法**。这也意味着我们需应尽可能地缩小类型泛化的应用范围。

**泛型类对于static方法是无效的**，如下例中的类是无法通过编译的：
```java
public class GenericStaticMethod<T> {
    public static void foo(T t) {
        // ...
    }
}
```
因为JVM在加载static方法时，类还处于初始化状态，类的实例也还未创建，此时static方法自然也无法得知泛型类的实例的*具体类型*。

但**泛型方法对于static方法是生效的**，因为方法级别的*泛型参数*与类的实例无关，static方法获取*具体类型*也并不依赖于类的实例。定义泛型方法，只需将*泛型参数*放置于方法返回值之前即可，如下例是修改上例后的泛型方法：
```java
public class GenericStaticMethod {
    public static <T> void foo(T t) {
        // ...
    }
}
```
当类中同时存在类级别和方法级别的*泛型参数*时，此时方法中的*泛型参数*代表的是泛型方法的*具体类型*。调整上例代码：
```java
public class GenericStaticMethod<T> {
    public static <T> void foo(T t) {
        System.out.println(t.getClass());
    }

    public static void main(String[] args) {
        new GenericStaticMethod<String>().foo(1);
    }
}
```
输出：`class java.lang.Integer`。同时可见，若传入的*泛型参数*是基本类型，则Java的自动打包机制就会介入。

## 擦除

## 边界

## 扩展
