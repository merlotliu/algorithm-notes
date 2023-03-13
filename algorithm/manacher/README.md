---
layout: post
title: Manacher
date: 2022-12-13 22:49:55
updated: 2023-02-25 22:49:55
tags: [string, manacher]
categories: [算法&数据结构]
comments: true
---

## Manacher

在 O(n) 的时间复杂度下，求得字符串中的最长回文字串，并返回其长度或改回字串。

例1：[5. 最长回文子串 - 力扣（Leetcode）](https://leetcode.cn/problems/longest-palindromic-substring/description/)

对于求解最长回文子串的题目，我们很容易想到这样的暴力解法：

到达一个位置，从这个位置向两端扩展，依次判断两端的值是否相等，直到不相等，记录当前回文长度，并与之前记录的最大值比较；

很显然，这样做的时间复杂度为 O(n^2)，且无法判断偶数的回文串。

继而，我们想是否可以添加特殊字符，将偶数回文串扩展为奇数回文串？

例如 abba，可以扩充为 #a#b#b#a#，可以发现当判断到 两个 b 中间的 # 时，即可得出一个最长的回文串，回文直径为 9，回文半径为 5，而5（回文半径） - 1 = 4正好是原来回文串的长度。

而这样处理后的字符串，我们称为 manacher string。

值得说的一点是，特殊字符可以是任意字符，不需要是原来串中未出现的，因为我们可以发现，再比对的过程中，始终是，原来串中的字符和原来的字符比较，特殊字符和特殊字符比较。

## 主要框架

首先记住几个主要概念：

- 回文半径和回文直径：
  对于字符串 abcba 来说，除了 c 以外，其他字符位置的回文半径和直径都是 1 ，而 c 的回文直径为 5（从该位置扩展出去的回文字符总数），回文半径为 3（并不是直径/2，而是从当前位置到回文边界的字符总数）；
- **回文半径数组；**（最重要）
- 之前已经判断过的回文的最右回文右边界 R；
- 之前已经判断过的回文的最右回文中心 C；

整个 manacher 算法的匹配流程和暴力解法是一样，只是在后续的匹配中能够重用前面的一些匹配结果。总的匹配过程中，分为两个大情况：

- 当前索引 idx 在 R 之外 ： 按照暴力的中心扩展的方式判断回文串，并记录长度，更新 R 和 C，以及回文半径数组；
- 当前索引 idx 在 R 内 ：
  - 当前索引 idx 相对于 C 对称的 idx\*（C - (idx - C)） ，的回文串完全在 (L（左边界）, R)内 : 此时 idx 的回文长度与 idx\* 一样；
  - 当前索引 idx 相对于 C 对称的 idx\* 的回文串有部分在 C 为中心的回文串之外，则 此时 idx 的回文长度为，从当前索引到右边界的长度；
  - 当前索引 idx 相对于 C 对称的 idx\* 的回文串与 C 的左边界重合，此时无法确定 idx 为中心的回文串的最长长度，从右边界 + 1的位置通过暴力的方式继续扩展，直到无法扩展，更新 R、C以及回文半径数组。

```c++
#pragma once
#include <string>
#include <vector>

using namespace std;

class Solution {
public:
	int max(int a, int b) {
		return a > b ? a : b;
	}
	/* 添加特殊字符 # : abc-> #a#b#c# */
	string manachterString(string s) {
		string mstr(s.size() * 2 + 1, 0);
		for (int i = 0; i < s.size() * 2 + 1; i++) {
			mstr[i] = i % 2 == 0 ? '#' : s[i / 2];
		}
		return mstr;
	}
	/* 获取str 中pos位置为中心的回文串的半径，从 r 位置开始判断, 前面的长度r - pos认为是回文  */
	int getPalindromeRadius(string str, int pos, int r) {
		int l = 2 * pos - r;
		int radius = r - pos;
		while (l >= 0 && r < str.size() && str[l--] == str[r++]) radius++;
		return radius;
	}

	string longestPalindrome(string s) {
		if (s.size() <= 1) {
			return s;
		}
		string str = manachterString(s);
		int R = -1; /* 最右回文右边界 */
		int C = -1; /* 最右回文中心点 */
		vector<int> radius(str.size(), 1); /* 回文半径数组 */
		int max_idx = 0;
		for (int i = 0; i < str.size(); i++) {
			if (i > R) { /* 当前索引 i 在 R 之外, 暴力扩展 */
				radius[i] = getPalindromeRadius(str, i, i + 1);
				R = i + radius[i] - 1;
				C = i;
			}
			else {
				/* i 关于 C 的对称点回文区域在最右回文左边界右侧 : i的回文半径等于对称点半径 */
				if (radius[2 * C - i] < (R - i + 1)) {
					radius[i] = radius[2 * C - i];
					/* i 关于 C 的对称点回文区域有部分在最右回文外面 : i的回文半径等于i到右边界 */
				}
				else if (radius[2 * C - i] > (R - i + 1)) {
					radius[i] = R - i + 1;
					/* i 关于 C 的对称点回文区域左边界与 C 的左边界重合，需要暴力扩充判断 i 边界 */
				}
				else {
					radius[i] = getPalindromeRadius(str, i, R + 1);
					R = i + radius[i] - 1;
					C = i;
				}
			}
			//printf("%d %d\n", i, radius[i]);
			if (radius[i] > radius[max_idx]) {
				max_idx = i;
			}
		}
		return s.substr((max_idx - radius[max_idx] + 1) / 2, radius[max_idx] - 1);
	}
};
```

合并一些逻辑后的精简代码：

```c++
class Solution {
public:
    int min(int a, int b) {
        return a < b ? a : b;
    }
    /* 添加特殊字符 # : abc-> #a#b#c# */
    string manachterString(string s) {
        string mstr(s.size() * 2 + 1, 0);
        for(int i = 0; i < s.size() * 2 + 1; i++) {
            mstr[i] = i % 2 == 0 ? '#' : s[i / 2];
        }
        return mstr;
    }
    /* 获取str 中pos位置为中心的回文串的半径，从 r 位置开始判断, 前面的长度r - pos认为是回文  */
    int getPalindromeRadius(string str, int pos, int r) {
        int l = 2 * pos - r;
        int radius = r - pos;
        while(l >= 0 && r < str.size() && str[l--] == str[r++]) radius++;
        return radius;
    }

    string longestPalindrome(string s) { 
        if(s.size() <= 1) {
            return s;
        }   
        string str = manachterString(s);
        int R = -1; /* 最右回文右边界的下一个位置 */
        int C = -1; /* 最右回文中心点 */
        vector<int> radius(str.size(), 1); /* 回文半径数组 */
        int max_idx = 0;
        for(int i = 0; i < str.size(); i++) {
            radius[i] = i >= R ? 1 : min(radius[2 * C - i], R - i);
            /* 从 i + radius[i] 的位置开始扩展 */
            int l = i - radius[i];
            int r = i + radius[i];
            while(l > -1 && r < str.size() && str[l--] == str[r++]) {
                radius[i]++;
            }
            if(i + radius[i] > R) {
                R = i + radius[i];
                C = i;
            }
            //printf("%d %d\n", i, radius[i]);
            if(radius[i] > radius[max_idx]) {
                max_idx = i;
            }
        }
        return s.substr((max_idx - radius[max_idx] + 1) / 2, radius[max_idx] - 1);
    }
};
```

## Leetcode

### 中等

[5. 最长回文子串 - 力扣（Leetcode）](https://leetcode.cn/problems/longest-palindromic-substring/description/)
