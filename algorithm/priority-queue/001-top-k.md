---
layout: post
title: TopK
date: 2022-12-13 22:49:55
updated: 2023-02-27 22:49:55
tags: [优先级队列, 大根堆, 小根堆]
categories: [算法&数据结构]
comments: true
---

## TopK

[347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/description/)

1. 统计所有数出现的频率；
2. 构建一个长度为 k 的小根堆，由于存放的是 pair 类型，所以还需要重写比较器；
3. 将统计的频数加入到小根堆，小根堆大小超过 k，则弹出堆顶元素；
4. 将小根堆的元素放入结果集并返回;

## 代码

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

