---
layout: post
title: 优先级队列
date: 2022-12-13 22:49:55
updated: 2023-02-27 22:49:55
tags: [优先级队列, 大根堆, 小根堆]
categories: [算法&数据结构]
comments: true
---

## 重写比较器

当我们需要向优先级队列中添加复杂数据结构时，需要重写比较器。比较器的重写可以通过类或者lambda表达式。

**优先级比较器返回 false 第一个参数放在前面，反之后面的数放在前面**（有些困惑，因为在 `sort`对`vector`等容器排序时，返回 `true` 第一个参数放前面）。如 `lhs` 和 `rhs`，在 比较器为 `return lhs > rhs` 的情况下，`lhs` 和 `rhs` 中较小的数在前面，较大的数在后面。

### 类

如果通过类的形式，则应该像下面一样创建一个类并重写其 `()` 运算：

```c++
template<typename T>
class comparator {
public:
    bool operator()(const T &lhs, const T &rhs) {
        return lhs < rhs; 
        /* 如果是复杂数据结构，则还需要参考 T 的 < 操作，如果没有重写或为简单数据结构，则构建大根堆 */
        // return lhs > rhs;
        // 小根堆
    }
};

# 使用
priority_queue<T, vector<T>, comparator<T>> q;
```

### lambda 表达式

由于 `stl` 优先级队列的第三个参数为模板类类型，所以需要通过 `decltype` 进行一个自动推导出表达式函数指针的类型。

```c++
# 定义比较器
auto cmp = [](const T &left, const T &right) { return (left) < (right);};
# 使用
# decltype 自动推到出类型
# 并将比较器传给 优先级队列
priority_queue<T, vector<T>, decltype(cmp)> q(cmp);

# 等价于
# bool (*cmp)(const int &a, const int &b) = [](const int &a, const int &b){
#		return a > b;
#	};
# priority_queue<T, vector<T>, bool (*)(const int &a, const int &b)> q(cmp);
# 即 decltype(cmp) 被推导为了 bool (*)(const int &a, const int &b)
```

### 其他

事实上，应该还有一种方式间接的修改比较器的结果。我们知道 `stl` 提供了 `std::greater<T>` 和 `std::less<T>` 两个模板比较器，那么如果我们重写 `T` 的 `>` （greater）或 `<` （less），配合上对应的模板比较器，是不是也能实现对应的比较功能？答案当然是 OK 的。

## 经典题型

### 1 TopK

[347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/description/)

1. 统计所有数出现的频率；
2. 构建一个长度为 k 的小根堆，由于存放的是 pair 类型，所以还需要重写比较器；
3. 将统计的频数加入到小根堆，小根堆大小超过 k，则弹出堆顶元素；
4. 将小根堆的元素放入结果集并返回;

```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int> res;
        unordered_map<int, int> times;
        for(auto num : nums) {
            times[num]++;
        }
        auto cmp = [](const pair<int, int> &l, const pair<int, int> &r){
            return l.second > r.second;
        };
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> pri_que(cmp);
        for(auto time : times) {
            pri_que.push(time);
            if(pri_que.size() > k) {
                pri_que.pop();
            }
        }
        while(!pri_que.empty()) {
            res.push_back(pri_que.top().first);
            pri_que.pop();
        }
        return res;
    }
};
```



### 2 数组中位数

要求：查询时间复杂度为O(1)；

#### 思路

维护两个堆，一个大根堆和一个小根堆。

首先将第一个数放入大根堆。

接着遍历数组，进行以下操作：

1. 与大根堆顶元素比较，大于则进入小根堆，小于进入大根堆；
2. 如果两个堆的长度大于等于2，相互调整为小于2；

查询时，两个堆大小一样说明为偶数，返回堆顶元素和的一半。

不一样时，返回大的那个堆的堆顶元素。

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

### 3 Dijkstra算法优化

改写堆以优化Dijkstra算法算法。