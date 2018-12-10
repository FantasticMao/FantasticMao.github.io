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
HashMap 使用一个 `transient Node<K, V>[] table` 字段存储 key-value 键值对的数据，`Node<K, V>` 默认情况下的 HashMap.Node 是链表类型，特殊情况下是 HashMap.TreeNode 红黑树类型。因为 HashMap.TreeNode 间接继承了 HashMap.Node，所以这两种类型可以同时适用于上述的 `table` 字段。

HashMap.Node 实现了 Map.Entry 接口，它为 HashMap 保存了 key-value 元素的基本数据和经过 `hash(object)` 方法计算的 key 的散列值。除此之外，HashMap.Node 作为链表的节点，它还保存了它的下一个节点的引用。HashMap.Node 类的字段如下图所示：
![image](/images/Java集合框架概览/HashMapEntry.png)

HashMap.TreeNode 间接继承了 HashMap.Node。当 HashMap.Node 类型的链表长度大于 HashMap 定义的常量 `TREEIFY_THRESHOLD` 时，链表就会被 `treeifyBin(Node<K,V>[], int)` 方法转置成 HashMap.TreeNode 类型的红黑树。（红黑树的数据结构此处暂不讨论）

综上所述，HashMap 中所涉及的数据结构，大致如下图所示：
![image]()

### 计算数组下标
HashMap 为了将元素均匀地分散在 `table` 数组上（避免哈希碰撞），使用元素 key 的哈希值进行了一系列的位运算，从而最终确定元素的数组下标。HashMap 计算元素的数组下标的关键代码如下图所示：
![image](/images/Java集合框架概览/HashMapHash.png)

阅读源码可以得出，在 HashMap 中计算元素的数组下标逻辑为：
* 当 key == null 时，数组下标为 0；
* 当 key != null 时，数组下标为 (table.length - 1) & (key.hashCode() ^ (key.hashCode() >>> 16))。

### get 方法
HashMap get 方法的关键代码如下图所示：
![image](/images/Java集合框架概览/HashMapGet.png)

HashMap get 方法的关键步骤：
1. 根据 key，计算元素的数组下标；
2. 根据数组下标，获取对应的 Node 节点，判断该节点的哈希值和 key 是否与目标节点的哈希值和 key 匹配，若匹配成功则直接返回；
3. 若在 2 中匹配节点失败，则获取当前节点的下一个节点，依据当前节点的类型（链表或红黑树），使用不同方式遍历后续的所有节点，查找与目标节点的哈希值和 key 匹配的节点。若匹配成功则直接返回对应的节点；
4. 若在 2 和 3 中匹配节点失败，则表示目标元素不存在。get 方法最终返回的目标元素为 null。

### put 方法
HashMap put 方法的关键代码如下图所示：
![image](/images/Java集合框架概览/HashMapPut.png)

HashMap put 方法的关键步骤：
1. 根据 key，计算元素的数组下标；
2. 根据数组下标，获取对应的 Node 节点，判断该节点是否为 null，若节点为 null 则直接为该节点赋值；
3. 若在 2 中的节点不为 null，则判断当前节点的哈希值和 key 是否与待插入节点的哈希值和 key 匹配，若匹配成功则获取该节点；
4. 若在 3 中匹配节点失败，则依据该节点的类型（链表或红黑树），使用不同方式遍历后续的所有节点，查找与待插入节点的哈希值和 key 匹配的节点。若匹配成功则获取对应的节点，如果匹配失败则为最后一个节点的下一个节点赋值。当在链表类型的节点中查找时，若发现链表长度大于或等于 HashMap 定义的常量 `TREEIFY_THRESHOLD` 时，则需将当前节点的类型由链表转置为红黑树；
5. 若在 3 或 4 中获取节点成功，则表示此次插入操作是替换 HashMap 中的旧元素，需要使用待插入元素的新值替换旧元素的旧值，并直接返回旧值；
6. 若在 3 和 4 中匹配节点失败，则表示此次插入操作是新插入元素，需要更新 HashMap 中统计信息的相关字段，并判断插入新元素后的 HashMap 是否需要扩容。此时 put 方法最终返回的旧元素值为 null。

### resize 方法
HashMap resize 方法的关键代码如下图所示：

### 注意事项
关于 HashMap，有几点是需要注意一下的：
1. HashMap 不保障线程安全；
2. HashMap 允许 key 和 value 是 null；
3. HashMap 以哈希算法确定元素的位置，不会保证元素的插入顺序；
4. 可以通过优化 HashMap 持有元素的 `hashCode()`，从而降低哈希碰撞的可能性。


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