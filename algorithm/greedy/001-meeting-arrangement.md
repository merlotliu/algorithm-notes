---
layout: post
title: 会议室安排
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [贪心算法]
categories: [算法&数据结构]
comments: true
---

## 会议室安排

在一天的时间内，最多可以安排多少个会议？

给定n组数据，每组数据包含会议的开始时间和结束时间，同一时间内只能存在一个会议。

如：[[1, 3], [3, 4], [2, 5], [7, 9], [2, 10]]

## 策略

1. 将会议按照结束时间递增的顺序排序；
2. 每次记录上一次会议的结束时间，初始为最小值；
3. 如果当前会议的开始时间晚于上一次会议的结束时间，该会议可以被选中，会议结束时间更新。
4. 重复3直至全部会议遍历完，返回结果。

## 代码

```cpp
int getMaxArrangeMeetingCount(std::vector<std::vector<int>> &meetings) {
	auto cmp = [](std::vector<int> &a, std::vector<int> &b) {
		return a[1] < b[1];
	};
	std::sort(meetings.begin(), meetings.end(), cmp);
	int res = 0;
	int last_end_time = -1;
	for (auto m : meetings) {
		if (m[0] >= last_end_time) {
			last_end_time = m[1];
			res++;
		}
	}
	return res;
}
```
