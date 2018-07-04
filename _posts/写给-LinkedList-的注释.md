---
title: 写给 LinkedList 的注释
date: 2018-07-04 21:07:42
categories: 编程
tags:
- Java
---
基于 JDK8 为 [LinkedList](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html) 编写个人理解的注释。<!-- more -->

```java
package java.util;

......

public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    // 链表中的节点个数
    transient int size = 0;

    // 链表中的第一个节点
    transient Node<E> first;

    // 链表中的最后一个节点
    transient Node<E> last;

    // 记录链表的内部节点的改变次数
    // 用于抛出 ConcurrentModificationException，为了避免同时迭代和修改内部数组
    protected transient int modCount = 0

    // LinkedList 内部节点，用于存放 LinkedList 的 <E> 数据 
    private static class Node<E> {
        // 内部节点的数据
        E item;
        // 内部节点的上一个节点
        Node<E> next;
        // 内部节点的下一个节点
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }

    public LinkedList() {
    }

    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }

    // 将元素关联为链表的第一个节点
    private void linkFirst(E e) {
        final Node<E> f = first;
        // 创建一个新的节点，作为第一个节点
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;
        if (f == null)
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
    }

    // 将元素关联为链表的最后一个节点
    void linkLast(E e) {
        final Node<E> l = last;
        // 创建一个新的节点，作为最后一个节点
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }

    // 将元素关联为指定节点的上一个节点
    void linkBefore(E e, Node<E> succ) {
        // 获取指定节点的上一个节点
        final Node<E> pred = succ.prev;
        // 创建一个新的节点，prev = 指定节点的上一个节点，next = 指定节点
        final Node<E> newNode = new Node<>(pred, e, succ);
        // 修改指定节点的上一个节点
        succ.prev = newNode;
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }

    // 从链表中删除第一个节点，并返回第一个节点的数据
    private E unlinkFirst(Node<E> f) {
        final E element = f.item;
        final Node<E> next = f.next;
        f.item = null;
        f.next = null; // help GC
        first = next;
        if (next == null)
            last = null;
        else
            next.prev = null;
        size--;
        modCount++;
        return element;
    }

    // 从链表中删除最后一个节点，并返回最后一个节点的数据
    private E unlinkLast(Node<E> l) {
        // assert l == last && l != null;
        final E element = l.item;
        final Node<E> prev = l.prev;
        l.item = null;
        l.prev = null; // help GC
        last = prev;
        if (prev == null)
            first = null;
        else
            prev.next = null;
        size--;
        modCount++;
        return element;
    }

    // 从链表中删除指定节点，并返回指定节点的数据
    E unlink(Node<E> x) {
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;
        size--;
        modCount++;
        return element;
    }

    // 获取链表的第一个节点
    public E getFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
    }

    // 获取链表的最后一个节点
    public E getLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return l.item;
    }

    // 删除链表的第一个节点
    public E removeFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return unlinkFirst(f);
    }

    // 删除链表的最后一个节点
    public E removeLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return unlinkLast(l);
    }

    // 添加一个节点至链表的开头
    public void addFirst(E e) {
        linkFirst(e);
    }

    // 添加一个节点至链表的末尾
    public void addLast(E e) {
        linkLast(e);
    }

    ......
}
```
