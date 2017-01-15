---
title: Java泛型的擦除
date: 2017-01-02 20:12:26
categories: Java
---
<blockquote class="blockquote-center">因为只有知道了某个技术不能做到什么，你才能更好地做到所能做的。——《Java编程思想》</blockquote>

本篇记录Java的一个残缺实现，确切地说是Java SE5为向后兼容而采取的折中实现：泛型的擦除。<!-- more -->参考文献：《Java编程思想》15章。

## 简单语法
泛型顾名思义*泛化的类型*，实现了*参数化类型*的概念，使代码可以应用于多种类型。

### 泛型类/接口
定义泛型类时，需在类名后添加一个尖括号包裹的*类型参数*。然后在实例化类时，使用*具体类型*替换*类型参数*。在泛型类中，*类型参数*代表的就是实例化类时的*具体类型*，如下是一个泛型类：
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
泛型类对类型的泛化应用于整个类之上，而泛型方法则仅限于当前方法。书中提供了一个编写泛型代码的基本原则：**无论何时，只要你能做到，你就应该尽量使用泛型方法**。这也意味着我们应尽可能地缩小类型泛化的应用范围。

**泛型类对于static方法是无效的**，如下例中的类是无法通过编译的：
```java
public class GenericStaticMethod<T> {
    public static void foo(T t) {
        // ...
    }
}
```
因为JVM在加载static方法时，类还处于初始化状态，类的实例也还未创建，此时static方法自然也无法得知泛型类的实例的*具体类型*。

但**泛型方法对于static方法是生效的**，因为方法级别的*类型参数*与类的实例无关，static方法获取*具体类型*也并不依赖于类的实例。定义泛型方法，只需将*类型参数*放置于方法返回值之前即可，如下是修改上例后的泛型方法：
```java
public class GenericStaticMethod {
    public static <T> void foo(T t) {
        // ...
    }
}
```
当类中同时存在类级别和方法级别的*类型参数*时，此时*类型参数*代表泛型方法的*具体类型*。调整上例代码：
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
输出：`class java.lang.Integer`。同时可见，若传入的*类型参数*是基本类型，则Java的自动打包机制就会介入。

### 多个类型参数
可以为泛型类/接口/方法声明多个*类型参数*，如典型的Map
```java
public interface Map<K,V> {
    V get(Object key);

    V put(K key, V value);
}
```

### 边界&逆变&通配符
泛型类型的转换，起始都是由编译器做保证。边界`extends`可以指定编译器在泛型擦除中，
逆变`super`
通配符`?`
```java
interface Animals {
}

class Bird implements Animals {
    void fly() {
    }
}

class Fish implements Animals {
    void swim() {
    }
}

class LanternFish extends Fish {
    void light() {
    }
}
```

## 擦除
无界泛型的元素在运行时，都将被擦除成Object类。
Java泛型中对类型的安全转换，其实都是由编译器来处理了。如例1.1[泛型类/接口](#泛型类-接口)中的代码，由编译器转换后，与如下代码无异：
```java
public class Generic<T> {
    private Object t;

    public T getT() {
        return (T) t;
    }

    public void setT(Object t) {
        this.t = t;
    }
}
```
