---
title: 写给 ArrayList 的注释
date: 2018-07-02 20:53:59
categories: 编程
tags:
- Java
---
基于 JDK8 为 [ArrayList](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html) 编写个人理解的注释。<!-- more -->

```java
package java.util;

......

public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // 内部数组的默认容量
    private static final int DEFAULT_CAPACITY = 10;

    // 空数组，当 size == 0 时使用
    private static final Object[] EMPTY_ELEMENTDATA = {};

    // 空数组，当 size == DEFAULT_CAPACITY 时使用
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    // 内部数组，用于存放 ArrayList 元素
    transient Object[] elementData;

    // 内部数组中的元素个数
    private int size;

    // 记录链表的内部节点的改变次数
    // 用于抛出 ConcurrentModificationException，为了避免同时迭代和修改内部数组
    protected transient int modCount = 0;

    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            // new 内部数组
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            // 当 size == 0 时，替换内部数组为 EMPTY_ELEMENTDATA
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // 兼容 c.toArray() 可能没有（不正确地）返回 Object[] 情况
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // 当 size == 0 时，替换内部数组为 EMPTY_ELEMENTDATA
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }

    // ArrayList 扩容操作

    // 确认内部数组是否需要扩容
    public void ensureCapacity(int minCapacity) {
        // 获当前取内部数组的容量
        // 若当前内部数组不是 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 时，则容量为 DEFAULT_CAPACITY
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            ? 0 : DEFAULT_CAPACITY;

        // 若当前内部数组的容量大于需要扩容的最小容量，则扩容内部数组
        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }

    // 计算内部数组需要扩容的容量
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
        // 若当前内部数组是 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 时，则选择 DEFAULT_CAPACITY 和 minCapacity 的最大值
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }

    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

    private void grow(int minCapacity) {
        // 新扩容的数组长度：旧数组长度 + 旧数组长度 * 2
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            // 当新扩容的数组长度达到上限时，则以最大的内部数组长度扩容
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }

    // ArrayList 数组操作

    // 按升序判断元素在内部数组中的位置
    public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

    // 按降序判断元素在内部数组中的位置
    public int lastIndexOf(Object o) {
        if (o == null) {
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

    // 按下标获取内部数组的元素
    @SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }

    // 按下标设置内部数组的元素，并返回旧元素值
    public E get(int index) {
        // 校验参数
        rangeCheck(index);

        return elementData(index);
    }

    // 按下标设置内部数组的元素，并返回旧元素值
    public E set(int index, E element) {
        //校验参数
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }

    // 添加新元素至内部数组的末尾位置
    public boolean add(E e) {
        // 确认内部数组是否需要扩容
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    // 添加新元素至内部数组的指定下标位置
    public void add(int index, E element) {
        // 校验参数
        rangeCheckForAdd(index);

        // 确认内部数组是否需要扩容
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        // 将内部数组 index 之后的元素向后移动一位
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        // 将新元素复制给内部数组的 index 下标位置
        elementData[index] = element;
        // 更新 size
        size++;
    }

    // 按下标删除内部数组的元素，并返回旧元素值
    public E remove(int index) {
        // 校验参数
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        // 将内部数组 index 之后的元素向前移动一位
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        // 将数组最后一位元素置空
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }

    // 按 o.equals() 匹配并删除内部数组的元素，并返回删除是否成功
    public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

    // 移动内部数组
    private void fastRemove(int index) {
        modCount++;
        // 将内部数组 index 之后的元素向前移动一位
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        // 将数组最后一位元素置空
        elementData[--size] = null; // clear to let GC do its work
    }

    // 集合的差集：删除内部数组中的指定元素
    public boolean removeAll(Collection<?> c) {
        Objects.requireNonNull(c);
        return batchRemove(c, false);
    }

    // 集合的交集：检索内部数组中的指定元素
    public boolean retainAll(Collection<?> c) {
        Objects.requireNonNull(c);
        return batchRemove(c, true);
    }

    private boolean batchRemove(Collection<?> c, boolean complement) {
        final Object[] elementData = this.elementData;
        int r = 0, w = 0;
        boolean modified = false;
        try {
            for (; r < size; r++)
                // removeAll：将未匹配的元素保留下来
                // retainAll：将匹配的元素保留下来
                if (c.contains(elementData[r]) == complement)
                    elementData[w++] = elementData[r];
        } finally {
            // Preserve behavioral compatibility with AbstractCollection,
            // even if c.contains() throws.
            if (r != size) {
                System.arraycopy(elementData, r,
                                 elementData, w,
                                 size - r);
                w += size - r;
            }
            if (w != size) {
                // clear to let GC do its work
                for (int i = w; i < size; i++)
                    elementData[i] = null;
                modCount += size - w;
                size = w;
                modified = true;
            }
        }
        return modified;
    }

    ......
}
```
