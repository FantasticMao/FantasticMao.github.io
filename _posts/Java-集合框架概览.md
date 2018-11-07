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
HashMap 使用一个 `transient Node<K, V>[] table` 字段存储 key-value 键值对的数据，`Node<K, V>` 默认情况下是 HashMap.Node 链表类型，特殊情况下是 HashMap.TreeNode 红黑树类型。因为 HashMap.TreeNode 间接继承了 HashMap.Node，所以这两种类型可以同时适应 `table` 字段的类型。

HashMap.Node 实现了 Map.Entry 接口，它为 HashMap 保存了 key-value 元素的基本数据和经过 `hash(object)` 方法计算的 key 的散列值。除此之外，HashMap.Node 作为链表的一个节点，还保存了它在链表中的下一个节点的引用值。HashMap.Node 类的结构如下图所示：
![images](/images/Java集合框架概览/1.png)

HashMap.TreeNode 间接继承了 HashMap.Node。当 HashMap.Node 类型的链表长度大于 HashMap 定义的固定值 `TREEIFY_THRESHOLD` 时，链表就会被 `treeifyBin(Node<K,V>[], int)` 方法转置成 HashMap.TreeNode 类型的红黑树。（红黑树的数据结构略显复杂，此处暂不讨论）

综上所述，HashMap 中所涉及的数据结构，大致如下图所示：
![images]()

HashMap 为了将元素均匀地分布在 `table` 数组上（即避免哈希碰撞），使用元素 key 的哈希值进行了一系列的位运算，最终确定元素的数组下标。HashMap 计算元素的数组下标的关键代码如下图所示：
![images](/images/Java集合框架概览/2.png)

![images](/images/Java集合框架概览/3.png)

总结得出，在 HashMap 中计算元素的数组下标的过程为：
1. 当 key == null 时，数组下标为 0；
2. 当 key != null 时，数组下标为 (table.length - 1) & (key.hashCode() ^ (key.hashCode() >>> 16))。

HashMap get 方法的关键代码如下图所示：


HashMap get 方法的关键步骤：
1. 根据 key，计算元素在 Node 数组中的下标；
2. 根据数组下标，获取 key 对应的 Node 数组中的节点，判断该节点 key 是否与目标元素 key 相等，若相等则直接返回；
3. 在 2 获取的 Node 节点之上，依据不同的节点类型（链表或红黑树），使用不同的迭代方式查找目标元素。若找到则返回。判断每个节点 key 是否与目标元素 key 相等，若相等则直接返回；
4. 2 和 3 均失败的情况下，返回 null。

HashMap 的 put 方法

HashMap 的 resize 方法

关于 HashMap，有几点是需要注意一下的：
1. HashMap 不会保证元素的插入顺序；
2. HashMap 没有任何线程安全的保障；
3. HashMap 允许 key 和 value 是 `null`；
4. HashMap 使用优化：根据实际情况指定 `initialCapacity` 和 `loadFactor` 构造函数、优化 key 的 `hashCode()`，尽可能地避免哈希碰撞。


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