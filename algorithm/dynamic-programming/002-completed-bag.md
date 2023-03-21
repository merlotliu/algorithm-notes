---
layout: post
title: 01背包
date: 2022-12-13 00:14:33
updated: 2023-03-02 00:14:33
tags: [01背包]
categories: [算法&数据结构]
comments: true
---

## 完全背包

将 01 背包问题描述中每个物品最多只能取 1 次，变为每个物品可以取无数次。

### 一维 dp 数组完全背包

由于，每个物品可以重复选取，故在遍历背包容量更新 dp 时，应**顺序遍历**：

```c++
int bagCompleted(vector<int>& weight, vector<int>& value, int maxWeight) {
    /* weight 为重量数组 */
    /* 值得注意的是dp数组的长度为最大重量+1 */
    int dp[weight.size()][maxWeight + 1]{0};
    for(int i = 0; i < weight.size(); i++) { // 遍历物品    
        for(int j = weight[i]; j <= maxWeight; j++) { // 遍历背包容量         
        	dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);    
        }
    }
}
```

值得注意的是：

- 完全背包中，物品和容量的遍历顺序可以交换；

## 经典例题

[518. 零钱兑换 II - 力扣（Leetcode）](https://leetcode.cn/problems/coin-change-ii/)

给你一个整数数组 coins 表示不同面额的硬币，另给一个整数 amount 表示总金额。

请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 0 。

假设每一种面额的硬币有无限个。 

题目数据保证结果符合 32 位带符号整数。

 
示例 1：
```
输入：amount = 5, coins = [1, 2, 5]
输出：4
解释：有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

提示：

* 1 <= coins.length <= 300
* 1 <= coins[i] <= 5000
* coins 中的所有值 互不相同
* 0 <= amount <= 5000

## 代码

```c++
class Solution {
public:
    /* 注意：组合的情况下一定要主要便利的顺序，不然就需要去重！ */
    int change(int amount, vector<int>& coins) {
        /* dp[j] 表示容量为 j 的背包的组合种数 */
        vector<int> dp(amount + 1, 0);
        /* base case dp[0] = 1 表示凑齐 0 只有 1 中可能（什么硬币都不选） */
        dp[0] = 1;
        /* 先遍历硬币规格，再遍历背包容量，能保证如{1， 2， 5}的规格下，只出现{1, 2}，而不会出现{2,1} */
        /* 每一层使用当前硬币规格凑amount的过程就是，用新的规格替换旧规格增加组合的方式 */
        for(int i = 0; i < coins.size(); i++) {
            /* 小于 i 的背包容量肯定不会增加新的组合 */
            for(int j = coins[i]; j <= amount; j++) {
                /* 表示使用 coint[i] 为 dp[j] 增加了 dp[j - coins[i]] * 1 种组合 */
                /* 这个 1 表示的就是当前规格的硬币 */
                dp[j] += dp[j - coins[i]];
            }
        }
        return dp[amount];
    }
};
```