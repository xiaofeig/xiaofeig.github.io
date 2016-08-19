---
title: 集合框架Hashtable
date: 2016-08-19 10:33:20
updated: 2016-08-19 10:33:25
categories: [Learning, Java]
tags: [java]
---

# Hashtable

基于位桶+链表实现。

<!-- more -->

## 结构

![](http://7xt0u5.com1.z0.glb.clouddn.com/ecb512eb-4fe4-4170-8043-566f64379152.png)

- 继承Dictionary（陈旧）
- 实现Cloneable，可被克隆
- 实现Serializable，可被序列化

## 属性

```java
// 存放元素的动态数组
private transient Entry<?,?>[] table;
// 元素个数
private transient int count;
// 容量上限（扩容临界值）
private int threshold;
// 加载因子
private float loadFactor;
// 修改次数
private transient int modCount = 0;
```

## 构造

```java
// 默认容量为11，加载因子为0.75
public Hashtable() {
    this(11, 0.75f);
}
public Hashtable(int initialCapacity) {
    this(initialCapacity, 0.75f);
}
public Hashtable(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+initialCapacity);
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal Load: "+loadFactor);

    if (initialCapacity==0)
        initialCapacity = 1;
    this.loadFactor = loadFactor;
    table = new Entry<?,?>[initialCapacity];
    // 容量上限
    threshold = (int)Math.min(initialCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
}
public Hashtable(Map<? extends K, ? extends V> t) {
    // 容量取2*t.size()和11的较大值
    this(Math.max(2*t.size(), 11), 0.75f);
    putAll(t);
}
```

## 方法

与[HashMap](http://coding.xiaofeig.cn/2016/08/07/Le-16-HashMap/#方法)中类似。

## 源码

### put

```java
// 添加键值对（put方法被synchronized修饰，线程安全）
public synchronized V put(K key, V value) {
    // value不能为null（和HashMap不同）
    if (value == null) {
        throw new NullPointerException();
    }

    // Makes sure the key is not already in the hashtable.
    Entry<?,?> tab[] = table;
    // key不能为null，否则抛出NullPointerException（和HashMap不同）
    int hash = key.hashCode();
    // 获取table下标
    int index = (hash & 0x7FFFFFFF) % tab.length;
    @SuppressWarnings("unchecked")
    Entry<K,V> entry = (Entry<K,V>)tab[index];
    // 查找是否有key相同元素，有则替换value
    for(; entry != null ; entry = entry.next) {
        if ((entry.hash == hash) && entry.key.equals(key)) {
            V old = entry.value;
            entry.value = value;
            return old;
        }
    }

    addEntry(hash, key, value, index);
    return null;
}
```

### addEntry

```java
// 添加时，不存在key相同时，添加新的entry
private void addEntry(int hash, K key, V value, int index) {
    // 修改次数+1
    modCount++;
    Entry<?,?> tab[] = table;
    // 元素个数 > 容量上限，进行扩容
    if (count >= threshold) {
        // table再哈希，容量扩至2倍，重置元素下标
        rehash();
        tab = table;
        hash = key.hashCode();
        index = (hash & 0x7FFFFFFF) % tab.length;
    }

    // 创建新的entry，并添加至链表表头（HashMap是添加至链表尾部）
    @SuppressWarnings("unchecked")
    Entry<K,V> e = (Entry<K,V>) tab[index];
    tab[index] = new Entry<>(hash, key, value, e);
    count++;
}
```

### rehash

和[HashMap](http://coding.xiaofeig.cn/2016/08/07/Le-16-HashMap/)类似，不同的是链表元素个数>8时，转为红黑树，Hashtable纯粹是链表结构。

```java
// 再哈希
protected void rehash() {
    int oldCapacity = table.length;
    Entry<?,?>[] oldMap = table;

    // 新容量为原来的两倍
    int newCapacity = (oldCapacity << 1) + 1;
    if (newCapacity - MAX_ARRAY_SIZE > 0) {
        if (oldCapacity == MAX_ARRAY_SIZE)
            // Keep running with MAX_ARRAY_SIZE buckets
            return;
        newCapacity = MAX_ARRAY_SIZE;
    }
    Entry<?,?>[] newMap = new Entry<?,?>[newCapacity];
    // 修改次数+1
    modCount++;
    // 重置容量上限，newCapacity * loadFactor和MAX_ARRAY_SIZE + 1的较小值
    threshold = (int)Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
    table = newMap;
    for (int i = oldCapacity ; i-- > 0 ;) {
        for (Entry<K,V> old = (Entry<K,V>)oldMap[i] ; old != null ; ) {
            Entry<K,V> e = old;
            old = old.next;
            // 重新计算table下标
            int index = (e.hash & 0x7FFFFFFF) % newCapacity;
            e.next = (Entry<K,V>)newMap[index];
            newMap[index] = e;
        }
    }
}
```

## 和HashMap的不同之处

|HashMap|Hashtable|
|:---|:---|
|继承AbstractMap|继承Dictionary|
|线程不安全|put、remove等方法均被synchronized修饰，同步，线程安全|
|key、value均可为null|key、value均**不可**为null|
|由**位桶+链表+红黑树**构成|由**位桶+链表**构成|
|添加新entry时，放置链表**尾部**|添加新entry时，放置链表**头部**|
|...|...|


