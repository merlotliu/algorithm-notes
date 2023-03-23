---
layout: post
title: KMP
date: 2022-12-13 22:49:55
updated: 2023-02-25 22:49:55
tags: [string, kmp]
categories: [算法&数据结构]
comments: true
---

## KMP

這已經是第 N 次複習 kmp 了，這一次希望自己能夠寫一篇通俗易懂的筆記，供自己日後複習和大家借鑑。（不要問為啥用繁體，因為 mac 的 bug）

### 字符串匹配問題

當前有這樣這一個問題：

有一個主字符串 S，和一個模式串 P，請判斷 S 是否包含 P，如果包含請返回 S 中 P 的開始索引，不包含請返回 -1。

面對這一問題，我們不難想出一個簡單可行的暴力解法。

### 暴力匹配（Brute-Force）

這個名字真的非常直觀，兩個詞都有暴力、強迫的含義，類似的還有暴力遞歸（Force-Recursion）。

对于主字符串 S : AAAABAAC，模式串 P：AAC。

暴力匹配的做法是，从 P 中依次选取字符与 S 中的字符匹配：

- 如果字符相等，P 和 S 继续比对下一个位置的字符；
- 如果字符不相等，即出现失配，此时S 回退到 (S 失配位置索引) - (P 失配位置索引) + 1， P 回退到第一个字符；

直到 P 的字符与 S 中的字符完全配对，或 S 剩余字符长度小于 P。

其暴力匹配的过程如下所示：

![kmp-1](https://github.com/merlotliu/merlotliu-pics/blob/master/algorithm/kmp-1.png?raw=true)

其对应的代码实现也非常简单，以下是 C++ 的实现：

```c++
#include <string>
using namespace std;

int bf(string s, string p) {
	int i = 0, j = 0;
  while(i < s.size() && j < p.size()) {
    if(s[i] == p[j]) {
      i++;
      j++;
    } else { /// 失配需要重新开始匹配
      i = i - j + 1;
      j = 0;
    }
  }
  return (j == p.size()) ? i - p.size() : -1;
}
```

不难看出，在主字符串长度为 n，模式串长度为 m 的情况下，如果每次失配的情况出现在最后一个字符，则会达到最坏的情况，时间复杂为 O(n*m)。

那么，我们该如何对其进行改进呢？

### 前后缀最大相同长度

我们先引入一个概念前后缀最大相同长度，什么是前后缀最大相同长度呢？

**注意，前后缀不能包含整个字符串。**

![kmp-3](https://github.com/merlotliu/merlotliu-pics/blob/master/algorithm/kmp-3.png?raw=true)

然后，我们可以简单观察一下前面匹配的过程，然后就可以发现我们回退的“太远了”，怎么理解这个回退的太远了？

比如在如下的匹配过程中，S 从 i 开始 一路匹配到 x，x 的位置出现失配。

此时，如果是 BF 的算法，S 会回退到 i  + 1 的位置，y 回退到 0，然后继续比对。

而如果我们记录了 P 中每个位置前面字符串的前后缀长度，我们就可以跳过一些不可能配出 P 的位置。

如下，S[i : x-1] 与 P[0 : y-1] 是相等的，对于 y 前面的字符串的前后缀最大相同长度为 3，意味着 P 中**绿色部分**和**紫色部分**相等，且 S 中**橙色部分**和**红色部分**相

等。又因为**红色部分**和**紫色部分**相等，所以**绿色部分**等于**红色部分**，故在 P 中指针在回退时，可以跳转到 y' 的位置与 S 中的 x 比对，但遗憾的发现 y' 的位置仍然

不相等，所以继续向前跳转，最终到达 P 的开始，此时 S 的比对位置只好从 x + 1 开始。

![kmp-2](https://github.com/merlotliu/merlotliu-pics/blob/master/algorithm/kmp-2.png?raw=true)

而这就是 kmp 的匹配过程

那么为什么后缀与前缀最大相同部分的前面的内容没有办法完全与 P 匹配呢（S 中红色部分之前的内容）？

其实很好理解，假设 i 位置后面的 k 位置，k 位置在红色部分之前，从 k 位置能够得出 P ，那么 S 中 k 到 x - 1 的部分与 P 中 0 到 x - 1 - k 的部分必须相等。

而 S 中 k 到 x - 1 的部分又是与 P 中 0 + k - i 到 y - 1 的部分相等的，这样就会推出 k 到 x - 1 的部分才是前后缀相同长度的后缀，而这与已知矛盾。

![kmp-4](https://github.com/merlotliu/merlotliu-pics/blob/master/algorithm/kmp-4.png?raw=true)

### KMP

kmp 算法是匹配一个字符串 S 包含的字串 P 的开始索引的算法（成功返回开始索引，失败返回 -1），时间复杂度为 O(N)，N为 S 的长度。

下面给出 kmp 的完整流程：

- 当字符串长度 为 0，或者 子串 P 长度大于 S 的长度时，返回 -1；
- 计算 next 数组；
- 开始匹配：
  - 当字符相等时，同时增加 S 和 P 的当前字符索引；
  - 不相等时：
    - 如果当前 P 的索引还没有到最左端（0位置），通过 next 数组跳转到最长匹配前后缀的位置，继续下一轮匹配；
    - 如果已经到达最左端，则从当前 S 的索引开始已经没有办法再完成匹配，S 的索引向后移动一格；
- 最后，如果匹配成功，P 索引等于 P 的长度，返回 S 的索引 - P 的长度，反之返回-1；

实际上，如果 S 来到 idx 位置，P 来到 pos 位置不相等时，pos 跳转到 next[pos] 位置，相当于 P[0] 与 S[idx - next[pos]] 对齐，然后继续匹配，当已经到最左端时，没办法找到对其的字符，只能移动 idx 了。

#### 主逻辑代码

```c++
#include <string>
#include <vector>
using namespace std;

int kmp(string s, string p) {
    if(s.size() < p.size()) return -1;
    vector<int> next(p.size(), 0);
    getNextArr(p, next);
    int idx = 0; /// 主字符串 s 的索引指针
    int pos = 0; /// 模式串 p 的索引指针
    while(idx < s.size() && pos < p.size()) {
        if(s[idx] == p[pos]) {
            idx++;
            pos++;
        } else if (pos > 0) { /// 失配且仍有相同的前后缀
            pos = next[pos];
        } else { /// 失配且不存在相同的前后缀
            idx++;
        }
    }
    return (pos == p.size() ? idx - p.size() : -1);
}
```

#### 那么如何计算 kmp 的时间复杂度？

在循环中，我们可以发现 idx 始终增加，pos 有回退的情况。

但我们可以发现 pos 回退的次数受到 pos 自增和 next 数组的影响，而 pos 的自增次数是 pos 回退的上限。

怎么理解？假设 S 的长度为 n，P 的长度为 m。

我们看第一个分支和循环条件，可以知道，idx 最多自增 n 次，也就是第一个循环最多进入 n 次，那么不管 pos 回退多少次，只要跳出循环 pos 最多自增 n 次。

既然 pos 最多自增 n 次，那么 pos 最多回退 n 次，什么时候能达到 n 次？

类似 AAAAAAAABC 中匹配 AAAC 时，每次回退都是从 P 的最后一个字符递减到第一个字符。 

**所以，kmp 的主逻辑的时间复杂度为 O(n)。**

### next 数组

next 数组是 kmp 的最核心部分：

- next[i] 位置的值表示模式串 P[i] 之前的字符串的前后缀相同的最长长度，也就是说 next[i] 的值与 P[i] 无关。
- next[0] 则表示空集的前后缀相同长度，这一值可以设为 0 或不表示正确前后缀的值，通常设为 -1 或 0。
- next[1] 则表示 P[0] 的前后缀相同长度，单个字符的前后缀长度被定义为 0。

![kmp-5](https://github.com/merlotliu/merlotliu-pics/blob/master/algorithm/kmp-5.png?raw=true)

#### 暴力求解

最简单的求解 next 数组的方法，肯定是暴力求解

对于位置 i 的 next 值，从长度为 i - 1 的前后缀开始判断前后缀是否相等，直到前后缀相等或前后缀长度为 0。

```c++
#include <string>
#include <vector>
using namespace std;

void getNextArr(string p, vector<int> & next) {
    for(int i = 2; i < next.size(); i++) {
        for(int j = i - 1; j > 0; j--) { /// 从 i 位置之前字符串长度 - 1 的前后缀开始判断
            int l = 0, r = i - j;
            while(l < j && p[l] == p[r]) {
                l++;
                r++;
            }
            if(l == j) { /// 如果本长度的前后缀相同则为最大长度
                next[i] = j;
                break;
            }
        }
    }
}
```

假设 P 的长度为 m，对于每个位置不可避免都需要求解前后缀相同最大长度，则外层循环一定是 O(m)。

而对于每个位置最坏的情况是每个长度的前后缀都需要进行字符串比较，则这一层最坏时间复杂度为 O(m)。

最内层循环是比对前后缀字符是否相等，最坏情况是 O(m)，故暴力求解的时间复杂度最坏为 O(m^3)。

#### 优化求解

优化的方向，其实与 kmp 的思想类似，咱们可以利用已经存在的信息。

现在已知 pos 位置前面所有的 next 值，如何求 pos 位置的 next 值？

- 使用 pos - 1 位置的字符与前面最长前后缀的前缀的后一个字符依次比较。
- 如果遇到相等的字符，则当前 pos 的 next 值，为当前最长前后缀的值 + 1。
- 如果不相等，最长前后缀调到最长前后缀的最长前后缀，继续与 pos - 1 位置字符比较，直到相等或为 0。
- 如果最后为 0，则当前位置的 next 值也是 0。

![kmp-next-pro](https://github.com/merlotliu/merlotliu-pics/blob/master/algorithm/kmp-next-pro.png?raw=true)

完整流程：

- base case ：next[0] = -1或0，next[1] = 0；
  其实很好理解，当字符串只有一个字符时，前面为空串则不存在前后缀了；
  当字符串为两个字符时，前面仅有一个字符，最长前后缀匹配长度即为0（因为不能包含整个串）；
- pos 位置的 next 值 ：
  - 当 pos - 1 位置的字符 和 next[len] 位置的字符相等时，next[pos] = next[len] + 1，其中 len 的初值为 next[pos - 1];
  - 不相等时，则比对 next[next[pos]] 位置和 idx - 1的字符（实际上是跳转到 pos - 1对应的最长前后缀匹配长度的位置，继续比对）；
  - 如果当前已经是 0 位置，还不能匹配，则说明没有匹配的长度，next[pos] = 0；


#### next 代码

```c++
#include <string>
#include <vector>
using namespace std;

void getNextArr(string p, vector<int> & next) {
  	int pos = 2;
  	/// len 的含义：
  	/// 1. 当前比对最大前后缀的长度；
  	/// 2. 当前与 P[pos - 1] 位置比较的字符的下标；
  	int len = 0;
  	while(pos < next.size()) {
    		if(p[pos] == p[len]) {
          	next[pos] = len + 1;
          	pos++;
          	len++;
        } else if (len > 0) {
          	len = next[len];
        } else {
          	next[pos++] = 0;
        }
    }
}
```

#### 计算 next 数组的时间复杂度？

分析过程跟 kmp 其实是一样的，不再赘述，其时间复杂度为 O(m)，m 为模式串字符串 P 的长度。

### 完整代码

kmp 整体的时间复杂度为 O(n + m) ，因为 m < n 所以，时间复杂度为 O(n)。

```c++
#include <string>
#include <vector>
using namespace std;

void getNextArr(string p, vector<int> & next) {
  	int pos = 2;
  	/// len 的含义：
  	/// 1. 当前比对最大前后缀的长度；
  	/// 2. 当前与 P[pos - 1] 位置比较的字符的下标；
  	int len = 0;
  	while(pos < next.size()) {
    		if(p[pos - 1] == p[len]) {
          	next[pos] = len + 1;
          	pos++;
          	len++;
        } else if (len > 0) {
          	len = next[len];
        } else {
          	next[pos++] = 0;
        }
    }
}

int kmp(string s, string p) {
    if(s.size() < p.size()) return -1;
    vector<int> next(p.size(), 0);
    getNextArr(p, next);
    int idx = 0; /// 主字符串 s 的索引指针
    int pos = 0; /// 模式串 p 的索引指针
    while(idx < s.size() && pos < p.size()) {
        if(s[idx] == p[pos]) {
            idx++;
            pos++;
        } else if (pos > 0) { /// 失配且仍有相同的前后缀
            pos = next[pos];
        } else { /// 失配且不存在相同的前后缀
            idx++;
        }
    }
    return (pos == p.size() ? idx - p.size() : -1);
}
```

## Leetcode

[28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

[459. 重复的子字符串 - 力扣（Leetcode）](https://leetcode.cn/problems/repeated-substring-pattern/)

## Reference 

1. [如何更好地理解和掌握 KMP 算法? - 阮行止的回答 - 知乎 ](https://www.zhihu.com/question/21923021/answer/1032665486)
2. [从头到尾彻底理解KMP（2014年8月22日版）](https://blog.csdn.net/v_july_v/article/details/7041827/)