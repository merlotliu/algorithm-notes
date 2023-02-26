---
layout: post
title: 滑动窗口
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [滑动窗口, 单调队列]
categories: [算法&数据结构]
comments: true
---

## 滑动窗口

窗口的最大值和最小值更新结构：

- 双端队列：数据的承载容器（因为需要从头尾都能够出入元素）；
- 窗口具有左边界 L 和右边界 R，且左闭右开，右边界优先级大于左边界；
- 在窗口变化的过程中，L 和 R 都只能往右移动，不能往左移动；
- L 的值始终 小于等于 R；
- 在 R 右边界的移动过程中，保证双端队列的元素单调性，如果当前数进入后不能保证单调，则依次弹出队尾元素，直到队空或当前元素插入后仍保证单调；
- 在 L 移动过程中，如果越过队首元素，则弹出队首元素；
- 最重要的是，一定要明确 双端队列 **所维持的主要信息**；

例1：[239. 滑动窗口最大值 - 力扣（Leetcode）](https://leetcode.cn/problems/sliding-window-maximum/)

```c++
#pragma once
#include <vector>
#include <stack>
#include <deque>

using namespace std;

class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
				/* 输入异常处理 */
        if(nums.size() < 1 || k < 1 || nums.size() < k) {
            return {};
        }
        /* 双端队列记录下标 */
    		deque<int> win;
        vector<int> res;
        for(int L = 0, R = 0; R < nums.size(); ) {
						/* 窗口右移动，不能保持队列单调则出队列，直至可以满足单调 */
            while(!win.empty() && nums[win.back()] < nums[R]) {
                win.pop_back();
            }
            win.push_back(R++);
            /* 窗口已经形成，获取最大值 */
            if(R - L == k) {
                res.push_back(nums[win.front()]);
              	/* 有元素已经在窗口外 L = R - k */
            		if(win.front() == L++) {
                		win.pop_front();
                }
            }
        }
				return res;
		}
};
```

Notes:

- 窗口的左右边界 L 和 R，不一定需要显示的指出，两者可定义一个，另外一个通过 窗口大小 k 的关系，计算得出，具体是 R - k + 1 还是 R - k ，看编码逻辑方式。

## Leetcode

### 困难

[239. 滑动窗口最大值 - 力扣（Leetcode）](https://leetcode.cn/problems/sliding-window-maximum/)

## Reference 