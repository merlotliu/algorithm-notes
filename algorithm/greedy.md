---
layout: post
title: 贪心算法
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [贪心算法]
categories: [算法&数据结构]
comments: true
---
# 贪心算法

对问题求解时，不从整体上考量，仅选择局部最优的策略，最后得到优良解的方法。

贪心算法的优良解不一定总是最优解。

在该算法中，选对贪心策略，就能保证最后的最优解是由局部最优解构成的。

## 解题技巧

证明贪心策略能得到全局最优解是很麻烦的。通常：

1. 可以实现一个暴力解法作为对数器；
2. 然后根据题目、数据，提出贪心策略A、B、C......
3. 将以上策略与对数器的暴力解测试，往往能够得出最优策略。

总之，不要纠结于贪心策略的证明。

## 经典题型

### 1 会议室安排

在一天的时间内，最多可以安排多少个会议？

给定n组数据，每组数据包含会议的开始时间和结束时间，同一时间内只能存在一个会议。

如：[[1, 3], [3, 4], [2, 5], [7, 9], [2, 10]]

#### 策略

1. 将会议按照结束时间递增的顺序排序；
2. 每次记录上一次会议的结束时间，初始为最小值；
3. 如果当前会议的开始时间晚于上一次会议的结束时间，该会议可以被选中，会议结束时间更新。
4. 重复3直至全部会议遍历完，返回结果。

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



### 2 字符数组拼接

给定一个字符串数组，怎么样拼接可以使最后的字符串的字典序最小。

> 设想一本英语字典里的单词，何者在前何者在后？
>
> 显然的做法是先按照第一个字母、以 a、b、c……z 的顺序排列；如果第一个字母一样，那么比较第二个、第三个乃至后面的字母。如果比到最后两个单词不一样长（比如，sigh 和 sight），那么把短者排在前。
>
> 通过这种方法，我们可以给本来不相关的单词强行规定出一个顺序。“单词”可以看作是“字母”的[字符串](https://baike.baidu.com/item/字符串?fromModule=lemma_inlink)，而把这一点推而广之就可以认为是给对应位置元素所属集合分别相同的各个有序[多元组](https://baike.baidu.com/item/多元组?fromModule=lemma_inlink)规定顺序：下面用形式化的语言说明。

或者可以将一个字符串看做26进制的数，然后比较大小，对于长度不一的数，可以在最后面补0。即b与abc比较时，变成b00与abc比较。

#### 策略

如果前后两个字符串为a、b，若a.b < b.a，则将a放在前面，反之b放在前面，按照这一规则，将字符串数组排序；

以此排序好之后，遍历数组拼接得到的字符串即为字典序最小。

```cpp
std::string getMinJointString(std::vector<std::string> &strs) {
	auto cmp = [](std::string &a, std::string &b) {
		std::string str1 = a + b;
		std::string str2 = b + a;
		return str1.compare(str2);
	};
	std::sort(strs.begin(), strs.end(), cmp);
	std::string res;
	for (auto str : strs) {
		res += str;
	}
	return res;
}
```

#### 证明

先证明传递性，再证明不按照这一方式拼接一定比这一方式字典序大。

不同贪心题目的证明都是不一样的，所以很麻烦。
