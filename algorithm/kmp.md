---
layout: post
title: 字符串之 KMP 匹配算法
date: 2022-12-13 22:49:55
updated: 2023-02-25 22:49:55
tags: [string, kmp]
categories: [算法&数据结构]
comments: true
---

kmp 算法是匹配一个字符串 str 包含的字串 substr 的开始索引的算法（成功返回开始索引，失败返回 -1），时间复杂度为 O(N)，N为str的长度。

## 代码

- 当字符串长度 为0，或者 子串 substr 长度大于 str 的长度时，返回 -1；
- 计算 next 数组；
- 开始匹配：
  - 当字符相等时，同时增加 str 和 substr 的当前字符索引；
  - 不相等时：
    - 如果当前 substr 的索引还没有到最左端（0位置），通过 next 数组跳转到最长匹配前后缀的位置，继续下一轮匹配；
    - 如果已经到达最左端，则从当前 str 的索引开始已经没有办法再完成匹配，str 的索引向后移动一格；
- 最后，如果匹配成功，substr 索引等于 substr 的长度，返回 str 的索引 - substr 的索引，反之返回-1；

实际上，如果 str 来到 str_idx 位置，substr 来到 substr_idx 位置不相等时，substr_idx 跳转到 next[substr_idx] 位置，相当于 substr[0] 与 str[str_idx - next[substr_idx]] 对齐，然后继续匹配，当已经到最左端时，没办法找到对其的字符，只能移动 str_idx 了。

```c++
int kmp(std::string str, std::string substr) {
  if(str.size() == 0 || substr.size() == 0 || str.size() < substr.size()) {
        return -1;
    } 
    std::vector<int> next;
    getNextArr(substr, next); /* get next array */
    
    int str_idx = 0;
    int substr_idx = 0;
    while(str_idx < str.size() && substr_idx < str.size()) {
        if(str[str_idx] == substr[substr_idx]) {
            str_idx++;
            substr_idx++;
        } else if(substr_idx > 0) {
            substr_idx = next[substr_idx];
        } else {
            str_idx++;
        }
    }
    return substr_idx == str.size() ? str_idx - substr_idx : -1;
}
```

## 最长前缀和后缀匹配长度

对于一个字符串，如 abbcc、abcabc等，其最长前缀和后缀匹配长度分别为 0 和 3。

- abbcc : 长度为 4 时，abbc 和 bbcc 不相等；长度为 3 时，abb 和 bcc 不相等；长度为 2 时，ab 和 cc 不相等；长度为 1 时，a 和 c 不相等；所以最长匹配长度为 0 ；
- abcabc : 长度为 5 时，abcab 和 bcabc 不相等；长度为 4 时，abca 和 cabc 不相等；长度为 3 时，abc 和 abc 相等；所以最长匹配长度为 3 ；

在匹配的过程中，我们依次取 (len - 1) 到 0 的前后缀进行比对，找到相等的时候即为最长匹配长度。很显然整个字符串的前后缀即为其本身，没有任何意义，而为 0 的时候说明没有匹配的长度。

## next 数组

next 数组是 kmp 最核心的部分：

数组长度 : 字串 substr 的长度；

next[idx] : 

- 在 idx 位置，表示 substr 的索引为 idx 的字符的前面的字符字串的最长前缀和后缀匹配长度，即 substr[0 ~ (idx - 1)]的最长前缀和后缀匹配长度。如在字符串 abcabce 中，idx = 6 时，next[6] 的值即为 abcabc 的最长前缀和后缀匹配长度（3），idx 为 3 时，next[3] 的值即为 abc 的最长前缀和后缀匹配长度（0）；
- 同时，我们规定 next[0] = -1, next[1] = 0（当然可以自定义合适的值，相应修改后续代码即可）；

显然，我们可以发现在 **idx 位置的 next 数组值与 substr 的 idx 位置字符无关**，与 0 ~ (idx - 1)的字符串的最长前缀和后缀匹配长度相关；

### next 数组赋值

- base case ：next[0] = -1，next[1] = 0；
  其实很好理解，当字符串只有一个字符时，前面为空串则不存在前后缀了；
  当字符串为两个字符时，前面仅有一个字符，最长前后缀匹配长度即为0（因为不能包含整个串）；
- idx 位置的 next 值 ：
  - 当 idx - 1 位置的字符 和 next[idx - 1] 位置的字符相等时，next[idx] = next[idx - 1] + 1;
  - 不相等时，则比对 next[next[idx - 1]] 位置和 idx - 1的字符（实际上是跳转到 idx - 1对应的最长前后缀匹配长度的位置，继续比对）；
  - 如果当前已经是 0 位置，还不能匹配，则说明没有匹配的长度，next[idx] = 0；

- 如：abceabcd ，已知前7个位置的值分别为 { -1, 0, 0, 0, 1, 2, 3 }，求 next[7]：
  - idx = 7，当前比对位置 cur = 前一个位置 next[6] = 3，比较 idx 位置字符 d 和 next[6] = 3 位置字符 e，显然不相等；
  - 当前比对位置 cur = next[next[6]] = next[3] = 0，即 a（0） 和 d（7） 比对，显然不相等；
  -  此时比对位置已经为 0，但仍然不相等，说明没有匹配长度所以，next[7]为0；

事实上，kmp 的算法流程，使用next 数组的逻辑与此非常类似。当然，我们也可以通过简单的字符串暴力匹配的方式获得 next 数组（较为费时而已）。这一方法的时间复杂度为 O(M)，M为字符串长度。

#### 代码

```c++
void getNextArr(std::string str, std::vector<int> &next) {
    if(str.size() == 0) return ;
    next[0] = -1; // 如果后面使用索引而不是next数组的值判断是否到达左边界可以不赋值为-1
    if(str.size() == 1) return ;
    next[1] = 0;
    int idx = 2;
    int cn = next[1];
    while(idx < str.size()) {
        if(str[idx - 1] == str[next[idx - 1]]) {
            next[idx++] = ++cn;
            //next[idx++] = cn + 1; cn++;
        } else if (idx > 0) {
            cn = next[cn];
        } else {
            next[idx++] = 0;
        }
    }
    return ;
}
```

## Leetcode

### 简单

[459. 重复的子字符串 - 力扣（Leetcode）](https://leetcode.cn/problems/repeated-substring-pattern/)

## Reference 
