title: JDK剖析-集合篇-LinkedList
tags: |-

  - Java
  - Java集合
  - LinkedList
  - java
permalink: jdk-source-collections-linkedlist
id: 17
updated: '2014-09-27 03:32:08'
date: 2014-09-12 19:04:16
---


##构造方法

```java
public LinkedList() {
}
```

```java
public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```
##全局变量

###元素类

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

由此可见LinkedList的双向链表结构，Node类包含了分别指向前一节点和后一个节点的两个引用，还有就是对自己的引用item。构造方法需要接受前后两个节点的引用。

`transient Node<E> first;`<br>
`transient Node<E> last;`

如果LinkedList的长度为0，那么first和last都为Null，否则first.prev==null && last.next==null,并且first和last都不能为Null。

`transient int size = 0;`

##add()方法

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}
```
```java
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```
LinkedList的add方法是将元素往链表的尾部插入，当然也可以在头部插入，是同样的道理。

```java
private void linkFirst(E e) {
    final Node<E> f = first;
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;
    size++;
    modCount++;
}
```

##remove()方法

```java
public E remove(int index) {
    checkElementIndex(index);
    return unlink(node(index));
}
```
检查索引位置的合法性

```java
Node<E> node(int index) {
    // assert isElementIndex(index);

    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```
遍历对应索引位置的元素对象，最多索引总长度的一半

```java
E unlink(Node<E> x) {
    // assert x != null;
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
```
判断被删除节点是否为特首的头尾节点，然后将该节点的前后节点指向正确的元素即可，由此可见链表的删除方式还是非常简单的，并没有遍历完整的链表(最多遍历链表长度的一半)，也不用更改链表的索引值。索引速度是比数组结构要快些。

##总结

LinkedList内部维护了一个链表结构，对于链表只要知道头或尾就能遍历出所有元素，当add元素的时候，会将元素封装成Node<E>这个内部类就拥有分别指向前节点，后节点，自己节点的引用了。LinkedList遍历链表最多只遍历总长度的一半，避免了遍历完整表，有效提高了查找效率。


