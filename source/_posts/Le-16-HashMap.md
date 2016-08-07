---
title: 集合框架HashMap
date: 2016-08-07 16:31:47
updated: 2016-08-07 16:31:52
categories: [Learning, Java]
tags: [java]
---

# [HashMap](http://www.jianshu.com/p/31a358d14caf)

## hashcode

**基本概念**

Hash，即散列，把任意长度的输入，通过散列算法变成固定长度的输出。由于是不定长到定长的压缩映射，输出值域小于输入值域，所以不同的输入可能有相同的输出，即hash碰撞。

<!-- more -->

**解决冲突**

1. **开放地址法**：按照某种方式继续探测哈希表中的其它存储单元，直到找到空位置为止：

$$ H\_i(key) = (H(key) + d_i) \% m(i = 1, 2, \.\.\., k(k<= m-1) ) $$

$$H(key)$$表示key的直接hash地址，$$d_i$$表示每次再探测时的地址增量，$$m$$表示哈希表长度。

增量$$d_i$$可以有不同的取法：

- 1, 2, 3, ... 线性探测再散列
- 1^2， -1^2， 2^2, -2^2， ..., k^2, -k^2, ... 二次探测再散列
- 伪随机序列 伪随机再散列

2. **再hash法**：再次散列，如果依然冲突的话，可以变为多重冲突或者结合其它解决冲突的方法使用。

3. **链地址法**：如果哈希表空间为0~m-1，设置m个指针构成的数据ST[m]，哈希地址为i的数据均放在头指针为ST[i]的链表中。类似邻接表，适用于冲突比较严重的情况。HashMap采用这种方式。

4. **建立公共溢出区**：冲突的值均放在溢出区。

**哈希表**

是根据hashcode直接访问内存存储位置的数据结构。

Java中Hash表结构的集合框架排除重复元素或冲突：

源码：`p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k)))`

1. obj1.hashCode() == obj2.hashCode()
2. obj1 == obj2 || obj2.equals(obj1)

针对上述的两个条件，有三种情况：

1. 条件1和条件2均成立时，判定重复，进行替换；
2. 条件1成立，条件2不成立时，判定hash冲突，HashMap采用**链地址法**解决冲突；
3. 条件1不成立，判定hash未冲突，坑位未占，直接添加即可。

如果要实现逻辑上的相等，不仅需要重写equals()方法，还要实现hashCode()方法，保证以下两点：

1. 对于equals()相等的对象hashCode一定相等
2. 对于equals()不等的对象hashCode尽量不等

- 重写equals()方法：比较对象全部或部分字段
- 重写hashCode()方法（Effective Java）：
    1. result = 17; //非零常数，一般取素数
    2. 对字段f，生成int c：
        - [boolean] c= (f?0:1);
        - [byte, char, short or int] c= (int)f;
        - [long] c= (int)(f^(f>>>32)); // >>>无符号右移
        - [float] c = Float.floatToIntBits(f); // [浮点数转为IEEE754表示方式](http://blog.163.com/dsp_lnint/blog/static/10316743420103154327115/)
        - [double] long l = Double.doubleToLongBits(f);c = (int)(l^(l>>>32));
        - [Object] c = f.hashCode();
    3. 对每个字段f的值c，循环执行`result = 37*result + c;`

在HashMap中，不是直接用对象的hashCode作为对象的存储地址，而是调用hash(key)方法生成存储地址，这么做是防止重写hashCode()方法使存储地址非法。

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

HashMap中定义了静态内部类`Node<K, V>`：

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
    ...
}
transient Node<K,V>[] table;
```

> 注：transient关键字标注不需要序列号的字段，即，这个字段的生命周期仅存于调用者的内存中而不会写到磁盘里持久化。

HashMap存储数据时，类似邻接表，table用于存放不同的hashCode节点，`Node<K,V>`存放了不同的key。

而HashSet其实就是HashMap的键的存储。

**Hash在其它地方的应用**

1. MD5加密将任意长度的字符串散列成32位的定长字符串
2. SSH中对证书信息进行散列获取证书信息（不知）
3. SHA1加密也是一种运用了散列进行加密的算法（不知）

> [资料一](http://blog.csdn.net/w_fenghui/article/details/2010387)
> [资料二](http://www.jianshu.com/p/4e3f3b53282f)
> [资料三](http://jingyan.baidu.com/article/ff41162582507512e5823763.html)

## 结构

![](http://7xt0u5.com1.z0.glb.clouddn.com/9aa9fc92-40c5-449f-b992-3fd868fa007a.png)

位桶+链表+红黑树实现，继承AbstractMap，实现Serializable、Cloneable。

1. Cloneable：可被克隆
2. Serializable：可被序列化

## 属性

> [参考](http://www.2cto.com/kf/201606/515215.html)

```java
// 初识容量为16，必须为2的n次幂
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
// 最大容量，2的30次方
static final int MAXIMUM_CAPACITY = 1 << 30;
// 默认填充比
static final float DEFAULT_LOAD_FACTOR = 0.75f;
// 当某个位桶的链表长度为8时，将链表转换为红黑树
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;
static final int MIN_TREEIFY_CAPACITY = 64;
// 位桶
transient Node<K,V>[] table;
transient Set<Map.Entry<K,V>> entrySet;
// 存放元素的个数
transient int size;
// 修改次数，fast-fail机制
transient int modCount;
// 扩容临界值，当实际大小（容量*填充比）超过临界值时，进行扩容
int threshold;
// 填充比
final float loadFactor;
```

```java
// 单向链表
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
    Node(int hash, K key, V value, Node<K,V> next) {
        ...
    }
}
```

```java
// 红黑树
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent; // 父节点
    TreeNode<K,V> left; // 左子树
    TreeNode<K,V> right; // 右子树
    TreeNode<K,V> prev; // needed to unlink next upon deletion
    boolean red; // 颜色
    TreeNode(int hash, K key, V val, Node<K,V> next) {
        super(hash, key, val, next);
    }
}
```

## 构造

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // 默认填充比
}
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0) // 非负，否则抛出异常
        throw new IllegalArgumentException("Illegal initial capacity: " + initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY) // 不能超过最大容量
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity); // 新扩容临界值
}
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}
```

## 方法

```java
void clear() // 移除所有键值
Object    clone() // 浅复制，键值本身不会复制
V    compute(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) // 尝试为指定的键和它当前映射的值计算一个映射（没有则返回null）
V    computeIfAbsent(K key, Function<? super K,? extends V> mappingFunction) // 如果指定的键没有关联值（或关联null），使用指定的映射函数计算值，非空则加入map
V    computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) // 如果指定键关联了非空值，尝试用给定的键值计算一个映射
boolean    containsKey(Object key) // 是否包含某键
boolean    containsValue(Object value) // 是否包含某值
Set<Map.Entry<K,V>>    entrySet()  // 键值对的Set集合
void    forEach(BiConsumer<? super K,? super V> action) // 为每个键值对指定特定函数，抛出异常则中断
V    get(Object key) // 获取某键的值
V    getOrDefault(Object key, V defaultValue) // 获取某键的值，没有则返回默认值
boolean    isEmpty() // map是否为空容器
Set<K>    keySet() // 键的Set集合
V    merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction) // 如果指定的键没有关联值或关联null，则用给定的非空值关联它
V    put(K key, V value) // 添加键值对
void    putAll(Map<? extends K,? extends V> m) // 添加给定map中所有键值对
V    putIfAbsent(K key, V value) // 如果指定的键没有关联值或关联null，将它关联新值，并返回原值
V    remove(Object key) // 移除某键
boolean    remove(Object key, Object value) // 移除某键值对
V    replace(K key, V value) // 替换键值对
boolean    replace(K key, V oldValue, V newValue) // 原值相同则替换键值对
void    replaceAll(BiFunction<? super K,? super V,? extends V> function) // 调用函数替换每个键值对，抛出异常中断
int    size() // map大小
Collection<V>    values() // map的值集合
```

## 示例

```java
hashMap.compute(2, new BiFunction<Integer, String, String>() {
    @Override
    public String apply(Integer integer, String s) { // integer为key，s为value
        return integer+"="+s;
    }
});
hashMap.computeIfAbsent(233, new Function<Integer, String>() {
    @Override
    public String apply(Integer integer) {
        return 20+"";
    }
});
hashMap.computeIfPresent(2, new BiFunction<Integer, String, String>() {
    @Override
    public String apply(Integer integer, String s) {
        return integer+"="+s;
    }
});
```


```java
hashMap.forEach(new BiConsumer<Integer, String>() {
    @Override
    public void accept(Integer integer, String s) {
        System.out.println(integer+"="+s);
    }
});
```

```java
hashMap.merge(3, "hello", new BiFunction<String, String, String>() {
    @Override
    public String apply(String s, String s2) { // s为原值，s2为新值
        return s+s2;
    }
});
```

```java
hashMap.replaceAll(new BiFunction<Integer, String, String>() {
    @Override
    public String apply(Integer integer, String s) {
        return integer+"="+s;
    }
});
```

## 源码

### put

1. tab[]是否为null或空数组，若是则进行扩容
2. 计算(n-1)&hash得索引i，tab[i]是否为null，若是则直接创建新节点
3. 若不为null，则判断tab[i].key是否与添加的是否相等
4. 判断tab[i]是否是红黑树节点
5. 若3、4均否，则遍历tab，找到key相同的那个节点或在tab末尾创建新节点
6. ++size，判断是否扩容

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length; // 扩容
    if ((p = tab[i = (n - 1) & hash]) == null) // 计算(n-1)&hash获得位桶下标
        tab[i] = newNode(hash, key, value, null); // tab[(n-1)&hash]为空，即数组的这个位置当前没有其它元素，可直接创建新节点
    else { // 该表位置有节点，存在hash冲突
        Node<K,V> e; K k;
        if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
            e = p; // 该表位置的第一个节点key值相同
        else if (p instanceof TreeNode) // 第一个节点是红黑树节点
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) { // 遍历链表
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null); // 在链表末尾创建新节点
                    if (binCount >= TREEIFY_THRESHOLD - 1) // 遍历次数binCount（即链表长度-1）已达(8-1)个
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash && ((k = e.key) == key || (key != null && key.equals(k)))) // 该表位置的某个节点key值相同
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value; // 保存原值
            if (!onlyIfAbsent || oldValue == null)
                e.value = value; // 重置
            afterNodeAccess(e);
            return oldValue; // 返回原值
        }
    }
    ++modCount;
    if (++size > threshold)
        resize(); // 大于临界值，进行扩容2倍
    afterNodeInsertion(evict);
    return null;
}
```

### resize

1. 若table为null或容量为0，则使用默认值16扩容，临界值为`16*0.75`，否则2
2. 若容量是否超过设定的最大容量，重置临界值不扩容返回，否则3
3. 容器容量加倍，临界值加倍
4. 若容器原来不为空，则需迁移数据
5. oldTab[i]链表中数据分别移至newTab[i]和newTab[i+oldTab.length]中，如下图

![](http://7xt0u5.com1.z0.glb.clouddn.com/1347de9f-82b1-49ae-bb1e-612cf33d0363.png)

```java
final Node<K,V>[] resize() { // 扩容
    Node<K,V>[] oldTab = table; // 原表
    int oldCap = (oldTab == null) ? 0 : oldTab.length; // 原表大小
    int oldThr = threshold; // 原扩容临界值
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) { // 原表大小超过最大容量
            threshold = Integer.MAX_VALUE; // 临界值设为最大Integer
            return oldTab; // 不进行扩容直接返回
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY && oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // 临界值加倍
    }
    else if (oldThr > 0)
        newCap = oldThr; // 若oldCap<=0&&oldThr>0，新容量大小取原扩容临界值
    else { // 无参构造函数初始化HashMap，表未初始化
        newCap = DEFAULT_INITIAL_CAPACITY; // 新表大小为默认值16
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY); // 新扩容临界值为0.75*16=12
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ? (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) { // 原表不为空，重新将数据移至新表
        for (int j = 0; j < oldCap; ++j) { // 遍历表
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e; // 只有一个节点时，重算位置赋值
                else if (e instanceof TreeNode) // 红黑树进行旋转
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else {
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 将链表拆分成两组，e.hash&oldCap为0和不为0
                        if ((e.hash & oldCap) == 0) { // 原来插入时计算的是e.hash&(oldCap-1)
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead; // e.hash&oldCap == 0
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead; // e.hash&oldCap != 0
                    }
                }
            }
        }
    }
    return newTab; // 返回新表
}
```

### get

1. (table.length-1)&hash获取位桶下标
2. 检查第一个节点是否是目标节点，若是则返回，若不是则3
3. 判断它是否是红黑树节点（instanceof），若是则使用红黑树查找算法，若不是则4
4. 遍历整个链表，直到返回null或目标节点

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 && (first = tab[(n - 1) & hash]) != null) { // 获取位桶的第一个节点
        if (first.hash == hash && ((k = first.key) == key || (key != null && key.equals(k)))) // 检查第一个节点是否是要找的节点
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode) // 如果第一个节点是红黑树的节点
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do { // 遍历整个链表
                if (e.hash == hash && ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```
