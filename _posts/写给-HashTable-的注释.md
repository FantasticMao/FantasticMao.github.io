---
title: 写给 HashTable 的注释
date: 2018-06-26 02:00:00
categories: 编程
tags:
- Java
---
基于 JDK8 为 HashTable 实现编写个人理解的注释。<!-- more -->

```java
package java.util;

...

public class Hashtable<K,V>
    extends Dictionary<K,V>
    implements Map<K,V>, Cloneable, java.io.Serializable {

    // Entry 数组，每个 Entry 节点都是一个 LinkedList
    private transient Entry<?,?>[] table;

    // 冗余字段，统计 HashTable 内部的 Entry 节点个数
    private transient int count;

    // Entry 数组的扩容阈值，threshold = capacity * loadFactor
    private int threshold;

    // Entry 数组的加载因子
    private float loadFactor;

    // 冗余字段，用于抛出 ConcurrentModificationException，为了避免同时迭代和修改 Node 数组
    private transient int modCount = 0;

    public Hashtable(int initialCapacity, float loadFactor) {
        ......
    }

    public Hashtable(int initialCapacity) {
        ......
    }

    public Hashtable() {
        ......
    }

    ......

    // 同步根据 Key 获取 Value
    @SuppressWarnings("unchecked")
    public synchronized V get(Object key) {
        Entry<?,?> tab[] = table;
        // 计算 Key 的 hashCode
        int hash = key.hashCode();
        // 存储在 Entry 数组中的索引值为 (hashCode & 0x7FFFFFFF) % table.length
        int index = (hash & 0x7FFFFFFF) % tab.length;
        // 遍历 Entry 节点，Node 节点不为 null
        for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
            // 判断 Key 是否匹配的条件：Entry的哈希值相等 && Key的equals()执行成功
            if ((e.hash == hash) && e.key.equals(key)) {
                return (V)e.value;
            }
        }
        return null;
    }

    // 同步向 Entry 数组添加 Key-Value 键值对
    public synchronized V put(K key, V value) {
        // Make sure the value is not null
        if (value == null) {
            // Node 节点不为 null
            throw new NullPointerException();
        }

        // Makes sure the key is not already in the hashtable.
        Entry<?,?> tab[] = table;
        int hash = key.hashCode();
        // 存储在 Entry 数组中的索引值为 (hashCode & 0x7FFFFFFF) % table.length
        int index = (hash & 0x7FFFFFFF) % tab.length;
        @SuppressWarnings("unchecked")
        Entry<K,V> entry = (Entry<K,V>)tab[index];
        for(; entry != null ; entry = entry.next) {
            // 判断 Key 是否匹配的条件：Entry的哈希值相等 && Key的equals()执行成功
            if ((entry.hash == hash) && entry.key.equals(key)) {
                V old = entry.value;
                // 替换 Entry 节点
                entry.value = value;
                return old;
            }
        }

        addEntry(hash, key, value, index);
        return null;
    }

    // 根据 Key 的 hashCode，向 Entry 数组添加 Key-Value 键值对
    private void addEntry(int hash, K key, V value, int index) {
        // 记录修改一次 Entry 数组
        modCount++;

        Entry<?,?> tab[] = table;
        if (count >= threshold) {
            // Rehash the table if the threshold is exceeded
            // 当 count >= threshold 时，扩容 Entry 数组
            rehash();

            tab = table;
            hash = key.hashCode();
            // 存储在 Entry 数组中的索引值为 (hashCode & 0x7FFFFFFF) % table.length
            index = (hash & 0x7FFFFFFF) % tab.length;
        }

        // Creates the new entry.
        @SuppressWarnings("unchecked")
        Entry<K,V> e = (Entry<K,V>) tab[index];
        // 创建新的 Entry 节点，并链接旧 Entry 节点
        tab[index] = new Entry<>(hash, key, value, e);
        count++;
    }

    @SuppressWarnings("unchecked")
    protected void rehash() {
        int oldCapacity = table.length;
        Entry<?,?>[] oldMap = table;

        // overflow-conscious code
        // 新 Entry 数组的容量为 (旧容量 * 2) + 1
        int newCapacity = (oldCapacity << 1) + 1;
        // 当新 Entry 数组的容量大于 Entry 数组的最大容量
        // 则保持运行最大容量的 Entry 数组
        if (newCapacity - MAX_ARRAY_SIZE > 0) {
            if (oldCapacity == MAX_ARRAY_SIZE)
                // Keep running with MAX_ARRAY_SIZE buckets
                return;
            newCapacity = MAX_ARRAY_SIZE;
        }
        Entry<?,?>[] newMap = new Entry<?,?>[newCapacity];

        // 记录修改一次 Entry 数组
        modCount++;
        // 兼容 Entry 数组最大容量的情况
        threshold = (int)Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
        table = newMap;

        // 迁移数据
        for (int i = oldCapacity ; i-- > 0 ;) {
            for (Entry<K,V> old = (Entry<K,V>)oldMap[i] ; old != null ; ) {
                Entry<K,V> e = old;
                old = old.next;

                int index = (e.hash & 0x7FFFFFFF) % newCapacity;
                e.next = (Entry<K,V>)newMap[index];
                newMap[index] = e;
            }
        }
    }

    ......

    private static class Entry<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Entry<K,V> next;

        protected Entry(int hash, K key, V value, Entry<K,V> next) {
            this.hash = hash;
            this.key =  key;
            this.value = value;
            this.next = next;
        }

        public int hashCode() {
            // 计算 Entry 节点的 hashcode
            return hash ^ Objects.hashCode(value);
        }

        ......
    }
}
```
