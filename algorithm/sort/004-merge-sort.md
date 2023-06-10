---
layout: post
title: 归并排序
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [排序算法, 归并排序]
categories: [算法&数据结构]
comments: true
---

## 归并排序

将数组分为左右两部分，分别对这两部分进行排序，排序完成后，合并两部分的结果。

实际上，不断的将数组进行拆解，拆解到每一部分只剩下一个数的时候，一定是有序的。

合并时，首先申请一个额外的数组，然后同时遍历并依次比较两个部分的内容，每次将数小的添加进辅助数组，直到其中一部分内容遍历完，将另外一个部分全部添加到辅助数组的末尾。最后将辅助数组的内容赋值到原数组的对应位置即可。

```c++
class MergeSort{
public:
	void sort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		mergeSort(arr, 0, len - 1);
	}

	void mergeSort(int *arr, int l, int r) {
		if (l == r) {
			return;
		}
		int m = l + ( (r - l) >> 1 ); // >> 优先级低于 +，应该加括号
		mergeSort(arr, l, m);
		mergeSort(arr, m+1, r);
		merge(arr, l, m, r);
		return;
	}

	void merge(int *arr, int l, int m, int r) {
		int *help = new int[r - l + 1];
		int i = 0; 
		int p1 = l;
		int p2 = m+1;
		while (p1 <= m && p2 <= r) {
			help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
		}
		while (p1 <= m) {
			help[i++] = arr[p1++];
		}
		while (p2 <= r) {
			help[i++] = arr[p2++];
		}
		for (int j = l, i = 0; j <= r; ) { 
			arr[j++] = help[i++];
		}
		delete[] help;
		return;
	}
};
```

### 时间复杂度

时间复杂度O(nlogn)，空间复杂度O(n)



### 小和&逆序对

#### 小和问题

在一个数组中，每个数左边比当前数小的数累加起来，叫做这个数组的小和，求一个数组的小和。

例：[1, 3, 4, 2, 5] 1左边没有比1小的数；3左边比三小的数：1；4左边比4小的数：1, 3；2左边比2小的数：1；5左边比5小的数：1, 3, 4, 2；故小和：1+ 1+3 + 1+ 1 + 3 + 4 + 2=16

#### 逆序对问题

在一个数组中，左边的数比右边的数大，则两个数构成一个逆序对，请打印所有逆序对。

#### 解法

对于小和问题，我们发现，问题等价于，右边有几个数比当前数大则累加几次，结合归并排序，我们即可获得优于O(n^2)的解法。

对于合并部分的逻辑需要进行一些改动：

- 判断两部分大小的时候，如果相等，则不产生小和，并且先拷贝右侧内容；
- 如果左侧小于右侧，产生小和，右侧应该有右边界索引减去右侧当前指针索引下标再加1的数量，乘上当前左侧数，即为当前部分小和；例如：左侧为[1,3,4]，右侧为[2,5]时（合并时各部分一定是已经排好序的，这样才能确定后面有几个数比当前数大），对于1来说，右侧当前索引为0，边界索引为1，则小和为(1-0+1)*1=2；

```cpp
int getSmallSum(int *arr, int l, int r) {
	if (l == r) {
		return 0;
	}
	int m = l + ((r - l) >> 1);
	return getSmallSum(arr, l, m) + getSmallSum(arr, m + 1, r) + getPartSmallSum(arr, l, m, r);
}

int getPartSmallSum(int *arr, int l, int m, int r) {
	int *help = new int[r - l + 1];
	int i = 0;
	int p1 = l;
	int p2 = m + 1;
	int res = 0;
	while (p1 <= m && p2 <= r) {
		res += arr[p1] < arr[p2] ? (r - p2 + 1) * arr[p1] : 0;
		help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
	}
	while (p1 <= m) {
		help[i++] = arr[p1++];
	}
	while (p2 <= r) {
		help[i++] = arr[p2++];
	}
	for (int j = l, i = 0; j <= r; ) {
		arr[j++] = help[i++];
	}
	delete[] help;
	return res;
}
```

