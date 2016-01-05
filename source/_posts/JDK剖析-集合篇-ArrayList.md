title: JDK剖析-集合篇-ArrayList
tags: |-

  - Java
  - Java集合
  - ArrayList
  - java
permalink: jdk-source-collections-arraylist
id: 16
updated: '2014-09-24 16:02:56'
date: 2014-09-12 18:06:53
---

Arraylist和LinkedList是在工作中常用的集合工具。

##构造方法
```
public ArrayList() {
    super();
    this.elementData = EMPTY_ELEMENTDATA;
}
```
初始化长度为0的Object[]，当第一次add()方法时，扩充为DEFAULT_CAPACITY个长度(默认为10)；


```
public ArrayList(int initialCapacity) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
    this.elementData = new Object[initialCapacity];
}
```
初始化initialCapacity长度的Object[]，


```
public ArrayList(Collection<? extends E> c) {
	elementData = c.toArray();
	size = elementData.length;
	// c.toArray might (incorrectly) not return Object[] (see 6260652)
    if (elementData.getClass() != Object[].class)
        elementData = Arrays.copyOf(elementData, size, Object[].class);
}
```
初始化长度为c.size()的Object[]。


##全局变量

`private static final int DEFAULT_CAPACITY = 10;`<br>
当第一次add的时候，默认扩容的长度

`private static final Object[] EMPTY_ELEMENTDATA = {};`<br>
空构造初始化的对象

`private transient Object[] elementData;`<br>
保存元素的数组

`private int size;`<br>
当前list的长度

`protected transient int modCount = 0;`<br>
list长度修改的次数，即elementData修改的次数

`private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;`<br>
list的最大长度，减8是因为有些虚拟机会将头信息放进数组中，为了防止OutOfMemoryError发生，

##add()方法


```
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```
首先确定确定list的容量，如果容量满了，就扩容。
然后存入size++位置
返回true


```
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}
```
首先判断elementData是不是刚初始化的空数组，如果是就将minCapacity赋值为DEFAULT_CAPACITY(默认为10)，

```
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```
将elementData的修改次数modCount加一

```
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```
oldCapacity >> 1 就是oldCapacity的二分之一，也就是newCapacity= oldCapacity*1.5，也就是数组需要扩容是则扩容原来的1.5倍的长度。


##remove()方法

```
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```
根据索引值查出元素位置，首先检查索引位置的合法性，记录修改次数，拿出被删元素，将被删除索引位置后面的元素依次往前移，将尾部元素位置至Null，记录数组size，并返回被删元素。

```
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
```
```
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
```
根据元素对象删除，根据元素是否为Null，遍历出元素位置，然后删除。


##set()方法
```
public E set(int index, E element) {
    rangeCheck(index);
    E oldValue = elementData(index);
    elementData[index] = element;
    return oldValue;
}
```
##get()方法
```
public E get(int index) {
    rangeCheck(index);
    return elementData(index);
}
```


##总结
ArrayList类的内部实现比较简单，内部维护一个Object数组，当需要扩充容量的时候，会新建一个Object数组，将原来的数据拷贝进来。操作list的方法实现也较简单。

















