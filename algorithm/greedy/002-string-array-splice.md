---
layout: post
title: 字符数组拼接
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [贪心算法]
categories: [算法&数据结构]
comments: true
---

## 字符数组拼接

给定一个字符串数组，怎么样拼接可以使最后的字符串的字典序最小。

> 设想一本英语字典里的单词，何者在前何者在后？
>
> 显然的做法是先按照第一个字母、以 a、b、c……z 的顺序排列；如果第一个字母一样，那么比较第二个、第三个乃至后面的字母。如果比到最后两个单词不一样长（比如，sigh 和 sight），那么把短者排在前。
>
> 通过这种方法，我们可以给本来不相关的单词强行规定出一个顺序。“单词”可以看作是“字母”的[字符串](https://baike.baidu.com/item/字符串?fromModule=lemma_inlink)，而把这一点推而广之就可以认为是给对应位置元素所属集合分别相同的各个有序[多元组](https://baike.baidu.com/item/多元组?fromModule=lemma_inlink)规定顺序：下面用形式化的语言说明。

或者可以将一个字符串看做26进制的数，然后比较大小，对于长度不一的数，可以在最后面补0。即b与abc比较时，变成b00与abc比较。

## 策略

如果前后两个字符串为a、b，若a.b < b.a，则将a放在前面，反之b放在前面，按照这一规则，将字符串数组排序；

以此排序好之后，遍历数组拼接得到的字符串即为字典序最小。

## 代码

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

## 证明

先证明传递性，再证明不按照这一方式拼接一定比这一方式字典序大。

不同贪心题目的证明都是不一样的，所以很麻烦。
