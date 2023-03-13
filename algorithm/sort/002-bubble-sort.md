---
layout: post
title: 冒泡排序
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [排序算法, 冒泡排序]
categories: [算法&数据结构]
comments: true
---

## 冒泡排序

对于一组共有 N 个数的数组，共进行 N-1 轮移动：

- 第一轮：12比较将大数换到2，23比较将大数换到3，直到 N-1 和 N 比较将本轮最大数换到 N 位置；
- 第二轮：直到本轮最大的数放置到 N-1；
- 第 N-1 轮：将12中最大的数放到 2 。

### 时间复杂度

O(n^2)

### 代码

```
#pragma once
class Code02BubbleSort {
public:
	void static bubbleSort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}

		for (int j = len - 1; j > 0; j--) {
			for (int i = 0; i < j; i++) {
				if (arr[i] > arr[i + 1]) {
					swap(arr, i, i + 1);
				}
			}
		}
		return;
	}

	void static swap(int *arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}
};
```

