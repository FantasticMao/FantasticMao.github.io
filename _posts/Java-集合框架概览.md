---
title: Java 集合框架概览
date: 2018-10-18 22:00:34
categories: 编程
tags:
- Java
---
本篇文章记录我所理解和掌握的在 JDK8 Collection Library 中的常用集合类，及其所涉及的数据结构和实现原理。<!-- more -->

---

# Map

## HashMap
HashMap 使用一个 `transient Node<K, V>[] table` 字段存储 Key-Value 键值对的数据，`Node<K, V>` 默认情况下是 HashMap.Node（链表），特殊情况下是 HashMap.TreeNode（红黑树）。因为 HashMap.TreeNode 间接继承了 HashMap.Node，所以这两种类型可以同时适应 `table` 字段的类型。

HashMap.Node 实现了 Map.Entry 接口，它为 HashMap 保存了 Key-Value 元素的基本数据和经过 `hash(object)` 方法计算的 Key 的散列值。除此之外，HashMap.Node 作为链表的一个节点，它还保存了它在链表中的下一个节点的引用值。HashMap.Node 类的结构如下图所示：
![images](/images/Java集合框架概览/1.png)

HashMap.TreeNode 间接继承了 HashMap.Node，当属于 HashMap.Node 类型的链表长度大于 HashMap 定义的固定值 `TREEIFY_THRESHOLD` 时，链表就会被 `treeifyBin(Node<K,V>[], int)` 方法转换成 HashMap.TreeNode 类型的红黑树。（红黑树的数据结构略显复杂，此处暂不讨论）

综上所述，HashMap 中所涉及的数据结构，可以大致用下图表示：
![images]()

HashMap 为了将元素均匀地分布在 `table` 数组上（即避免哈希碰撞），使用元素 Key 的哈希值，进行了一系列的位运算。HashMap 计算元素的数组下标方法如下图所示：
![images]()

HashMap 的 put 方法

HashMap 的 get 方法

HashMap 的 resize 方法

关于 HashMap，有几点是需要注意一下的：
1. HashMap 不会保证元素的插入顺序。
2. HashMap 没有任何线程安全的保障。
3. HashMap 允许 Key 和 value 是 `null`。
4. HashMap 使用优化：根据实际情况指定 `initialCapacity` 和 `loadFactor` 构造函数、优化 Key 的 `hashCode()`，尽可能地避免哈希碰撞。


---

## LinkedHashMap

## TreeMap

## Hashtable

## ConcurrentHashMap

## ConcurrentSkipListMap

## WeakHashMap

# List

## ArrayList

## LinkedList

## Vector

## Stack

## CopyOnWriteArrayList

# Set

## HashSet

## LinkedHashSet

## TreeSet

## CopyOnWriteArraySet

## ConcurrentSkipListSet

# Queue