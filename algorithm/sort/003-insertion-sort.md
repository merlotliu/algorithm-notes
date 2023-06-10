---
layout: post
title: 插入排序
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [排序算法, 插入排序]
categories: [算法&数据结构]
comments: true
---

## 插入排序

对于一组共有 N 个数的数组，共进行 N-1 轮移动。每一轮从当前位置向前看，依次比较前面的数，比前面的数小则交换，比前面的数大或者相等或前面没有数则停止，此时该部分的数已经有序：

- 第一轮：从第2个位置往前看，调整为有序；
- 第二轮：从第3个位置往前看，调整为有序

类似，打扑克抓牌顺牌的过程。

### 时间复杂度

数据状况会影响插入排序的时间复杂度

$$O(n^2)$$表示最差状况下的时间复杂度

### 代码

```
#pragma once
class Code03InsertionSort {
public:
	void static insertionSort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		for (int i = 1; i < len; i++) {
			for (int j = i; j > 0 && arr[j - 1] > arr[j]; j--) {
				swap(arr, j - 1, j);
			}
		}
	}

	void static swap(int *arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}
};

```

