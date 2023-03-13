---
layout: post
title: 数组中位数
date: 2022-12-13 22:49:55
updated: 2023-02-27 22:49:55
tags: [优先级队列, 大根堆, 小根堆]
categories: [算法&数据结构]
comments: true
---

## 数组中位数

要求：查询时间复杂度为O(1)；

### 思路

维护两个堆，一个大根堆和一个小根堆。

首先将第一个数放入大根堆。

接着遍历数组，进行以下操作：

1. 与大根堆顶元素比较，大于则进入小根堆，小于进入大根堆；
2. 如果两个堆的长度大于等于2，相互调整为小于2；

查询时，两个堆大小一样说明为偶数，返回堆顶元素和的一半。

不一样时，返回大的那个堆的堆顶元素。

## 代码

```cpp
#include <queue>
#include <iostream>
#include <algorithm>

class Median{
public:
	void insert(int num) {
		int max_heap_top = max_heap.empty() ? INT_MAX : max_heap.top();
		if (num < max_heap_top) {
			max_heap.push(num);
		} else {
			min_heap.push(num);
		}
		// adjust
		if (max_heap.size() > min_heap.size() + 1) {
			min_heap.push(max_heap.top());
			max_heap.pop();
		} else if (min_heap.size() > max_heap.size() + 1) {
			max_heap.push(min_heap.top());
			min_heap.pop();
		}
	}

	int getMedian() {
		if (min_heap.empty() && max_heap.empty()) return INT_MIN;
		if (min_heap.size() > max_heap.size()) {
			return min_heap.top();
		}
		if (min_heap.size() < max_heap.size()) {
			return max_heap.top();
		}
		return ((min_heap.top() + max_heap.top()) / 2);
	}
public:
	std::priority_queue<int, std::vector<int>, std::less<int>> max_heap;
	std::priority_queue<int, std::vector<int>, std::greater<int>> min_heap;
};

std::vector<int> generateRandomArray(int max_size, int max_val, int min_val) {
	int size = rand() % max_size;
	std::vector<int> res;
	for (int i = 0; i < size; i++) {
		int num1 = (rand() % (max_val - min_val + 1)) + min_val;
		int num2 = (rand() % (max_val - min_val + 1)) + min_val;
		res.push_back(num1 - num2);
	}
	return res;
}

void printArray(std::vector<int> &arr) {
	for (auto num : arr) {
		std::cout << num << " ";
	}
	std::cout << std::endl;
}
```