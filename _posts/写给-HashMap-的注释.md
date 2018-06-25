---
title: 写给 HashMap 的注释
date: 2018-06-25 21:21:36
categories: 编程
tags:
- Java
---
基于 JDK8 为 HashMap 实现编写个人理解的注释。<!-- more -->

```
package java.util;

......


public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {

        // Node 数组的默认容量
        static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

        // Node 数组的最大容量
        static final int MAXIMUM_CAPACITY = 1 << 30;

        // Node 数组的加载因子
        static final float DEFAULT_LOAD_FACTOR = 0.75f;

        // Node 节点由 LinkedList 转换为 Tree 的临界值
        static final int TREEIFY_THRESHOLD = 8;

        // Node 节点由 Tree 转换为 LinkedList 的临界值
        static final int UNTREEIFY_THRESHOLD = 6;

        // Node 节点由 LinkedList 转换为 Tree 时，Node 数组的最小容量
        static final int MIN_TREEIFY_CAPACITY = 64;

        // HashMap 内部节点，实现 Map.Entry 接口，用于存放 HashMap 的 Key-Value 数据
        static class Node<K,V> implements Map.Entry<K,V> {
            final int hash;
            final K key;
            V value;
            Node<K,V> next;

            Node(int hash, K key, V value, Node<K,V> next) {
                this.hash = hash;
                this.key = key;
                this.value = value;
                this.next = next;
            }

            public final int hashCode() {
                // 计算 Node 节点的 hashCode
                return Objects.hashCode(key) ^ Objects.hashCode(value);
            }

            ......
        }

        // 计算 Key 的 hashCode
        static final int hash(Object key) {
            int h;
            // 1. h1 = key.hashCode()
            // 2. h2 = key.hashCode() >>> 16
            // 3. h1 ^ h2
            return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
        }

        ......

        // Node 数组，每个 Node 节点可能为 LinkedList 或 Tree，两者之间可以互相转换
        transient Node<K,V>[] table;

        // 冗余字段，由 keySet() 和 values() 调用
        transient Set<Map.Entry<K,V>> entrySet;

        // 冗余字段，统计 HashMap 内部的 Node 节点的个数
        // 当 size > threshold 时，Node 数组将会调用 resize() 扩容
        transient int size;

        // 冗余字段，可抛出 ConcurrentModificationException，用于避免同时迭代和修改 Node 数组
        transient int modCount;

        // Node 数组的扩容阈值，threshold = capacity * loadFactor
        int threshold;

        // Node 数组的加载因子
        final float loadFactor;

        public HashMap(int initialCapacity, float loadFactor) {
            ......
        }

        public HashMap(int initialCapacity) {
            ......
        }

        public HashMap() {
        }

        ......

        // 根据 Key 获取 Value
        public V get(Object key) {
            Node<K,V> e;
            // HashMap 允许 Key 为 null
            return (e = getNode(hash(key), key)) == null ? null : e.value;
        }

        // 根据 Key 的 hashCode，从 Node 数组中获取 Node 节点
        final Node<K,V> getNode(int hash, Object key) {
            Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
            if ((tab = table) != null && (n = tab.length) > 0 &&
                // 存储在 Node 数组中的索引值为 (table.lenth - 1) & hashCode
                (first = tab[(n - 1) & hash]) != null) {
                // 判断 Key 是否匹配的条件：Node的哈希值相等 && key的引用相等 || key的equals()方法执行成功
                if (first.hash == hash && // always check first node
                    ((k = first.key) == key || (key != null && key.equals(k))))
                    return first;
                if ((e = first.next) != null) {
                    if (first instanceof TreeNode)
                        // 当 Node 节点是 Tree
                        return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                    do {
                        // 当 Node 节点是 LinkedList
                        if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                            return e;
                    } while ((e = e.next) != null);
                }
            }
            return null;
        }

        // 判断 Key 是否存在
        public boolean containsKey(Object key) {
            // 根据 Key 获取 Node 节点不为 null
            return getNode(hash(key), key) != null;
        }

        // 向 Node 数组添加 Key-Value 键值对
        public V put(K key, V value) {
            return putVal(hash(key), key, value, false, true);
        }

        // 根据 Key 的 hashCode，向 Node 数组添加 Key-Value 键值对
        final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                       boolean evict) {
            Node<K,V>[] tab; Node<K,V> p; int n, i;
            if ((tab = table) == null || (n = tab.length) == 0)
                // 初始化 Node 数组
                n = (tab = resize()).length;
            // 存储在 Node 数组中的索引值为 (table.lenth - 1) & hashCode
            if ((p = tab[i = (n - 1) & hash]) == null)
                // 若 Node 节点不存在，则创建节点
                tab[i] = newNode(hash, key, value, null);
            else {
                Node<K,V> e; K k;
                // 判断 Key 是否匹配的条件：Node的哈希值相等 && key的引用相等 || key的equals()方法执行成功
                if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                    // 替换 Node 节点
                    e = p;
                else if (p instanceof TreeNode)
                    // 当 Node 节点是 Tree
                    e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
                else {
                    // 当 Node 节点是 LinkedList
                    for (int binCount = 0; ; ++binCount) {
                        if ((e = p.next) == null) {
                            p.next = newNode(hash, key, value, null);
                            // 当 LinkedList 深度 >= TREEIFY_THRESHOLD - 1，则将 LinkedList 转置为 Tree
                            if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                                treeifyBin(tab, hash);
                            break;
                        }
                        // 遍历 LinkedList 获取 Node 节点
                        if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                            break;
                        p = e;
                    }
                }
                if (e != null) { // existing mapping for key
                    V oldValue = e.value;
                    // 当 Value == null 的情况下赋值
                    if (!onlyIfAbsent || oldValue == null)
                        e.value = value;
                    afterNodeAccess(e);
                    return oldValue;
                }
            }
            // 记录修改一次 Node 数组
            ++modCount;
            // 当 size > threshold 时，扩容 Node 数组
            if (++size > threshold)
                resize();
            afterNodeInsertion(evict);
            return null;
        }

        final Node<K,V>[] resize() {
            Node<K,V>[] oldTab = table;
            int oldCap = (oldTab == null) ? 0 : oldTab.length;
            int oldThr = threshold;
            int newCap, newThr = 0;
            if (oldCap > 0) {
                if (oldCap >= MAXIMUM_CAPACITY) {
                    threshold = Integer.MAX_VALUE;
                    return oldTab;
                }
                // Node 数组的新容量为旧容量了 * 2
                else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                        oldCap >= DEFAULT_INITIAL_CAPACITY)
                    // Node 数组的扩容阈值 * 2
                    newThr = oldThr << 1; // double threshold
            }
            else if (oldThr > 0) // initial capacity was placed in threshold
                newCap = oldThr;
            else {               // zero initial threshold signifies using defaults
                newCap = DEFAULT_INITIAL_CAPACITY;
                newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
            }
            if (newThr == 0) {
                float ft = (float)newCap * loadFactor;
                newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                        (int)ft : Integer.MAX_VALUE);
            }
            threshold = newThr;
            @SuppressWarnings({"rawtypes","unchecked"})
            // 创建新的 Node 数组，容量为旧数组的两倍
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
            table = newTab;
            if (oldTab != null) {
                // 迁移数据，并保证节点顺序
                for (int j = 0; j < oldCap; ++j) {
                    Node<K,V> e;
                    if ((e = oldTab[j]) != null) {
                        oldTab[j] = null;
                        if (e.next == null)
                            // LinedList 或者 Tree 节点没有下一个节点
                            newTab[e.hash & (newCap - 1)] = e;
                        else if (e instanceof TreeNode)
                            // 当 Node 节点是 Tree
                            ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                        else { // preserve order
                            // 当 Node 节点是 LinkedList
                            Node<K,V> loHead = null, loTail = null;
                            Node<K,V> hiHead = null, hiTail = null;
                            Node<K,V> next;
                            do {
                                next = e.next;
                                // 此处实现设计得很巧妙
                                // 根据 Key.hashCode & oldCap == 0，来区分迁移数据
                                if ((e.hash & oldCap) == 0) {
                                    if (loTail == null)
                                        loHead = e;
                                    else
                                        loTail.next = e;
                                    loTail = e;
                                }
                                else {
                                    if (hiTail == null)
                                        hiHead = e;
                                    else
                                        hiTail.next = e;
                                    hiTail = e;
                                }
                            } while ((e = next) != null);
                            // 1. 若 Key.hashCode & oldCap == 0，则新 Node 节点在新数组中的索引为原索引
                            // 2. 若 Key.hashCode & oldCap != 0，则新 Node 节点在新数组中的索引为原索引 + 旧数组容量（即原索引 * 2）
                            if (loTail != null) {
                                loTail.next = null;
                                newTab[j] = loHead;
                            }
                            if (hiTail != null) {
                                hiTail.next = null;
                                newTab[j + oldCap] = hiHead;
                            }
                        }
                    }
                }
            }
            return newTab;
        }
}
```
