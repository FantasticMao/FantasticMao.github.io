---
title: 写给 LinkedHashMap 的注释
date: 2018-06-27 21:09:06
categories: 编程
tags:
- Java
---
基于 JDK8 为 LinkedHashMap 编写个人理解的注释。<!-- more -->

```java
package java.util;

......

// LinkedHashMap 类继承自 HashMap
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
{
    // LinkedHashMap 内部节点，继承 HashMap.Node 类
    static class Entry<K,V> extends HashMap.Node<K,V> {
        // 增加 before 和 after 字段，用于记录 Entry 节点在双向链表中的上一个节点和下一个节点
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }

    // Entry 节点，记录 LinkedHashMap 双向链表的第一个节点（也是 eldest 节点）
    transient LinkedHashMap.Entry<K,V> head;

    // Entry 节点，记录 LinkedHashMap 双向链表的最后一个节点（也是 youngest 节点）
    transient LinkedHashMap.Entry<K,V> tail;

    // 定义遍历 LinkedHashMap 内部节点排序方式
    final boolean accessOrder;

    private void linkNodeLast(LinkedHashMap.Entry<K,V> p) {
        // 在添加新的 Entry 节点之前，保存 LinkedHashMap 双向链表的「最后一个节点」为局部变量 last
        // last 变量表示在添加新的 Entry 节点之后，LinkedHashMap 双向链表的「上一个最后一个节点」
        LinkedHashMap.Entry<K,V> last = tail;

        // 开始添加新的 Entry 节点至 LinkedHashMap 节点

        // 将 LinkedHashMap 双向链表的「最后一个节点」赋值为新添加的 Entry 节点
        tail = p;
        if (last == null)
            // 若 LinkedHashMap 双向链表的「上一个最后一个节点」不存在，则表示双向链表为空，并将双向链表的「第一个节点」也赋值为新添加的 Entry 节点
            head = p;
        else {
            // 若 LinkedHashMap 双向链表的「上一个最后一个节点」存在，则表示双向链表不为空，并将新添加的 Entry 节点的「上一个节点」链接到双向链表的「上一个最后一个节点」，将双向链表的「上一个最后一个节点」的「下一个节点」链接到新添加的 Entry 节点
            p.before = last;
            last.after = p;
        }
    }

    // 将源 Entry 节点替换为目标 Entry 节点，并更新 LinkedHashMap 双向链表
    private void transferLinks(LinkedHashMap.Entry<K,V> src,
                               LinkedHashMap.Entry<K,V> dst) {
        LinkedHashMap.Entry<K,V> b = dst.before = src.before;
        LinkedHashMap.Entry<K,V> a = dst.after = src.after;
        if (b == null)
            head = dst;
        else
            b.after = dst;
        if (a == null)
            tail = dst;
        else
            a.before = dst;
    }

    // 重新初始化 LinkedHashMap
    void reinitialize() {
        super.reinitialize();
        // 将LinkedHashMap 双向链表也置空
        head = tail = null;
    }

    // 重写 newNode(int, K, V, Node<K, V>)
    Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
        LinkedHashMap.Entry<K,V> p =
            new LinkedHashMap.Entry<K,V>(hash, key, value, e);
        // 将新生成的 Entry 节点关联到 LinkedHashMap 双向链表中
        linkNodeLast(p);
        return p;
    }

    // 重写 replacementNode(Node<K, V>, Node<K, V>)
    Node<K,V> replacementNode(Node<K,V> p, Node<K,V> next) {
        LinkedHashMap.Entry<K,V> q = (LinkedHashMap.Entry<K,V>)p;
        LinkedHashMap.Entry<K,V> t =
            new LinkedHashMap.Entry<K,V>(q.hash, q.key, q.value, next);
        // 将需替换的 Entry 节点关联到 LinkedHashMap 双向链表中
        transferLinks(q, t);
        return t;
    }

    // 重写 newTreeNode(int, K, V, Node<K, V>)
    TreeNode<K,V> newTreeNode(int hash, K key, V value, Node<K,V> next) {
        TreeNode<K,V> p = new TreeNode<K,V>(hash, key, value, next);
        // 将新生成的 Entry 节点关联到 LinkedHashMap 双向链表中
        linkNodeLast(p);
        return p;
    }

    // 重写 replacementTreeNode(Node<K, V>, Node<K, V>)
    TreeNode<K,V> replacementTreeNode(Node<K,V> p, Node<K,V> next) {
        LinkedHashMap.Entry<K,V> q = (LinkedHashMap.Entry<K,V>)p;
        TreeNode<K,V> t = new TreeNode<K,V>(q.hash, q.key, q.value, next);
        // 将需替换的 Entry 节点关联到 LinkedHashMap 双向链表中
        transferLinks(q, t);
        return t;
    }

    // 删除 HashMap.Node 节点时，更新 LinkedHashMap 双向链表
    void afterNodeRemoval(Node<K,V> e) { // unlink
        LinkedHashMap.Entry<K,V> p =
            (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
        p.before = p.after = null;
        if (b == null)
            head = a;
        else
            b.after = a;
        if (a == null)
            tail = b;
        else
            a.before = b;
    }

    // 在插入 HashMap.Node 节点时，更新 LinkedHashMap 双向链表
    void afterNodeInsertion(boolean evict) { // possibly remove eldest
        LinkedHashMap.Entry<K,V> first;
        // 若需要在插入节点时，删除 LinkedHashMap 双向链表的 eldest 节点，可以通过重写 removeEldestEntry(Map.Entry<K, V>) 并返回结果 true 实现
        if (evict && (first = head) != null && removeEldestEntry(first)) {
            K key = first.key;
            removeNode(hash(key), key, null, false, true);
        }
    }

    // 若 LinkedHashMap 内部按访问顺序排序，则在获取 HashMap.Node 时，更新 LinkedHashMap 双向链表
    void afterNodeAccess(Node<K,V> e) { // move node to last
        LinkedHashMap.Entry<K,V> last;
        if (accessOrder && (last = tail) != e) {
            LinkedHashMap.Entry<K,V> p =
                (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
            p.after = null;
            if (b == null)
                head = a;
            else
                b.after = a;
            if (a != null)
                a.before = b;
            else
                last = b;
            if (last == null)
                head = p;
            else {
                p.before = last;
                last.after = p;
            }
            tail = p;
            ++modCount;
        }
    }

    public LinkedHashMap(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor);
        accessOrder = false;
    }

    public LinkedHashMap(int initialCapacity) {
        super(initialCapacity);
        accessOrder = false;
    }

    public LinkedHashMap() {
        super();
        accessOrder = false;
    }

    public LinkedHashMap(Map<? extends K, ? extends V> m) {
        super();
        accessOrder = false;
        putMapEntries(m, false);
    }

    public LinkedHashMap(int initialCapacity,
                         float loadFactor,
                         boolean accessOrder) {
        super(initialCapacity, loadFactor);
        // 指定 LinkedHashMap 内部排序方式：true 访问顺序，false 插入顺序
        this.accessOrder = accessOrder;
    }

    // 重写 containsValue(Object)，从 LinkedHashMap 双向链表的第一个节点开始遍历，判断 Value 是否存在
    public boolean containsValue(Object value) {
        for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after) {
            V v = e.value;
            if (v == value || (value != null && value.equals(v)))
                return true;
        }
        return false;
    }

    // 重写 get(Object)，根据 Key 获取 Value
    public V get(Object key) {
        Node<K,V> e;
        // 使用 HashMap.getNode(int, Object) 获取节点
        if ((e = getNode(hash(key), key)) == null)
            // LinkedHashMap 允许 Key 为 null
            return null;
        if (accessOrder)
            afterNodeAccess(e);
        return e.value;
    }

    // LinkedHashMap 没有重写 put(K, V)

    ......
}
```
