---
title: 集合框架LinkedList
date: 2016-08-07 16:30:23
updated: 2016-08-07 16:30:29
categories: [Learning, Java]
tags: [java]
---

# [LinkedList](http://www.jianshu.com/p/681802a00cdf)

顺序插入，查找慢，插入、删除较快

<!-- more -->

## 链表

**1. 单向链表**

<img src="http://7xt0u5.com1.z0.glb.clouddn.com/e47cecdd-f281-4e84-9c32-cf44042c781e.png" width="300"/>

**2. 单向循环链表**

<img src="http://7xt0u5.com1.z0.glb.clouddn.com/fd2ce9a7-3296-471f-bab7-28daa4f707f5.png" width="300"/>

**3. 双向链表**

<img src="http://7xt0u5.com1.z0.glb.clouddn.com/70bd069d-41e0-43c4-bcd0-355d0974c2ff.png" width="450"/>

**4. 双向循环链表**

<img src="http://7xt0u5.com1.z0.glb.clouddn.com/b49600aa-6ed8-4783-b0bb-f565983a1756.png" width="300"/>

## 结构

<img src="http://7xt0u5.com1.z0.glb.clouddn.com/0590a5df-dac7-419a-a572-75222efb2a8c.png" width="400"/>

链表集合，继承AbstractSequentialList，实现Deque、Cloneable、Serializable。

1. 实现Deque，可以当作双端序列使用
2. Cloneable：可被克隆
3. Serializable：可被序列化

## 属性

```java
transient int size = 0; // 长度
transient Node<E> first; // 头节点
transient Node<E> last; // 尾节点
private static class Node<E> {
    E item; // 元素
    Node<E> next; // 下一节点
    Node<E> prev; // 上一节点
}
```

**Invariant（不变式，恒为true）:**

**(first==null&&last==null)||(first.prev==null&&first.item!=null)**

**(first==null&&last==null)||(last.next==null&&last.item!=null)**

- (first==null&&last==null)：空链表
- (first.prev==null&&first.item!=null)：非空链表
- (last.next==null&&last.item!=null)：非空链表

由此可以看出，LinkedList实现的是**双向链表**，即上述的第3种。

## 构造

```java
public LinkedList() {
}
public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```

## 方法

```java
// Queue接口的方法
boolean add(E e) // 尾部添加一个节点
E element() // 获取头节点元素
boolean offer(E e) // 尾部添加元素
E peek() // 获取头节点元素
E poll() // 获取头节点元素并移除
E remove() // 获取头节点元素并移除

// Deque接口的方法
void addFirst(E e) // 添加元素至头节点
void addLast(E e) // 添加元素至尾节点
ListIterator<E> listIterator(int index) // 迭代器
Iterator<E> descendingIterator() // 逆序迭代器
E getFirst() // 获取头节点元素
E getLast() // 获取尾节点元素
boolean offerFirst(E e) // 头部添加元素
boolean offerLast(E e) // 尾部添加元素
E peekFirst() // 获取头节点元素，空集合返回null
E peekLast() // 获取尾节点元素，空集合返回null
E pollFirst() // 获取头节点元素并移除，空集合返回null
E pollLast() // 获取尾节点元素并移除，空集合返回null
E pop() // 出栈，弹出链表头节点
void push(E e) // 压栈，添加在链表头部（与add()相反）
boolean remove(Object o) // 移除指定节点元素
E removeFirst() // 获取头节点元素并移除
E removeLast() // 获取尾节点元素并移除
boolean removeFirstOccurrence(Object o) // 移除指定元素（出现的第一个）
boolean removeLastOccurrence(Object o) // 移除指定元素（出现的最后一个）

// 其它
void add(int index, E element) // 指定位置添加一个节点
boolean addAll(Collection<? extends E> c) // 尾部添加指定集合中所有元素
boolean addAll(int index, Collection<? extends E> c) // 指定位置添加指定集合中所有元素
E get(int index) // 获取指定位置节点元素
E set(int index, E element) // 替换指定位置元素
E remove(int index) // 获取指定位置节点元素并移除
int indexOf(Object o) // 获取元素首次出现的位置
int lastIndexOf(Object o) // 逆序获取元素首次出现的位置
void clear() // 清除所有元素
boolean contains(Object o) // 是否包含某元素
int size() // 大小
Spliterator<E> spliterator() // spliterator
Object[] toArray() // 将集合以数组形式返回
<T> T[] toArray(T[] a) // 将集合以数组形式返回，类型、大小由参数决定，不足返回null
Object clone() // 浅复制集合
```

## 源码

### add

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}
void linkLast(E e) {
    final Node<E> l = last; // 保存last
    final Node<E> newNode = new Node<>(l, e, null); // Node(pre, element, next)，创建新节点，next为null
    last = newNode; // last为新节点
    if (l == null)
        first = newNode; // 链表为空时，初始化first
    else
        l.next = newNode; // 链表不为空时，上一节点的next指向newNode
    size++;
    modCount++;
}
public void add(int index, E element) {
    checkPositionIndex(index); // 检查index >= 0 && index <= size
    if (index == size) // 尾部添加节点
        linkLast(element);
    else
        linkBefore(element, node(index));
}
void linkBefore(E e, Node<E> succ) {
    final Node<E> pred = succ.prev; // 保存原位置节点前一个节点
    final Node<E> newNode = new Node<>(pred, e, succ); // 创建新节点，设置pre，next
    succ.prev = newNode; // 设置原位置节点的pre为新节点
    if (pred == null)
        first = newNode; // 原位置节点前一个节点为null，设置新节点为头节点
    else
        pred.next = newNode; // 原位置节点的前一个节点的next为新节点
    size++;
    modCount++;
}
public boolean addAll(Collection<? extends E> c) {
    return addAll(size, c); // 转为addAll(index, c)
}
public boolean addAll(int index, Collection<? extends E> c) {
    checkPositionIndex(index); // 检查index >= 0 && index <= size
    Object[] a = c.toArray(); // 转为数组
    int numNew = a.length;
    if (numNew == 0)
        return false;
    Node<E> pred, succ;
    if (index == size) {
        succ = null; // 插入到尾部
        pred = last;
    } else {
        succ = node(index); // 插入到位置i（0<=i<size）
        pred = succ.prev;
    }
    for (Object o : a) { // 遍历节点，进行插入操作
        @SuppressWarnings("unchecked") E e = (E) o;
        Node<E> newNode = new Node<>(pred, e, null); // 设置pre，next
        if (pred == null)
            first = newNode; // pred为null，重置first
        else
            pred.next = newNode; // 设置前一个节点的next
        pred = newNode; // 重置pred
    }
    if (succ == null) {
        last = pred; // 插入到尾部，重置last
    } else {
        pred.next = succ; // 设置插入的最后一个节点的next
        succ.prev = pred; // 设置原位置节点的prev
    }
    size += numNew;
    modCount++;
    return true;
}
```

### remove

```java
public E remove(int index) {
    checkElementIndex(index);
    return unlink(node(index));
}
E unlink(Node<E> x) {
    final E element = x.item; // 保存原值
    final Node<E> next = x.next; // 前一个节点
    final Node<E> prev = x.prev; // 后一个节点
    if (prev == null) {
        first = next; // 若前一个节点为null，则重置first
    } else {
        prev.next = next; // 否则设置前一个节点的next为后一个节点
        x.prev = null; // 解除原位置节点prev，便于垃圾回收
    }
    if (next == null) {
        last = prev; // 若后一个节点为null，则重置last
    } else {
        next.prev = prev; // 否则设置后一个节点的prev为前一个节点
        x.next = null; // 解除原位置节点next，便于垃圾回收
    }
    x.item = null; // 解除原位置节点item，便于垃圾回收
    size--;
    modCount++;
    return element; // 返回原值
}
```

```java
public boolean remove(Object o) {
    if (o == null) {
        for (Node<E> x = first; x != null; x = x.next) { // 遍历
            if (x.item == null) { // 若为null，则删除该节点
                unlink(x);
                return true; // 仅进行一次删除操作
            }
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item)) {
                unlink(x);
                return true;
            }
        }
    }
    return false;
}
```

```java
public E remove() {
    return removeFirst(); // 删除头节点
}
public E removeFirst() {
    final Node<E> f = first;
    if (f == null)
        throw new NoSuchElementException(); // 不存在则抛出异常
    return unlinkFirst(f); // 删除操作
}
private E unlinkFirst(Node<E> f) {
    final E element = f.item; // 保存原值
    final Node<E> next = f.next; // 保存下一个节点
    f.item = null; // 重置为null
    f.next = null; // 便于垃圾回收
    first = next; // 重置first
    if (next == null)
        last = null; // 若next为null，重置last
    else
        next.prev = null; //否则重置next.prev
    size--;
    modCount++;
    return element; // 返回原值
}
```

```java
public boolean removeAll(Collection<?> c) {
    Objects.requireNonNull(c); // 非空检查
    boolean modified = false; // 是否删除
    Iterator<?> it = iterator();
    while (it.hasNext()) {
        if (c.contains(it.next())) {
            it.remove(); // 调用迭代器的remove()方法
            modified = true;
        }
    }
    return modified;
}
```

### set

```java
public E set(int index, E element) {
    checkElementIndex(index); // 检查index >= 0 && index <= size
    Node<E> x = node(index);
    E oldVal = x.item; // 保存原值
    x.item = element; // 重置新值
    return oldVal; // 返回原值
}
```

### get

```java
public E get(int index) {
    checkElementIndex(index); // 检查index >= 0 && index <= size
    return node(index).item; // 返回，待续。。
}
Node<E> node(int index) {
    if (index < (size >> 1)) { // 如果位置index < (size/2)
        Node<E> x = first; // 从first开始遍历
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last; // 否则从last开始遍历
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

### offer

```java
public boolean offer(E e) {
    return add(e);
}
```

### peek

```java
public E peek() {
    final Node<E> f = first;
    return (f == null) ? null : f.item;
}
```

### poll

```java
public E poll() {
    final Node<E> f = first;
    return (f == null) ? null : unlinkFirst(f);
}
```

### pop

```java
public E pop() {
    return removeFirst();
}
```

### push

```java
public void push(E e) {
    addFirst(e);
}
```


## 迭代

1. 随机访问：list.get(i)，效率超级低
2. 迭代器：list.iterator()，效率最高
3. foreach：for(Object i: list)，效率其次