---
title: 集合框架ArrayList
date: 2016-08-07 16:26:23
updated: 2016-08-07 16:26:30
categories: [Learning, Java]
tags: [java]
---

# [ArrayList](http://www.jianshu.com/p/085a5ba2aca8)

顺序插入，查找快，删除、插入较慢。

<!-- more -->

## 结构

<img src="http://7xt0u5.com1.z0.glb.clouddn.com/7cb31792-49ee-4b00-80fa-3a275d9d288e.png" width="500"/>

动态数组，继承AbstractList，实现List、RandomAccess、Cloneable、Serializable。

- Q：ArrayList继承了AbstractList，为什么还要实现List？
- [A](http://stackoverflow.com/questions/4387419/why-does-arraylist-have-implements-list)：显式表明ArrayList实现了List，AbstractList只是为了提升List实现的代码重用。

1. RandomAccess：标记接口，表明List的某个实现支持随机访问。
2. Cloneable：可被克隆
3. Serializable：可被序列化

不是线程安全，多线程可以选择Vector或CopyOnWriteArrayList。

> JDK 1.8

## 属性

```java
// 保存数据的数组
transient Object[] elementData;
// ArrayList中实际数据的数量
private int size;
```

- Q：elementData需要序列化，为什么还要被transient修饰，被transient修饰了，为什么还能被序列化？
- [A](http://zhidao.baidu.com/link?url=8xuaZ3lw4AJxK_vE7Xz0hVDntSPa3PTR-ub58AsH5Gm7sLsCFHIWD800MCBRC5_-KcOdIUsZw1LAenZE4SNetq)：elementData是一个缓存数组，通常会预留容量，所以elementData中只有实际存放的元素需要被序列化，故被transient修饰防止所有元素被序列化；ArrayList中元素还能被序列化是因为它重写了writeObject()方法（debug看到这个方法是被放射获取调用的）。

```java
private void writeObject(java.io.ObjectOutputStream s)
    throws java.io.IOException{
    int expectedModCount = modCount;
    s.defaultWriteObject();
    s.writeInt(size);
    for (int i=0; i<size; i++) {
        s.writeObject(elementData[i]); // 只序列化size个元素
    }
    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}
```


## 构造

```java
// 不可变的空数组
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
// 初始化指定大小的数组
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
    }
}
```

- 第一个构造函数会将elementData初始化为空数组
- 第二个构造函数会将elementData初始化为指定大小的数组

当添加元素时，会调用如下方法，若elementData为空数组，则将它扩展为默认长度（10）的数组；若添加元素后elementData长度不够，将会将elementData扩展为原来的1.5倍。

```java
// 默认容器大小
private static final int DEFAULT_CAPACITY = 10;
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}
// 确保ArrayList能装下minCapacity个元素
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
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

## 方法


```java
boolean add(E e) // 尾部添加元素
void add(int index, E element) // 在指定位置添加元素
boolean addAll(Collection<? extends E> c) // 尾部添加集合中所有元素
boolean addAll(int index, Collection<? extends E> c) //　指定位置添加集合中所有元素

void clear() // 清除所有元素
Object clone() // 浅复制集合
boolean contains(Object o) // 是否包含某元素
void ensureCapacity(int minCapacity) // 确保ArrayList的容器能装下minCapacity个元素

void forEach(Consumer<? super E> action) // 对每个元素执行action操作（迭代器模式）
E get(int index) // 获取指定位置元素
int indexOf(Object o) // 获取指定元素位置
boolean isEmpty() // 是否为空集合

Iterator<E> iterator() // 获取迭代器
int lastIndexOf(Object o) // 获取指定位置，倒序查找

ListIterator<E> listIterator() // 返回迭代器ListIterator
ListIterator<E> listIterator(int index) // 在指定位置返回迭代器ListIterator

E remove(int index) // 移除并获取指定位置元素
boolean remove(Object o) // 移除元素，仅移除遍历到的第一个
boolean removeAll(Collection<?> c) // 移除指定集合中所有元素
boolean removeIf(Predicate<? super E> filter) // 移除满足条件的元素
void replaceAll(UnaryOperator<E> operator) // 按照operator操作替换所有元素
boolean retainAll(Collection<?> c) // 只保留包含在指定集合中的元素
E set(int index, E element) // 设置指定位置的元素值
int size() // 集合大小
void sort(Comparator<? super E> c) // 根据指定比较方式comparator排序
Spliterator<E> spliterator() // 获取集合的spliterator，不会用
List<E> subList(int fromIndex, int toIndex) // 获取fromIndex-toIndex的子集合
Object[] toArray() // 转化为数组
<T> T[] toArray(T[] a) // 创建一个新数组传入，返回新数组类型元素，元素不够置null
void trimToSize() // 缩减ArrayList容器的大小至真实存放元素的数量
```

## 示例

- `void forEach(Consumer<? super E> action)`

```java
newList.forEach(new Consumer<Integer>(){
    @Override
    public void accept(Integer s) {
        System.out.println("Consumer."+s);
    }
});
```

- `boolean removeIf(Predicate<? super E> filter)`

```java
newList.removeIf(new Predicate<Integer>() {
    @Override
    public boolean test(Integer s) {
        if(s < 50)
            return true;
        return false;
    }
});
```

- `void replaceAll(UnaryOperator<E> operator)`

```java
newList.replaceAll(new UnaryOperator<Integer>() {
    @Override
    public Integer apply(Integer s) {
        return s*s;
    }
});
```

- `void sort(Comparator<? super E> c)`

```java
newList.sort(new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o1.compareTo(o2);
    }
});
```

- `Spliterator<E> spliterator()`

```java
Spliterator<Integer> spliterator = newList.spliterator();
System.out.println(spliterator.characteristics());
```

## 源码

### add

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // 确保容器能装下(size+1)个元素
    elementData[size++] = e;
    return true;
}
public void add(int index, E element) {
    rangeCheckForAdd(index); // 数组越界检查
    ensureCapacityInternal(size + 1);
    // arraycopy(src, srcPos, dest, destPos, length) 将src[srcPos, srcPos+length]复制到dest[destPos, destPos+length]
    System.arraycopy(elementData, index, elementData, index + 1, size - index);
    elementData[index] = element;
    size++;
}
public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);
    System.arraycopy(a, 0, elementData, size, numNew);
    size += numNew;
    return numNew != 0;
}
public boolean addAll(int index, Collection<? extends E> c) {
    rangeCheckForAdd(index);
    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);
    int numMoved = size - index;
    if (numMoved > 0)
        System.arraycopy(elementData, index, elementData, index + numNew, numMoved);
    System.arraycopy(a, 0, elementData, index, numNew);
    size += numNew;
    return numNew != 0;
}
// 数组越界检查
private void rangeCheckForAdd(int index) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index)); // 越界抛出异常
}
private void ensureCapacityInternal(int minCapacity) {
    // 若为空数组{}，选择较大参数进行扩容
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    if (minCapacity - elementData.length > 0)
        // 如果要求的容器大小大于当前容器大小，则进行扩容
        grow(minCapacity);
}
// 扩容
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1); // 新容量约为旧容量的1.5倍
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity; // 旧容量的1.5倍仍不足，就直接等于要求的大小minCapacity
    if (newCapacity - MAX_ARRAY_SIZE > 0) // 旧容量的1.5倍大于容量最大值
        // 若要求的大小minCapacity大于MAX_ARRAY_SIZE，新容量取Integer.MAX_VALUE，否则MAX_ARRAY_SIZE（即ArrayList容量最大值为Integer.MAX_VALUE，估计这么大会内存不足吧）
        newCapacity = hugeCapacity(minCapacity);
    // 扩容，Array.copyOf底层实现是System.arraycopy
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

### remove

```java
public E remove(int index) {
    rangeCheck(index); // 越界检查
    modCount++;
    E oldValue = elementData(index); // 取出旧值，待返回
    int numMoved = size - index - 1; // 待移动的个数
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved); // 向前移动一个位置
    elementData[--size] = null; // 最后一个元素置null
    return oldValue;
}
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index); // 所有元素向前移动一个位置
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
public boolean removeAll(Collection<?> c) {
    Objects.requireNonNull(c); // 需要非空值，null报空指针异常
    return batchRemove(c, false);
}
// 批量删除
private boolean batchRemove(Collection<?> c, boolean complement) {
    final Object[] elementData = this.elementData;
    int r = 0, w = 0;
    boolean modified = false;
    try {
        for (; r < size; r++)
            if (c.contains(elementData[r]) == complement) // complement=false
                elementData[w++] = elementData[r]; // 只保留c中不存在的元素
    } finally {
        if (r != size) { // 不解，为什么r可能!=size
            System.arraycopy(elementData, r, elementData, w, size - r);
            w += size - r;
        }
        if (w != size) {
            for (int i = w; i < size; i++) // 剩余元素全置null
                elementData[i] = null;
            modCount += size - w;
            size = w; // 重置size
            modified = true; // 修改flag
        }
    }
    return modified;
}
```

### set

```java
public E set(int index, E element) {
    rangeCheck(index); // 越界检查
    E oldValue = elementData(index);
    elementData[index] = element; // 设置新值
    return oldValue; // 返回旧值
}
```

### get

```java
public E get(int index) {
    rangeCheck(index);
    return elementData(index); // elementData(int index)方法，等同于elementData[index]
}
public int indexOf(Object o) {
    if (o == null) {
        for (int i = 0; i < size; i++)
            if (elementData[i]==null) // 返回遍历到的第一个元素位置
                return i;
    } else {
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1; // 不存在返回-1
}
public boolean contains(Object o) {
    return indexOf(o) >= 0; // 使用indexOf()方法检测是否包含
}
public boolean containsAll(Collection<?> c) {
    for (Object e : c)
        if (!contains(e)) // 一旦不包含某元素，返回false
            return false;
    return true;
}
public int size() {
    return size;
}
public boolean isEmpty() {
    return size == 0;
}
```

## 迭代

1. 随机访问：list.get(i)，效率最高
2. 迭代器：list.iterator()
3. foreach：for(Object i: list)
