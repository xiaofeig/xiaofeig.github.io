---
title: 集合框架TreeMap
date: 2016-08-17 21:48:40
updated: 2016-08-17 21:48:45
categories: [Learning, Java]
tags: [java]
---

# [TreeMap](http://www.jianshu.com/p/210c2f4ca130)

TreeMap是基于[红黑树（Red-Black Tree）](http://coding.xiaofeig.cn/2016/08/14/Le-17-Red-Black%20Tree/)实现。

<!-- more -->

## 结构

![](http://7xt0u5.com1.z0.glb.clouddn.com/92c52e6b-f8ff-4f8b-9716-17b3ee0055e3.png)

## 属性

```java
// 比较器
private final Comparator<? super K> comparator;
// 根节点
private transient Entry<K,V> root;
// 树中键值对个数
private transient int size = 0;
// 树结构的修改次数
private transient int modCount = 0;
```

```java
// 节点
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key; // 键
    V value; // 值
    Entry<K,V> left; // 左子节点
    Entry<K,V> right; // 右子节点
    Entry<K,V> parent; // 父节点
    boolean color = BLACK; // 颜色属性，默认黑色
    ...
}
```

## 构造

```java
public TreeMap() {
    comparator = null; // 比较器为null，使用key的自然顺序来维持TreeMap的顺序，Key要求实现Comparable接口
}
public TreeMap(Comparator<? super K> comparator) {
    this.comparator = comparator; // 设置比较器
}
public TreeMap(Map<? extends K, ? extends V> m) {
    comparator = null; // 比较器为null
    putAll(m); // 放置所有元素
}
public TreeMap(SortedMap<K, ? extends V> m) {
    comparator = m.comparator(); // 根据SortedMap的比较器维持TreeMap的顺序
    try {
        buildFromSorted(m.size(), m.entrySet().iterator(), null, null);
    } catch (java.io.IOException cannotHappen) {
    } catch (ClassNotFoundException cannotHappen) {
    }
}
```

## 方法

```java
V put(K key, V value) // 添加键值对
void putAll(Map<? extends K,? extends V> map) // 添加map中所有

V remove(Object key) // 根据key移除
void clear() // 移除所有键值对
Map.Entry<K,V> pollFirstEntry() // 移除并返回最小key的键值对
Map.Entry<K,V> pollLastEntry() // 移除并返回最大key的键值对

V replace(K key, V value) // 替换某key的value
boolean replace(K key, V oldValue, V newValue) // 替换某键值对的value

int size() // 键值对个数
Comparator<? super K> comparator() // 返回比较器，根据key的自然排序则返回null

V get(Object key) // 指定key的value

Set<K> keySet() // key集合
NavigableSet<K> navigableKeySet() // 和keySet()一样
Collection<V> values() // 值集合
Set<Map.Entry<K,V>> entrySet() // 映射的键值对集合

boolean containsKey(Object key) // 是否包含某键
boolean containsValue(Object value) // 是否包含某值

NavigableSet<K> descendingKeySet() // key的降序集合
NavigableMap<K,V> descendingMap() // 键值对的降序映射

Map.Entry<K,V> ceilingEntry(K key) // key的上限（>=）键值对
Map.Entry<K,V> floorEntry(K key) // key的下限（<=）键值对
K ceilingKey(K key) // key的上限（>=）键
K floorKey(K key) // key的下限（<=）键

Map.Entry<K,V> firstEntry() // 最小的键值对
Map.Entry<K,V> lastEntry() // 最大的键值对
K firstKey() // 最小的key
K lastKey() // 最大的key

Map.Entry<K,V> higherEntry(K key) // 大于key的第一个键值对
K higherKey(K key) // 大于key的第一个键
Map.Entry<K,V> lowerEntry(K key) // 小于key的第一个键值对
K lowerKey(K key) // 小于key的第一个键

SortedMap<K,V> headMap(K toKey) // portion < tokey 的部分
NavigableMap<K,V> headMap(K toKey, boolean inclusive) // portion < tokey 的部分（inclusive是否包含边界）
SortedMap<K,V> subMap(K fromKey, K toKey) // fromKey <= portion < toKey 的部分
NavigableMap<K,V> subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive) // fromKey < portion < toKey 的部分（inclusive是否包含边界）
SortedMap<K,V> tailMap(K fromKey) // portion >= tokey 的部分
NavigableMap<K,V> tailMap(K fromKey, boolean inclusive) // portion > tokey 的部分（inclusive是否包含边界）

void forEach(BiConsumer<? super K,? super V> action) // 对每个键值对执行相同操作，异常中断
void replaceAll(BiFunction<? super K,? super V,? extends V> function) // 对每个键值对执行相同的替换操作，异常中断

Object clone() // 浅复制TreeMap实例
```



## 示例

```java
// 遍历treeMap
treeMap.forEach(new BiConsumer<Integer, String>() {
    @Override
    public void accept(Integer integer, String s) {
        System.out.println(integer+"->"+s);
    }
});
// 替换treeMap所有元素
treeMap.replaceAll(new BiFunction<Integer, String, String>() {
    @Override
    public String apply(Integer integer, String s) {
        return s+"....";
    }
});
```

## 源码

源码分析可参考[红黑树](http://coding.xiaofeig.cn/2016/08/14/Le-17-Red-Black%20Tree/)这篇文章来看。

### put

```java
public V put(K key, V value) {
    Entry<K,V> t = root;
    // 根节点为null，直接创建一个节点返回
    if (t == null) {
        compare(key, key); // 类型校验，是否为null
        root = new Entry<>(key, value, null);
        size = 1;
        modCount++;
        return null;
    }
    int cmp;
    Entry<K,V> parent;
    Comparator<? super K> cpr = comparator;
    // 比较器不为null，使用比较器维持TreeMap的元素顺序
    if (cpr != null) {
        // 循环，获取插入后的父节点
        do {
            // 记录父节点
            parent = t;
            cmp = cpr.compare(key, t.key);
            // key<t.key，插入到节点的左边
            if (cmp < 0)
                t = t.left;
            // key>t.key，插入到节点的右边
            else if (cmp > 0)
                t = t.right;
            // key==t.key，覆盖原值即可，并返回原值
            else
                return t.setValue(value);
        } while (t != null);
    }
    // 比较器为null，使用key作为比较器进行比较，key需实现Comparable接口
    else {
        if (key == null)
            throw new NullPointerException();
        @SuppressWarnings("unchecked")
            Comparable<? super K> k = (Comparable<? super K>) key;
        // 同上面一样，获取插入后的父节点
        do {
            parent = t;
            cmp = k.compareTo(t.key);
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
                t = t.right;
            else
                return t.setValue(value);
        } while (t != null);
    }
    // 创建新节点
    Entry<K,V> e = new Entry<>(key, value, parent);
    if (cmp < 0)
        // key小于父节点，插入到左侧
        parent.left = e;
    else
        // key大于父节点，插入到右侧
        parent.right = e;
    // 插入后，保持红黑树平衡进行调整
    fixAfterInsertion(e);
    size++;
    modCount++;
    return null;
}
```

## fixAfterInsertion

```java
// 插入后，保持红黑树平衡进行调整
private void fixAfterInsertion(Entry<K,V> x) {
    x.color = RED;
    // 节点x不为null，不是根节点，且父节点为红色，才需要调整
    while (x != null && x != root && x.parent.color == RED) {
        // 节点x的父节点是祖父节点的左子节点
        if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
            // 获取x的叔叔节点
            Entry<K,V> y = rightOf(parentOf(parentOf(x)));
            // 叔叔节点为红色
            if (colorOf(y) == RED) {
                /*
                    GB
                    ├───────┐
                    PR      UR
                    │
                  x→NR
                */
                // 设置父节点为黑色
                setColor(parentOf(x), BLACK);
                // 设置叔叔节点为黑色
                setColor(y, BLACK);
                // 设置祖父节点为红色
                setColor(parentOf(parentOf(x)), RED);
                // x指向祖父节点，如果祖父节点的父节点也为红色，则继续循环
                x = parentOf(parentOf(x));
                /*
                  x→GR
                    ├───────┐
                    PB      UB
                    │
                    NR
                */
            // 叔叔节点为黑色
            } else {
                // 节点x是否是右子节点
                if (x == rightOf(parentOf(x))) {
                    /*
                        GB
                        ├───────┐
                        PR      UB
                        ├────┐
                           x→NR
                    */
                    // 获取父节点
                    x = parentOf(x);
                    // 进行左旋
                    rotateLeft(x);
                    /*
                        GB
                        ├───────┐
                        NR      UB
                        ├────┐
                      x→PR
                    */
                }
                /*
                    GB
                    ├───────┐
                    PR      UB
                    ├────┐
                  x→NR
                */
                // 设置父节点为黑色
                setColor(parentOf(x), BLACK);
                // 设置祖父节点为红色
                setColor(parentOf(parentOf(x)), RED);
                /*
                    GR
                    ├───────┐
                    PB      UB
                    ├────┐
                  x→NR
                */
                // 右旋祖父节点
                rotateRight(parentOf(parentOf(x)));
                /*
                    PB
                    ├───────┐
                  x→NR      GR
                            ├────┐
                                 UB
                */
            }
        // 节点x的父节点是祖父节点的右子节点
        } else {
            // 获取x的叔叔节点
            Entry<K,V> y = leftOf(parentOf(parentOf(x)));
            // 叔叔节点为红色
            if (colorOf(y) == RED) {
                /*
                    GB
                    ├───────┐
                    UR      PR
                            │
                          x→NR
                */
                // 设置父节点为黑色
                setColor(parentOf(x), BLACK);
                // 设置叔叔节点为黑色
                setColor(y, BLACK);
                // 设置祖父节点为红色
                setColor(parentOf(parentOf(x)), RED);
                // x指向父节点
                x = parentOf(parentOf(x));
                /*
                  x→GR
                    ├───────┐
                    UB      PB
                            │
                           NR
                */
            //如果叔叔节点是黑色
            } else {
                /*
                    GB
                    ├───────┐
                    UB      PR
                            │
                          x→NR
                */
                // 如果x是左子节点
                if (x == leftOf(parentOf(x))) {
                    /*
                        GB
                        ├───────┐
                        UB      PR
                                ├────┐
                              x→NR
                    */
                    // x指向父节点
                    x = parentOf(x);
                    // 父节点右旋
                    rotateRight(x);
                    /*
                        GB
                        ├───────┐
                        UB      NR
                                ├────┐
                                   x→PR
                    */
                }
                /*
                    GB
                    ├───────┐
                    UB      PR
                            ├────┐
                               x→NR
                */
                setColor(parentOf(x), BLACK);
                setColor(parentOf(parentOf(x)), RED);
                rotateLeft(parentOf(parentOf(x)));
                /*
                  x→PB
                    ├───────┐
                    GR      NR
                    ├────┐
                    UB
                */
            }
        }
    }
    // 设置根节点为黑色
    root.color = BLACK;
}
```

### rotateLeft

```java
// 左旋
// 重置三个父子节点
// 1. p 和 r.left
// 2. p.parent 和 r
// 3. r 和 p
private void rotateLeft(Entry<K,V> p) {
    if (p != null) {
        Entry<K,V> r = p.right;
        // 1. p 和 r.left 建立父子节点关系
        p.right = r.left;
        if (r.left != null)
            r.left.parent = p;
        // 2. p.parent 和 r 建立父子节点关系
        r.parent = p.parent;
        if (p.parent == null)
            root = r;
        else if (p.parent.left == p)
            p.parent.left = r;
        else
            p.parent.right = r;
        // 3. r 和 p 建立父子节点关系
        r.left = p;
        p.parent = r;
    }
}
```

### rotateRight

```java
// 右旋
// 重置三个父子节点
// 1. p 和 l.right
// 2. p.parent 和 l
// 3. l 和 p
private void rotateRight(Entry<K,V> p) {
    if (p != null) {
        Entry<K,V> l = p.left;
        // 1. p 和 l.right 建立父子节点关系
        p.left = l.right;
        if (l.right != null)
            l.right.parent = p;
        // 2. p.parent 和 l 建立父子节点关系
        l.parent = p.parent;
        if (p.parent == null)
            root = l;
        else if (p.parent.right == p)
            p.parent.right = l;
        else p.parent.left = l;
        // 3. l 和 p 建立父子节点关系
        l.right = p;
        p.parent = l;
    }
}
```

### remove

```java
public V remove(Object key) {
    // 根据key查找键值对
    Entry<K,V> p = getEntry(key);
    if (p == null)
        return null;
    // 保存原值
    V oldValue = p.value;
    // 删除键值对
    deleteEntry(p);
    // 返回原值
    return oldValue;
}
```

### getEntry

```java
// 根据key查找键值对
final Entry<K,V> getEntry(Object key) {
    if (comparator != null)
        // 使用比较器查找
        return getEntryUsingComparator(key);
    if (key == null)
        throw new NullPointerException();
    @SuppressWarnings("unchecked")
        Comparable<? super K> k = (Comparable<? super K>) key;
    Entry<K,V> p = root;
    // 循环遍历查找
    while (p != null) {
        int cmp = k.compareTo(p.key);
        if (cmp < 0)
            p = p.left;
        else if (cmp > 0)
            p = p.right;
        else
            return p;
    }
    return null;
}
```

```java
// 使用比较器查找
final Entry<K,V> getEntryUsingComparator(Object key) {
    @SuppressWarnings("unchecked")
        K k = (K) key;
    Comparator<? super K> cpr = comparator;
    if (cpr != null) {
        Entry<K,V> p = root;
        // 循环遍历查找
        while (p != null) {
            int cmp = cpr.compare(k, p.key);
            if (cmp < 0) // 查找左子树
                p = p.left;
            else if (cmp > 0) // 查找右子树
                p = p.right;
            else
                return p;
        }
    }
    return null;
}
```

### deleteEntry

```java
// 删除键值对
private void deleteEntry(Entry<K,V> p) {
    modCount++;
    size--;
    // 左右子节点均不为null
    if (p.left != null && p.right != null) {
        // 查找替代p删除的节点，即p的右子节点[的最左子节点]
        Entry<K,V> s = successor(p);
        // 只进行key和value的赋值操作，颜色不变
        p.key = s.key;
        p.value = s.value;
        p = s; // 指向新删除的节点
    }
    // 移除p后，获取p的替代节点
    Entry<K,V> replacement = (p.left != null ? p.left : p.right);
    if (replacement != null) {
        // 连接替代节点replacement和p的父节点的父子关系
        replacement.parent = p.parent;
        if (p.parent == null)
            root = replacement;
        else if (p == p.parent.left)
            p.parent.left  = replacement;
        else
            p.parent.right = replacement;

        // 取消p的所有节点关系，帮助GC
        p.left = p.right = p.parent = null;

        // p只有为黑色时，才需要平衡红黑树
        if (p.color == BLACK)
            fixAfterDeletion(replacement); // 参见下文的源码分析
    } else if (p.parent == null) { // p节点是唯一节点时
        root = null;
    } else { // 无子节点，父节点不为null
        // p只有为黑色时，才需要平衡红黑树
        if (p.color == BLACK)
            fixAfterDeletion(p); // p节点作为虚位节点传参

        if (p.parent != null) { // 取消p和父节点的关系
            if (p == p.parent.left)
                p.parent.left = null;
            else if (p == p.parent.right)
                p.parent.right = null;
            p.parent = null;
        }
    }
}
```

### fixAfterDeletion

```java
private void fixAfterDeletion(Entry<K,V> x) {
    // 循环，x是根节点或为红色均不需要调整
    while (x != root && colorOf(x) == BLACK) {
        // x是左子节点
        if (x == leftOf(parentOf(x))) {
            // 获取兄弟节点
            Entry<K,V> sib = rightOf(parentOf(x));
            // 兄弟节点为红色---------------第1种情况
            if (colorOf(sib) == RED) {
                // 设置兄弟节点为黑色
                setColor(sib, BLACK);
                // 设置父节点为红色
                setColor(parentOf(x), RED);
                // 左旋父节点
                rotateLeft(parentOf(x));
                // 兄弟节点指向父节点的右子节点（LB）
                sib = rightOf(parentOf(x));
            }
            // 兄弟节点的左右子节点均为黑色---------------第2种或第3种情况
            if (colorOf(leftOf(sib))  == BLACK && colorOf(rightOf(sib)) == BLACK) {
                // 设置兄弟节点为红色
                setColor(sib, RED);
                // x指向父节点
                x = parentOf(x);
            } else {
                // 兄弟节点的右子节点为黑色，左子节点为红色---------------第4种情况
                if (colorOf(rightOf(sib)) == BLACK) {
                    // 设置兄弟节点的左子节点为黑色
                    setColor(leftOf(sib), BLACK);
                    // 设置兄弟节点为红色
                    setColor(sib, RED);
                    // 右旋兄弟节点
                    rotateRight(sib);
                    // 兄弟节点指向父节点的右子节点
                    sib = rightOf(parentOf(x));
                }
                // ---------------第5种情况
                // 设置兄弟节点为父节点的颜色
                setColor(sib, colorOf(parentOf(x)));
                // 设置父节点为黑色
                setColor(parentOf(x), BLACK);
                // 设置兄弟节点的右子节点为黑色
                setColor(rightOf(sib), BLACK);
                // 左旋父节点
                rotateLeft(parentOf(x));
                // 已平衡，x指向根节点
                x = root;
            }
        } else { // 对称的
            Entry<K,V> sib = leftOf(parentOf(x));

            if (colorOf(sib) == RED) {
                setColor(sib, BLACK);
                setColor(parentOf(x), RED);
                rotateRight(parentOf(x));
                sib = leftOf(parentOf(x));
            }

            if (colorOf(rightOf(sib)) == BLACK &&
                colorOf(leftOf(sib)) == BLACK) {
                setColor(sib, RED);
                x = parentOf(x);
            } else {
                if (colorOf(leftOf(sib)) == BLACK) {
                    setColor(rightOf(sib), BLACK);
                    setColor(sib, RED);
                    rotateLeft(sib);
                    sib = leftOf(parentOf(x));
                }
                setColor(sib, colorOf(parentOf(x)));
                setColor(parentOf(x), BLACK);
                setColor(leftOf(sib), BLACK);
                rotateRight(parentOf(x));
                x = root;
            }
        }
    }
    // ---------------第3种情况
    setColor(x, BLACK);
}
```

## 迭代

### 遍历键

```java
Set<Integer> keys = treeMap.keySet();
Iterator<Integer> iterator = keys.iterator();
while(iterator.hasNext()) {
    Integer key = iterator.next();
    System.out.println(key + "=" + treeMap.get(key));
}
```

### 遍历值

```java
Collection<String> values = treeMap.values();
Iterator<String> iterator = values.iterator();
while(iterator.hasNext()){
    String value = iterator.next();
    System.out.println(value);
}
```

### 遍历键值对

```java
Set<Map.Entry<Integer, String>> entries = treeMap.entrySet();
Iterator<Map.Entry<Integer, String>> iterator  = entries.iterator();
while(iterator.hasNext()){
    Map.Entry<Integer, String> entry = iterator.next();
    System.out.println(entry.getKey()+"="+entry.getValue());
}
```

