---
title: Monotonic Stack
date: {{ date }}
updated: {{ date }}
tags: 
categories: 
comments: true

---

# 单调栈

对于一个存放整形数据的栈，单调栈从栈底到栈顶只能是从小到大，或者从大到小。

例1：求一个**无重复**数据的数组中，每个位置左边和右边离他最近的比他大的值。

解：

- 维护一个从栈底到栈顶递减的单调栈（即数字大的在下面，数字小的在上面）；
- 如果当前数 num，大于栈顶元素，栈顶元素弹栈，生成**栈顶元素**信息：右边最近（num），左边最近（当前栈顶元素，如果弹栈后栈为空，则左边没有比他大的），然后将当前数入栈；
- 当前数小于栈顶元素则入栈；
- 最后如果遍历到数组末尾，但栈不为空，依次弹出栈内元素，栈内元素：右边（无），左边（原来在栈中的下一个元素）；

此外，对于有重复元素的情况下，可以在栈内存放可变的结构，如链表，然后把相同的数的下标用链表连接。

例2：正数数组中**累加和与最小值的乘积**最大的子数组，返回该值。

解：

- 通过单调栈得出当前值的作为最小值的左右边界（即左右最近较大值）；
- 以自身值 * 左右边界中间长度，比较得出最大值即可；

## 单调栈代码框架

值得注意的是：

- 单调栈有单调递增和单调递减栈（从栈底），求最大值递减（大的在下）；
- 单调栈存放的数据：索引、值还是可变结构。在有重复数据时，需使用可变结构，如链表，将数值相同的索引放在一起；
- 弹出栈时，生成栈顶元素的信息，实际上入栈之前当前栈顶元素的值即为当前要入栈的元素的值**左值**（左边界）；
- 压栈时，需保证单调性，若单调性不满足，则弹栈直到满足当前数压栈后仍满足单调性；

```c++
/* 例1 : 无重复值 */
vector<int> getLeftRightMax(vector<int> &nums) {
    /* 维护递减栈，存放索引（能包含更多信息） */
    stack<int> mono_stk;
    vector<pair<int, int>> res(nums.size(), -1);
    for(int i = 0; i < nums.size(); i++) {
        /* 1. 栈空,直接压栈 */
        if(mono_stk.empty()) {
            res[i].first = -1; /* 当前值没有左边界 */
			mono_stk.push(i);
        } else { /* 当前栈不为空 */
			/* 2. 当前元素小于栈顶元素，压栈 */
            if(nums[i] < nums[mono_stk.top()]) {
                /* 当前值对应的左边界即为栈顶元素 */
                res[i].first = mono_stk.top(); 
            	mono_stk.push(i);
            } else {
            	/* 3. 当前元素大于栈顶元素，弹栈直至当前数压栈后仍保持单调 */
                while(!mono_stk.empty() && nums[i] > nums[mono_stk.top()]) {
                    /* 栈顶元素的右边界即为当前数 */
                    res[mono_stk.top()].second = i;
                    mono_stk.pop();
                }
                /* 当前数的左边界即为栈顶元素，
                	随后当前数入栈（此时说明压栈后满足单调） */
                res[i].first = !mono_stk.empty() ? mono_stk.top() : -1;
                mono_stk.push(i);
            }
        }
    }
    return res;
}
```

合并相关逻辑分支后

```c++
/* 例1 : 无重复值 */
vector<int> getLeftRightMax(vector<int> &nums) {
    /* 维护递减栈，存放索引（能包含更多信息） */
    stack<int> mono_stk;
    vector<pair<int, int>> res(nums.size(), -1);
    for(int i = 0; i < nums.size(); i++) {
        /* 栈不为空，且当前元素大于栈顶元素，弹栈直至当前数压栈后仍保持单调 */
        while(!mono_stk.empty() && nums[i] > nums[mono_stk.top()]) {
            /* 栈顶元素的右边界即为当前数 */
            res[mono_stk.top()].second = i;
            mono_stk.pop();
        }
        /* 当前数的左边界即为栈顶元素或栈空时无左边界，
        	随后当前数入栈（此时说明压栈后满足单调） */
        res[i].first = !mono_stk.empty() ? mono_stk.top() : -1;
        mono_stk.push(i);
    }
    return res;
}
```

## Leetcode 练习

### 中等

[739. 每日温度 - 力扣（Leetcode）](https://leetcode.cn/problems/daily-temperatures/)

[496. 下一个更大元素 I - 力扣（Leetcode）](https://leetcode.cn/problems/next-greater-element-i/description/)

[503. 下一个更大元素 II - 力扣（Leetcode）](https://leetcode.cn/problems/next-greater-element-ii/description/)

### 困难

这两道题的暴力解法还是挺好想的，O(N)解法有些麻烦。

[42. 接雨水 - 力扣（Leetcode）](https://leetcode.cn/problems/trapping-rain-water/description/)

[84. 柱状图中最大的矩形 - 力扣（Leetcode）](https://leetcode.cn/problems/largest-rectangle-in-histogram/description/)
