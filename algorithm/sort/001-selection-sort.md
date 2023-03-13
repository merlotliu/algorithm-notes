---
layout: post
title: 选择排序
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [排序算法, 选择排序]
categories: [算法&数据结构]
comments: true
---

## 选择排序

对于一组共有 N 个数的数组，共进行 N-1 轮移动：

- 第一轮：1~N中找到最小的数，移动到第1个位置；
- 第二轮：2~N中找到最小的数，移动到第2个位置；
- 第N-1轮：N-1~N中找到最小的数，移动到第N-1个位置；

### 时间复杂度

O(n^2)

### 代码

```c++
#pragma once

class Code01SelectionSort {
public:
	void static selectionSort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return ;
		}
		for (int i = 0; i < len - 1; i++) {
			int min_idx = i;
			for (int j = i + 1; j < len; j++) {
				if (arr[min_idx] > arr[j]) {
					min_idx = j;
				}
			}
			if (min_idx != i) {
				swap(arr, i, min_idx);
			}
		}
		return ;
	}

	void static swap(int *arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}
};
```

