---
layout: post
title: 快速排序
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [排序算法, 快速排序]
categories: [算法&数据结构]
comments: true
---

## 荷兰国旗问题

给定一个数组arr，和一个数num，请将小于num的数放在数组的左边，等于数num的数放在数组的中间，大于num的数放在数组的右边。

要求：额外空间复杂度O(1)，时间复杂度O(N)。

### 解法

首先，考虑一个更为简单的场景，在这个数组arr中，仅把小于等于num的数放在数组的左边，大于num的数放在右边。

要求：额外空间复杂度O(1)，时间复杂度O(N)。

对于这一问题，定义小于等于num的区域为L，起始区域为(-1, l]，标记该区域的右边界的指针l初始为-1。

然后开始遍历数组，数组中i位置的数包含两种情况：

- i 位置数 <= num：指针l+1的位置与i位置数交换，i++，从而区域L向右拓展；
- i 位置数 > num：i++。

遍历完数组后，则问题解决：

```
void easyNetherlandFlag(int num, int *arr, int len) {
	if (arr == nullptr || len < 2) {
		return;
	}
	int l = -1;
	for (int i = 0; i < len; i++) {
		if (arr[i] <= num) {
			swap(arr, i, ++l);
		}
		std::cout << arr[i] << std::endl;
	}
	return;
}
```



接着，解决荷兰国旗问题。定义小于num的区域为L，标记区域L的右边界的指针l初始值为-1，定义大于num的区域为R，标记区域R的左边界的指针r的初始值为n（数组长度）。

遍历数组时，会有三种情况：

- i 位置数 < num：指针l+1的位置与i位置数交换，i++（因为换过来的值一定是小于或等于num的数），从而区域L 向右拓展；
- i 位置数 = num：i++；
- i 位置数 > num：指针r-1的位置与i位置数交换，i不变（因为换过来的值尚未判断），从而区域R 向左拓展；

直到i遇到r时结束，问题也就解决：

```
void netherlandFlag(int num, int *arr, int len) {
	if (arr == nullptr || len < 2) {
		return;
	}
	int l = -1;
	int r = len;
	for (int i = 0; i < r; ) {
		if (arr[i] < num) {
			swap(arr, i++, ++l);
		} else if (arr[i] > num) {
			swap(arr, i, --r);
		} else {
			i++;
		}
	}
	return;
}
```



## 快排

影响快排效率的最重要因素就是，划分值的选取，选取的好时间复杂度趋向于O(n*logn)，反之趋向于O(n^2)。

具体步骤：

- 选取划分值：
    - 选择最后一个数作为划分值；
    - 每次随机选择一个数作为划分值；
- 根据划分值，划分区域（在这一部分遍历时要从区域的左端开始而不是从0开始）：
    - 小于划分值的部分在最左侧；
    - 等于划分值的部分在中间；
    - 大于划分值的部分在最右侧；
- 细分子区域（递归）；

```c++
#pragma once
#include <random>
#include <time.h>
#include <iostream>

class QuickSort {
public:
	void sort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		srand((unsigned int)time(nullptr));
		quickSort(arr, 0, len-1);
	}

private:
	void quickSort(int *arr, int L, int R) {
		// end condition
		if (L >= R) {
			return;
		}

		//int flag = arr[R]; // 2.0
		// random flag
		int flag = arr[(rand() % (R - L + 1)) + L]; // 3.0
		//std::cout << (R - L + 1) << " " << r << " " << flag << std::endl;
		// partition
		std::vector<int> bound = partition(flag, arr, L, R);
		// recursion
		quickSort(arr, L, bound[0]);
		quickSort(arr, bound[1], R);
	}

	std::vector<int> partition(int flag, int *arr, int L, int R) {
		// Notes: start from L, not 0
		int less = L - 1; 
		int more = R + 1;

		while (L < more) {
			if (arr[L] < flag) {
				swap(arr, L++, ++less);
			} else if (arr[L] > flag) {
				swap(arr, L, --more);
			} else {
				L++;
			}
		}
		return {less, more};
	}
};
```

### Notes

- 一定要特别注意边界条件；
- 每次遍历的的起始、终止条件；

### 时间复杂度

取决于选取的划分值

最好：O(n*logn) 最差：O(n^2) 

空间复杂度（主要用于记录划分值）：

最好：O(logn) 最差：O(n)