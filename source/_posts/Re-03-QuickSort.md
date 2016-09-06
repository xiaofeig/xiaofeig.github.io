---
title: 快速排序
date: 2016-09-06 13:43:20
updated: 2016-09-06 13:43:23
categories: [Reading, 算法]
tags: [java, 排序, 数组]
---

# 排序思想

- **选择中轴元素pivotKey**，默认为第0个元素（或最左边、最右边、中间的中位数）
- i表示`<pivotKey`的元素的下标，从左至右扫描，遇到`>=pivotKey`停止
- j表示`>pivotKey`的元素的下标，从右至左扫描，遇到`<=pivotKey`停止
- 两次扫描均停止后，`i<j`或`i>=j`
- 若`i<j`，交换a[i]和a[j]；若`i>=j`，a[j]=pivotKey
- 对j的左右子树重复上述上述操作

<!-- more -->

# 示例

对序列{49, 38, 65, 97, 76, 13, 27, 49}进行快速排序。

第一趟排序过程：

```
key
↓
49, 38, 65, 97, 76, 13, 27, 49
↑                       ↑ ← ↑
i                       j   j
27, 38, 65, 97, 76, 13,   , 49
↑  --→  ↑               ↑
i       i               j
27, 38,   , 97, 76, 13, 65, 49
        ↑           ↑ ← ↑
        i           j   j
27, 38, 13, 97, 76,   , 65, 49
        ↑ → ↑       ↑
        i   i       j
27, 38, 13,   , 76, 97, 65, 49
            ↑↑ ←--  ↑
            ij      j
27, 38, 13, 49, 76, 97, 65, 49
```

|序号|结果|
|:---|:---|
|初始状态|{49 38 65 97 76 13 27 49}|
|一趟排序结果|{27 38 13} 49 {76 97 65 49}|
|二趟排序结果|{13} 27 {38} 49 {76 97 65 49}|
|三趟排序结果|13 27 38 49 {76 97 65 49}|
|四趟排序结果|13 27 38 49 {49 65} 76 {97}|
|五趟排序结果|13 27 38 49 49 {65} 76 97|


# Java实现

```java
public static void quickSort(Integer[] array) {
	qSort(array, 0, array.length-1);
}

/**
 * 递归排序左右子树
 * @param array
 * @param i 最左侧元素下标
 * @param j 最右侧元素下标
 */
public static void qSort(Integer[] array, int i, int j) {
	if(i<j) {
		int pivotIndex = partition(array, i, j);
		qSort(array, i, pivotIndex-1);
		qSort(array, pivotIndex+1, j);
	}
}

/**
 * 分区
 * @param array
 * @param i 最左侧元素下标
 * @param j 最右侧元素下标
 * @return 返回中轴下标
 */
public static int partition(Integer[] array, int i, int j) {
	Integer pivotKey = array[i]; // 最左侧元素作为中轴
	while(i<j) {
		while(i<j&&array[j]>=pivotKey){
			j--;
		}
		array[i] = array[j]; // 小于中轴的元素移至左侧
		while(i<j&&array[i]<=pivotKey) {
			i++;
		}
		array[j] = array[i]; // 大于中轴的元素移至右侧
	}
	array[i] = pivotKey; // 重置中轴
	return i; // 返回中轴下标
}
public static void main(String[] args) {
	Integer[] array = {49, 38, 65, 97, 76, 13, 27, 49};
	quickSort(array);
	System.out.println(Arrays.toString(array));
}
```

# 总结

**时间复杂度：**

最好、平均： $$O(nlog_2n)$$
最坏： $$O(n^2)$$

**特点：**

- 不稳定
- 适合顺序结构，不宜链式结构
- 当n较大时，在平均情况下快速排序是所有内部排序方法中速度最快的一种，所以适合初始记录无序、n较大时的情况
