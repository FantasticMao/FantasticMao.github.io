---
title: Java泛型的擦除
date: 2017-01-02 20:12:26
categories: Java
---
<blockquote class="blockquote-center">因为只有知道了某个技术不能做到什么，你才能更好地做到所能做的。</blockquote>

本篇记录Java的一个残缺实现，确切地说是Java SE5为向后兼容而采取的折中实现——泛型，记录内容包括基本语法、通配符&边界、泛型擦除。<!-- more -->参考文献：
* 著作书籍：《Java编程思想》15章。
* 官方文档：[http://docs.oracle.com/javase/tutorial/extra/generics/index.html](http://docs.oracle.com/javase/tutorial/extra/generics/index.html)。
* StackOverflow：[http://stackoverflow.com/questions/4343202/difference-between-super-t-and-extends-t-in-java](http://stackoverflow.com/questions/4343202/difference-between-super-t-and-extends-t-in-java)。

## 基本语法
泛型顾名思义*泛化的类型*，实现了*参数化类型*的概念，使代码可以应用于多种类型。

### 泛型类/接口
定义泛型类时，需在类名后添加一个尖括号包裹的*参数类型*。然后在实例化类时，使用*具体类型*替换*参数类型*。在代码中，*参数类型*代表的就是类实例化时的*具体类型*，如下是一个泛型类：
```java
public class Generic<T> {
    private T t;

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }
}
```
泛型接口的定义与泛型类并没什么区别。

### 泛型方法
泛型类对类型的泛化应用于整个类之上，而泛型方法则仅限于当前方法。书中提供了一个编写泛型代码的基本原则：无论何时，只要你能做到，你就应该尽量使用泛型方法。这也意味着我们应尽可能地缩小类型泛化的应用范围。

泛型类对于static方法是无效的，因为JVM在加载static方法时，类还处于初始化状态，类的实例也还未创建，此时static方法自然也无法得知泛型类的实例的*具体类型*。如下例中的类是无法通过编译的：
```java
public class GenericStaticMethod<T> {
    public static void foo(T t) {
        // ...
    }
}
```

但泛型方法对于static方法是生效的，因为方法级别的*参数类型*与类的实例无关，static方法获取*具体类型*也并不依赖于类的实例。定义泛型方法，只需将*参数类型*放置于方法返回值之前即可，如下是修改上例后的泛型方法：
```java
public class GenericStaticMethod {
    public static <T> void foo(T t) {
        // ...
    }
}
```
当类中同时存在类级别和方法级别的*参数类型*时，此时*参数类型*代表的是泛型方法的*具体类型*。调整上例代码：
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
输出结果：`class java.lang.Integer`。

### 多个参数类型
可以为泛型类/接口/方法声明多个*参数类型*，如典型key-value结构的Map
```java
public interface Map<K,V> {
    V get(Object key);

    V put(K key, V value);
}
```

---

## 通配符&边界
使用有界通配符可以指定*参数类型*转换的边界。`<? extends T>`表示一个属于T子类的未知类型，意味着T是?的上界，`<? super T>`表示一个属于T超类的未知类型（包括T），意味着T是?的下界。

---

## 泛型擦除
Java泛型是使用擦除来实现的，这意味着当在使用泛型时，任何*具体类型*的信息都将被擦除 。

### 擦除的实质
泛型机制，可以使*参数类型*灵活地应用于多种*具体类型*，也可以使编译器在编译期就能检查类型转换的正确性。但实际上，泛型代码在由编译器编译之后，存储于字节码中的*参数类型*并不是*具体类型*，而只是一个`Object`类型（不使用边界的情况下），因此`List<Integer>`在编译之后实际存储的只是`List<Object>`，`new ArrayList<Integer>().getClass() == new ArrayList<String>().getClass()`的执行结果也会是true。这种现象叫做泛型的擦除，正如官方文档中所说的那样：

> It is misleading, because the declaration of a generic is never actually expanded in this way. There aren't multiple copies of the code--not in source, not in binary, not on disk and not in memory. If you are a C++ programmer, you'll understand that this is very different than a C++ template.

反汇编例1.1的[Generic泛型类](#泛型类-接口)中的代码，结果如下。这也证实了在字节码文件中存储的*具体类型*确实只是`Object`类型。
![images](http://ogvr8n3tg.bkt.clouddn.com/Java%E6%B3%9B%E5%9E%8B%E7%9A%84%E6%93%A6%E9%99%A4/1.png)

由于擦除，在不指定边界的情况下，泛型代码中的*参数类型*只能被作为`Object`类型使用，我们也无法获取任何有关*参数类型*的信息，编译器将会承担转换`Object`类型成*具体类型*的任务，就好似[Generic泛型类](#泛型类-接口)的代码是如下这样编写的：
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

而使用了边界的*参数类型*，则可以指定擦除的边界。将[Generic泛型类](#泛型类-接口)的*参数类型*调整为`<T extends Number>`，然后再次进行返汇编，结果如下。可见*参数类型*确实仅是被擦除到`Number`类型，因此我们也可以在泛型代码中调用`Number`的属性或方法。
![images](http://ogvr8n3tg.bkt.clouddn.com/Java%E6%B3%9B%E5%9E%8B%E7%9A%84%E6%93%A6%E9%99%A4/2.png)

### 擦除的代价
泛型作为Java SE5才出现的特性，不仅必须向后兼容，即能使Java SE5之前的代码依旧语法正确，还必须支持迁移兼容性，即能使泛化的应用代码和非泛化JDK类库相互兼容。Java设计者们最终选择采用擦除来实现泛型。擦除可允许泛化代码和非泛化代码共存，能在不破坏现有类库的情况下，使得非泛化的代码迁移到泛化的代码。

同时，选择擦除的代价也是显著的。因为擦除的存在，泛型代码在运行时所有关于*具体类型*的信息都丢失了，也因此不能顺利地对*参数类型*执行一些操作，如下例：
```java
public class Erased<T> {
    private T t;

    public void foo() {
        boolean f = t instanceof Integer; // ok
        this.t = new T(); // error
        T[] array1 = new T[10]; // error
        T[] array2 = (T[]) new Object[10]; // unchecked cast
    }
}
```

对上述两个问题的补偿：1. 可以通过显示地传入类的Class对象，并使用反射来替换*参数类型*的`new`表达式。2. 可以使用`List<T>`替换泛型数组。调整上例代码：
```java
public class Erased<T> {
    private T t;

    public void foo(Class<T> clazz) throws Exception {
        boolean f = t instanceof Integer; // ok
        this.t = clazz.newInstance(); // ok
        List<T> array1 = new ArrayList<>(); // ok
    }
}
```

---

## 总结
在Java中，运用泛型机制可以编写更抽象的代码，这也正式是泛型最吸引人的地方。但Java的泛型相比于C++等其他语言的确存在显著的缺陷，不过它也并没那么糟糕。正确地理解擦除，恰当地使用边界，泛型将对于编写健壮的Java代码非常具有意义。
