---
title: 集合框架HashSet
date: 2016-08-18 15:33:28
updated: 2016-08-19 10:52:03
categories: [Learning, Java, 集合框架]
tags: [java]
---

# HashSet

HashSet基于HashMap来实现的，底层是使用HashMap的Key保存元素，Value统一为类的常量Object对象。

<!-- more -->

## 结构

![](http://7xt0u5.com1.z0.glb.clouddn.com/31079052-15b3-4e58-a257-c59e9d78232c.png)

继承AbstractCollection，实现Set、Serializable、Cloneable接口。

- Serializable：可被序列化
- Cloneable：可被克隆

**特性：**

- 无序
- 唯一
- 可为null
- 不同步（非线程安全）

## 属性

```java
// 使用map的Key保存元素
private transient HashMap<E,Object> map;
// map的Value均指向PRESENT，不设置为null是为了防止NullPointerException
private static final Object PRESENT = new Object();
```

## 构造

```java
// 使用HashMap的初始容量0和默认加载因子0.75f来初始化（JDK1.8）
public HashSet() {
    map = new HashMap<>();
}
public HashSet(Collection<? extends E> c) {
    // 容量为c的4/3倍和16的最大值
    map = new HashMap<>(Math.max((int) (c.size()/.75f) + 1, 16));
    // 添加所有元素
    addAll(c);
}
// 指定初始容量
public HashSet(int initialCapacity) {
    map = new HashMap<>(initialCapacity);
}
// 指定初始容量，加载因子（dummy未使用）
HashSet(int initialCapacity, float loadFactor, boolean dummy) {
    map = new LinkedHashMap<>(initialCapacity, loadFactor);
}
```

## 方法

```java
boolean add(E e) // 添加元素
void clear() // 清除
Object clone() // 浅复制
boolean contains(Object o) // 是否包含
boolean isEmpty() // 是否为空
Iterator<E> iterator() // 迭代器
boolean remove(Object o) // 移除
int size() // 集合大小
Spliterator<E> spliterator()
```

## 示例

```java
Spliterator spliterator = hashSet.spliterator();
// 为每个对象执行特定代码
spliterator.forEachRemaining(new Consumer() {
    @Override
    public void accept(Object o) {
        System.out.print(o+",");
    }
});
```

## 源码

### add

```java
public boolean add(E e) {
    // 调用HashMap.put(key, value)方法（key=e，value=PRESENT）
    return map.put(e, PRESENT)==null;
}
```

### remove

```java
public boolean remove(Object o) {
    //调用HashMap.remove(key)方法
    return map.remove(o)==PRESENT;
}
```


