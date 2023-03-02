---
layout: post
title: 回溯算法
date: 2023-12-13 00:14:33
updated: 2023-03-02 00:14:33
tags: [回溯, 暴力穷举]
categories: [算法&数据结构]
comments: true
---

# 回溯算法

回溯算法，即为暴力穷举。

暴力递归就是尝试

1. 把问题转化为规模缩小了的同类问题的子问题;
2. 有明确的不需要继续进行递归的条件(base case);
3. 有当得到了子问题的结果之后的决策过程;
4. 不记录每一个子问题的解；

对于一个问题里面的某个小决策，会有做与不做两个选择。

回溯法三要素：路径、选择列表、结束条件。

## 解决问题

- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等等

## 经典题型

### 1 汉诺塔问题

> 在经典汉诺塔问题中，有 3 根柱子及 N 个不同大小的穿孔圆盘，盘子可以滑入任意一根柱子。一开始，所有盘子自上而下按升序依次套在第一根柱子上(即每一个盘子只能放在更大的盘子上面)。移动圆盘时受到以下限制:
> (1) 每次只能移动一个盘子;
> (2) 盘子只能从柱子顶端滑出移到下一根柱子;
> (3) 盘子只能叠在比它大的盘子上。

打印n层汉诺塔从最左边移动到最右边的全部过程。

- 仅有一个圆盘时，可以直接从左移动到右；
- 2个时，先把上面的移到中间，再把下面的移动到右边，最后把上面再移动到右边；
- 3个，先把上面两个移动到中间，再把下面的移动到右边，最后把上面的移动到右边；
- n个时，先把n-1个移动到中间，再把下面的移动到右边，最后把上面n-1个移动到右边；

- ... ... 
- 事实上，把下面的从左移到右就是只剩一个的时候；
- 而把n-1从左移动到中间，还是从中间移动到右边，最终又会被拆解为2个圆盘或1个的情况；
- 因为最底层圆盘仅需要移动一次。

```cpp
void recursion(int i, std::string from, std::string to, std::string other) {
	if (i == 1) {
		std::cout << i << " " << from << " -> " << to << std::endl;
		return;
	}
	recursion(i - 1, from, other, to); // move 1-(i-1) from -> other
	std::cout << i << " " << from << " -> " << to << std::endl; // move i from -> to
	recursion(i - 1, other, to, from); // move 1-(i-1) other - > from
}

void hanoi() {
	int n = 4;
	recursion(n, "left", "right", "mid");
}
```



### 2 字符串的全部子序列

打印一个字符串的全部子序列，包括空字符串。

```cpp
// str 全部字符集合
// i 当前在哪一层
void backtrack(std::string &str, int i, std::string memo) {
	if (i >= str.size()) {
		std::cout << memo << " ";
		return;
	}
	// 不要
	backtrack(str, i + 1, memo);
	
	// 要
	memo.push_back(str[i]);
	backtrack(str, i + 1, memo);
}

void printAllSubsequence(std::string &str) {
	backtrack(str, 0, "");
	std::cout << std::endl;
}
```



### 3 字符串的全排列

打印一个字符串的全部排列，要求不要出现重复的排列。

打印一个字符串的全部排列（**非去重**）

```cpp
/* alt : 选择列表
 * selected : 已选择列表
 * i : 第几层
 * base case : i >= alt.size() 打印 selected
 */
void backtrack(std::string &alt, std::string &picked, int i) {
	// base case & end condition
	if (i >= alt.size()) {
		std::cout << picked << std::endl;
		return;
	}
	// for( one : alternative)
	for (auto ch : alt) {
		picked.push_back(ch); // picked
		backtrack(alt, picked, i + 1);
		picked.pop_back(); // unpicked
	}
}

void printAllPermutation(std::string &str) {
	std::string picked;
	backtrack(str, picked, 0);
}
```

去重剪枝的全排列

```cpp
/* alt : 选择列表
 * selected : 已选择列表
 * i : 第几层
 * base case : i >= alt.size() 打印 selected
 */
void backtrack(std::string &alt, std::string &picked, int i, std::unordered_set<char> &memo) {
	// base case & end condition
	if (i >= alt.size()) {
		std::cout << picked << std::endl;
		return;
	}
	// for( one : alternative)
	for (auto ch : alt) {
		if(memo.find(ch) == memo.end()) {
			picked.push_back(ch); // picked
			memo.insert(ch);
			backtrack(alt, picked, i + 1, memo);
			memo.erase(ch);
			picked.pop_back(); // unpicked
		}
	}
}

void printAllPermutation(std::string &str) {
	std::string picked;
	std::unordered_set<char> memo;
	backtrack(str, picked, 0, memo);
}
```



### 4 逆序给定栈

给你一个栈，请你逆序这个栈，不能申请额外的数据结构，只能使用递归函数。如何实现?

```cpp
#pragma once
#include <stack>
#include <iostream>

/* 
 * base case : stack is empty
 * 先出栈元素，如果为null，返回一直该栈底元素，不为空继续递归出栈
 */
int getStackBottom(std::stack<int> &stk) {
	int cur = stk.top();
	stk.pop();
	if (stk.empty()) {
		return cur;
	}
	int bottom = getStackBottom(stk);
	stk.push(cur);
	return bottom; 
}

/*
 * base case : stack is empty
 */
void backtrack(std::stack<int> &stk) {
	if (stk.empty()) {
		return;
	}
	int num = getStackBottom(stk);
	backtrack(stk);
	stk.push(num);
}

void reverseStack(std::stack<int> &stk) {
	backtrack(stk);
}

void printStack(std::stack<int> stk) {
	while (!stk.empty()) {
		std::cout << stk.top() << " ";
		stk.pop();
	}
	std::cout << std::endl;
}
```



### 5 数字与26字母的转化

规定1和A对应、2和B对应、3和C对应...那么一个数字字符串比如"111"，就可以转化为"AAA"、"KA"和"AK"。给定一个只有数字字符组成的字符串str，返回有多少种转化结果。

对于长度为n的字符串而言，在i位置的情况：

- i为0：不能转化；
- i为1：两种方式，i或（i，i+1）；
- i为2：
  - i+1在[0, 6]：i或（i，i+1）；
  - i；
- i在[3, 9]：i；

到达末尾结算。

```cpp
int res = 0;
void backtrack(std::string &num, int i) {
	if ('0' == num[i]) {
		return;
	}
	if (i == num.size()) {
		res++;
	}
	if ('1' == num[i]) {
		backtrack(num, i + 1);
		backtrack(num, i + 2);
	}
	if ('2' == num[i]) {
		backtrack(num, i + 1);
		if (i + 1 != num.size() && num[i + 1] >= '0' && num[i + 1] <= '6') {
			backtrack(num, i + 2);
		}
	}
	if (num[i] >= '3' && num[i] <= '9') {
		backtrack(num, i + 1);
	}
}

int numConvertToLetter(std::string &num) {
	backtrack(num, 0);
	return res;
}
```

不使用全局变量，让递归函数最后返回结果：

- 能产生多种情况的位置只有1和2，每次分叉的和为此次的返回值；
- 遇到0则返回0；
- 数组结束也返回0；

```

```



### 6 01背包问题

给定两个长度都为N的数组weights和values，weights[i]和values[i]分别代表i号物品的重量和价值。给定一个正数bag，表示一个载重bag的袋子，你装的物品不能超过这个重量。返回你能装下最多的价值是多少？

```cpp
#pragma once
#include <vector>
#include <algorithm>

int backtrack(std::vector<int> &weights, std::vector<int> &values, int bag, int i, int picked_weights, int picked_values) {
	if (picked_weights > bag) {
		return 0;
	}
	if(i == weights.size()) {
		return picked_values;
	}
	int unpicked = backtrack(weights, values, bag, i + 1, picked_weights, picked_values);
	int picked = backtrack(weights, values, bag, i + 1, picked_weights + weights[i], picked_values + values[i]);
	return std::max(unpicked, picked);
}

//std::vector<int> weights = { 2, 4, 5, 8, 10 };
//std::vector<int> values = { 4, 2, 1, 4, 3 };
int getMaxVal(std::vector<int> &weights, std::vector<int> &values, int bag) {
	return backtrack(weights, values, bag, 0, 0, 0);
}
```

完整的递归树如下：

```shell
.
├── n_0_0_0
│?? ├── n_1_0_0
│?? │?? ├── n_2_0_0
│?? │?? │?? ├── n_3_0_0
│?? │?? │?? │?? ├── n_4_0_0
│?? │?? │?? │?? └── p_4_10_3
│?? │?? │?? └── p_3_8_4
│?? │?? │??     ├── n_4_0_0
│?? │?? │??     └── p_4_8_3
│?? │?? └── p_2_5_1
│?? │??     ├── n_3_0_0
│?? │??     │?? ├── n_4_0_0
│?? │??     │?? └── p_4_10_3
│?? │??     └── p_3_8_4
│?? │??         ├── n_4_0_0
│?? │??         └── p_4_8_3
│?? └── p_1_4_2
│??     ├── n_2_0_0
│??     │?? ├── n_3_0_0
│??     │?? │?? ├── n_4_0_0
│??     │?? │?? └── p_4_10_3
│??     │?? └── p_3_8_4
│??     │??     ├── n_4_0_0
│??     │??     └── p_4_8_3
│??     └── p_2_5_1
│??         ├── n_3_0_0
│??         │?? ├── n_4_0_0
│??         │?? └── p_4_10_3
│??         └── p_3_8_4
│??             ├── n_4_0_0
│??             └── p_4_8_3
└── p_0_2_4
    ├── n_1_0_0
    │?? ├── n_2_0_0
    │?? │?? ├── n_3_0_0
    │?? │?? │?? ├── n_4_0_0
    │?? │?? │?? └── p_4_10_3
    │?? │?? └── p_3_8_4
    │?? │??     ├── n_4_0_0
    │?? │??     └── p_4_8_3
    │?? └── p_2_5_1
    │??     ├── n_3_0_0
    │??     │?? ├── n_4_0_0
    │??     │?? └── p_4_10_3
    │??     └── p_3_8_4
    │??         ├── n_4_0_0
    │??         └── p_4_8_3
    └── p_1_4_2
        ├── n_2_0_0
        │?? ├── n_3_0_0
        │?? │?? ├── n_4_0_0
        │?? │?? └── p_4_10_3
        │?? └── p_3_8_4
        │??     ├── n_4_0_0
        │??     └── p_4_8_3
        └── p_2_5_1
            ├── n_3_0_0
            │?? ├── n_4_0_0
            │?? └── p_4_10_3
            └── p_3_8_4
                ├── n_4_0_0
                └── p_4_8_3
```



### 7 获胜者

给定一个整型数组arr，代表数值不同的纸牌排成一条线。玩家A和玩家B依次拿走每张纸牌，规定玩家A先拿，玩家B后拿，但是每个玩家每次只能拿走最左或最右的纸牌，玩家A和玩家B都绝顶聪明。请返回最后获胜者的分数。

【举例】arr=[1,2,100,4]。

开始时，玩家A只能拿走1或4。如果开始时玩家A拿走1，则排列变为[2,100,4]，接下来玩家B可以拿走2或4，然后继续轮到玩家A...如果开始时玩家A拿走4，则排列变为[1,2,100]，接下来玩家B可以拿走1或100，然后继续轮到玩家A...

玩家A作为绝顶聪明的人不会先拿4，因为拿4之后，玩家B将拿走100。所以玩家A会先拿1，让排列变为[2,100,4]，接下来玩家B不管怎么选，100都会被玩家A拿走。玩家A会获胜，分数为101。所以返回101。

arr=[1,100,2]。

开始时，玩家A不管拿1还是2，玩家B作为绝顶聪明的人，都会把100拿走。玩家B会获胜，分数为100。所以返回100。



对于其中**一个玩家**来说：

- 先手：一定会选择最有利的决策使得收益最大化，而使得对手的收益最低。

- 后手：从另外一个玩家选择之后，收益降低的选择下，保证自己的收益最大化。

- base case：剩下最后一张牌：先手获取全部收益，后手为0；

```cpp
/* 这里的先后手都是相对于一个人而言 */
int firstPick(std::vector<int> &nums, int l, int r);
int secondPick(std::vector<int> &nums, int l, int r);

int firstPick(std::vector<int> &nums, int l, int r) {
	if (l == r) return nums[l];
	int left = secondPick(nums, l + 1, r) + nums[l];
	int right = secondPick(nums, l, r - 1) + nums[r];
	return std::max(left, right);
}

int secondPick(std::vector<int> &nums, int l, int r) {
	if (l == r) return 0;
	return std::min(firstPick(nums, l + 1, r), firstPick(nums, l, r - 1));
}

int winnerScore(std::vector<int> &nums) {
	return std::max(firstPick(nums, 0, nums.size() - 1), secondPick(nums, 0, nums.size() - 1));
}
```



### 8 n皇后

N皇后问题是指在N*N的棋盘上要摆N个皇后，要求任何两个皇后不同行、不同列，也不在同一条斜线上。

给定一个整数n，返回n皇后的摆法有多少种。n=1，返回1。

n=2或3，2皇后和3皇后问题无论怎么摆都不行，返回0。

n=8，返回92。

#### n皇后的摆法总数

```cpp
bool isValid(int row, int col, std::vector<std::vector<bool>> &board){
	for (int i = 0; i < row; i++) {
		for (int j = 0; j < board.size(); j++) {
			if (board[i][j] && (j == col || (abs(row - i) == abs(col - j)))) {
				return false;
			}
		}
	}
	return true;
}

int backtrack(int level, int n, std::vector<std::vector<bool>> &board) {
	if (level >= n) {
		return 1;
	}
	int res = 0;
	for (int i = 0; i < n; i++) {
		if (isValid(level, i, board)) {
			board[level][i] = 1;
			res += backtrack(level + 1, n, board);
			board[level][i] = 0;
		}
	}
	return res;
}

int totalNQueens(int n) {
	std::vector<std::vector<bool>> board(n, std::vector<bool>(n, 0));
	return backtrack(0, n, board);
}
```

##### 位运算优化常数时间

```cpp
int backtrack(int col_lim, int left_dia_lim, int right_dia_lim, int limit_range) {
	if (col_lim == limit_range) {
		return 1;
	}
	int res = 0;
	// calculate current all of possible position
	int pos = limit_range & (~(col_lim | left_dia_lim | right_dia_lim));
	while (pos != 0) {
		int most_right_one = pos & (~pos + 1); // get most right one
		pos -= most_right_one; // mark to limit
        // 下一个位置左斜线的限制就是当前左斜线限制或上当前选择的位置再左移一位
       	// 下一个位置右斜线的限制就是当前右斜线限制或上当前选择的位置再右移一位
		res += backtrack(col_lim | most_right_one,
			(left_dia_lim | most_right_one) << 1,
			(right_dia_lim | most_right_one) >> 1,
			limit_range);
	}
	return res;
}

int totalNQueensBit(int n) {
	return backtrack(0, 0, 0, (1 << n) - 1);
}
```

#### n皇后的所有摆法

```cpp
bool isValid(int row, int col, std::vector<std::string> &board) {
	for (int i = 0; i < row; i++) {
		for (int j = 0; j < board.size(); j++) {
			if (board[i][j] == 'Q' && (j == col || (abs(row - i) == abs(col - j)))) {
				return false;
			}
		}
	}
	return true;
}

void backtrack(int level, int n, std::vector<std::string> &board, std::vector<std::vector<std::string>> &res) {
	if (level >= n) {
		res.push_back(board);
		return;
	}
	for (int i = 0; i < n; i++) {
		if (isValid(level, i, board)) {
			board[level][i] = 'Q';
			backtrack(level + 1, n, board, res);
			board[level][i] = '.';
		}
	}
}

std::vector<std::vector<std::string>> solveNQueens(int n) {
	std::vector<std::string> board(n, std::string(n, '.'));
	std::vector<std::vector<std::string>> res;
	backtrack(0, n, board, res);
	return res;
}
```



## LeetCode & 代码随想录

### 回溯模板

```cpp
void backtrack(selected_list, start_idx, track) {
	if(end condition) {
		push(current_all_of_picked)
		return;
	}
	
	for(option : selected_list) {
		picked; // update track
		backtrack(selected_list, new_start_idx, track);
		unpicked; // update track
	}
}
```



### 1 组合

[77. 组合 - 力扣（LeetCode）](https://leetcode.cn/problems/combinations/)

base case : i > k 將當前選取的内容放入結果數組

```cpp
class Solution {
public:
    vector<vector<int>> res;
    void backtrack(int n, int k, int idx, vector<int> &picked) {
        if(picked.size() == k) {
            res.push_back(picked);
            return ;
        }
        // selcted list
        // idx : index of array [1 ... n]
        for(int i = idx; i <= n; i++) {
            picked.push_back(i); // picked
            backtrack(n, k, i + 1, picked);
            picked.pop_back(); // unpicked
        }
    }

    vector<vector<int>> combine(int n, int k) {
        vector<int> picked;
        backtrack(n, k, 1, picked);
        return res;
    }
};
```

#### 剪枝

设想以下情况，n=4，k=1时的递归树？

可以得知，在第一层for循环中，从1往后的分支都是多余的。为什么是多余的？因为元素个数已经够了。

我们需要的元素个数是k，当前已经有的元素为size，则k-size表示仍需要的个数，所以结束的位置应该为下一次能保证把选择填满的位置。所以，n-(k-size)+1是本轮的最后一个位置，這個+1可以通過舉例子確定。比如n=5，k=3的情況下，在第一輪size=0時，5-(3-0)+1=3，從3往後，還有4、5。

```cpp
class Solution {
public:
    vector<vector<int>> res;
    void backtrack(int n, int k, int idx, vector<int> &picked) {
        if(picked.size() == k) {
            res.push_back(picked);
            return ;
        }
        // selcted list
        // idx : index of array [1 ... n]
        for(int i = idx; i <= n - (k - picked.size()) + 1; i++) {
            picked.push_back(i); // picked
            backtrack(n, k, i + 1, picked);
            picked.pop_back(); // unpicked
        }
    }

    vector<vector<int>> combine(int n, int k) {
        vector<int> picked;
        backtrack(n, k, 1, picked);
        return res;
    }
};
```

### 2 组合总和

[216. 组合总和 III - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum-iii/)

```cpp
class Solution {
public:
    vector<vector<int>> res;
    void backtrack(int k, int n, int idx, vector<int> &picked) {
        // base case
        if(k == picked.size()) {
            if(n == 0) {
                res.push_back(picked);
            }
            return ;
        }
        // selected list
        for(int i = idx; i <= 9; i++) {
            picked.push_back(i);
            backtrack(k, n - i, i + 1, picked);
            picked.pop_back();
        }
    }

    vector<vector<int>> combinationSum3(int k, int n) {
        vector<int> picked;
        backtrack(k, n, 1, picked);
        return res;
    }
};
```

#### 剪枝

选择的总和已经大于n的话，则没必要继续遍历了。

```cpp
		// cut off
        if(n < 0) {
            return;
        }
```

### 3 電話號碼的字母組合

[17. 电话号码的字母组合 - 力扣（LeetCode）](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

- base case : 已選擇的字符串和數字字符串長度一致；
- 每一層表示一個數字，通過ref找到對應的字母列表，這個字母列表就是每一層的選擇列表；
- 每一層每一次從選擇列表選擇一個；

```cpp
class Solution {
public:
    vector<string> ref = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    vector<string> res;
    void backtrack(string digits, int digits_idx, string picked) {
        // base case : picked.size() == digits.size()
        if(digits.size() == picked.size()) {
            res.push_back(picked);
            return;
        }
        
        int num_idx = digits[digits_idx] - '2';
        for(auto ch : ref[num_idx]) {
            picked.push_back(ch); // picked
            backtrack(digits, digits_idx + 1, picked);
            picked.pop_back(); // unpicked
        }
    }

    vector<string> letterCombinations(string digits) {
        if(digits.empty()) return {};
        backtrack(digits, 0, "");
        return res;
    }
};
```

**注意**：输入1 * #按键等等异常情况。

代码中最好考虑这些异常情况，但题目的测试数据中应该没有异常情况的数据，所以我就没有加了。

**但是要知道会有这些异常，如果是现场面试中，一定要考虑到！**

### 4 组合总和

[39. 组合总和 - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum/)

- 每一次选择的位置应该在上一次的位置或之后，而不能往前；

```cpp
class Solution {
public:
    vector<vector<int>> res;
    void backtrack(vector<int> &candidates, int target, int idx, vector<int> &picked) {
        if(target < 0) {
            return;
        }
        // base case
        if(target == 0) {
            res.push_back(picked);
            return;
        }
        for(int i = idx; i < candidates.size(); i++) {
            picked.push_back(candidates[i]);
            backtrack(candidates, target - candidates[i], i, picked);
            picked.pop_back();
        }
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<int> picked;
        backtrack(candidates, target, 0, picked);
        return res;
    }
};
```

### 5 组合总和 II

[40. 组合总和 II - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum-ii/)

- 先排序，方便哈希表去重；

```cpp
class Solution {
public:
    vector<vector<int>> res;
    void backtrack(vector<int> &candidates, int target, int idx, vector<int> &picked) {
        if(target < 0) {
            return;
        }
        // base case
        if(target == 0) {
            res.push_back(picked);
            return;
        }
        unordered_set<int> memo;
        for(int i = idx; i < candidates.size(); i++) {
            if(memo.find(candidates[i]) == memo.end()) {
                picked.push_back(candidates[i]);
                memo.insert(candidates[i]);
                backtrack(candidates, target - candidates[i], i + 1, picked);
                picked.pop_back();
            }
        }
    }

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        vector<int> picked;
        backtrack(candidates, target, 0, picked);
        return res;
    }
};
```

### 6 分割回文串

[131. 分割回文串 - 力扣（LeetCode）](https://leetcode.cn/problems/palindrome-partitioning/)

- 判断回文
- 回溯三要素：路径、选择列表、终止条件；
  - 选择列表：s到size-1的连续字符串，下一次的开始位置就是本次的末尾；

```cpp
class Solution {
    vector<vector<string>> res;
public:
    bool isPalindrome(string &str, int l, int r) {
        while(l <= r) {
            if(str[l++] != str[r--]) {
                return false;
            }
        }
        return true;
    }
    void backtrack(string &str, int s, vector<string> &picked) {
        if(s == str.size()) {
            res.push_back(picked);
            return;
        }
        for(int i = s; i < str.size(); i++) {
            if(!isPalindrome(str, s, i)) {
                continue;
            }
            picked.push_back(str.substr(s, i - s + 1));
            backtrack(str, i + 1, picked);
            picked.pop_back();
        }
    }

    vector<vector<string>> partition(string s) {
        vector<string> picked;
        backtrack(s, 0, picked);
        return res;
    }
};
```

### 7 复原IP地址

[93. 复原 IP 地址 - 力扣（LeetCode）](https://leetcode.cn/problems/restore-ip-addresses/)

- 注意判断断点设置之后数字的合法性；
- 每一次选择，最多往后三位；
- 断点必须为4才合法，大于提前回溯，小于且遍历完字符串，提前回溯；

```cpp
class Solution {
public:
    vector<string> res;
    bool isValid(string &str, int start, int end) {
        if('0' == str[start]) {
            return end == start;
        }
        if('3' <= str[start] && '9' >= str[start]) {
            return (end - start < 2);
        }
        if('2' == str[start]) {
            if(end - start < 2) {
                return true;
            }
            if(str[start + 1] >= '6' || (str[start + 1] == '5' && str[end] >= '6')) {
                return false;
            }
        }
        // '1' must be true
        return true;
    }

    void backtrack(string &str, int start_idx, string track) {
        if(track.size() > str.size() + 4) {
            return ;
        }
        if(start_idx == str.size()) {
            if(track.size() == str.size() + 4) {
                track.pop_back(); // pop the tail '.'
                res.push_back(track);
            }
            return;
        }
        
        for(int i = start_idx; i < str.size() && i < start_idx + 3; i++) {
            if(!isValid(str, start_idx, i)) {
                continue;
            }
            string cur = str.substr(start_idx, i - start_idx + 1);
            backtrack(str, i + 1, track + cur + ".");
        }
    }

    vector<string> restoreIpAddresses(string s) {
        backtrack(s, 0, "");
        return res;
    }
};
```

### 8 子集

[78. 子集 - 力扣（LeetCode）](https://leetcode.cn/problems/subsets/)

- 选择列表：nums；
- 求子集的过程可以在沿途将内容加入进去，而不必遍历到末尾；

```cpp
class Solution {
    vector<vector<int>> res;
public:
    /*
    路径：回溯树的节点
    选择列表：数组未选择元素
    结束条件：到达回溯数底部
    */
    void backtrack(vector<int>& nums, int start_index, vector<int> &picked) {
        res.push_back(picked);
        for(int i = start_index; i < nums.size(); i++) {
            picked.push_back(nums[i]);
            backtrack(nums, i + 1, picked);
            picked.pop_back();
        }
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        vector<int> picked;
        backtrack(nums, 0, picked);
        return res;
    }
};
```

### 9 子集 II

[90. 子集 II - 力扣（LeetCode）](https://leetcode.cn/problems/subsets-ii/)

- 去重先排序

```cpp
class Solution {
    vector<vector<int>> res;
public:
    void backtrack(vector<int>& nums, int start_index, vector<int> &picked) {
        res.push_back(picked);
        unordered_set<int> memo;
        for(int i = start_index; i < nums.size(); i++) {
            if(memo.find(nums[i]) != memo.end()) {
                continue;
            }
            memo.insert(nums[i]);
            picked.push_back(nums[i]);
            backtrack(nums, i + 1, picked);
            picked.pop_back();
        }
    }

    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<int> picked;
        backtrack(nums, 0, picked);
        return res;
    }
};
```

### 10 递增子序列

[491. 递增子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/increasing-subsequences/)

- 递增；
- 在添加合理的字符串的路径上，依次添加子串；
- 由于有重复，而且添加的字串一定是递增的（有序），所以可以直接去重；

```cpp
class Solution {
    vector<vector<int>> res;
public:
    void backtrack(vector<int>& nums, int start_index, vector<int> &picked) {
        unordered_set<int> memo;
        for(int i = start_index; i < nums.size(); i++) {
            if(memo.find(nums[i]) != memo.end()) {
                continue;
            }
            if(!picked.empty() && picked[picked.size()-1] > nums[i]) {
                continue;
            }
            picked.push_back(nums[i]);
            memo.insert(nums[i]);
            if(picked.size() > 1) {
                res.push_back(picked);
            }
            backtrack(nums, i + 1, picked);
            picked.pop_back();
        }
    }

    vector<vector<int>> findSubsequences(vector<int>& nums) {
        vector<int> picked;
        backtrack(nums, 0, picked);
        return res;
    }
};
```

### 11 全排列

[46. 全排列 - 力扣（LeetCode）](https://leetcode.cn/problems/permutations/)

- 使用位图来记录已使用的索引，去重；

```cpp
class Solution {
    vector<vector<int>> res;
public:
    void backtrack(vector<int>& nums, vector<int> &picked, int limit) {
        if(picked.size() == nums.size()) {
            res.push_back(picked);
            return;
        }
        for(int i = 0; i < nums.size(); i++) {
            if((1 << i) & limit) {
                picked.push_back(nums[i]);
                backtrack(nums, picked, (1 << i) ^ limit);
                picked.pop_back();
            }
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        vector<int> picked;
        backtrack(nums, picked, (1 << nums.size()) - 1);
        return res;
    }
};
```



### 12 全排列II

[47. 全排列 II - 力扣（LeetCode）](https://leetcode.cn/problems/permutations-ii/)



```cpp
class Solution {
    vector<vector<int>> res;
public:
    void backtrack(vector<int>& nums, vector<int> &picked, int limit) {
        if(picked.size() == nums.size()) {
            res.push_back(picked);
            return;
        }
        unordered_set<int> memo;
        for(int i = 0; i < nums.size(); i++) {
            if(memo.find(nums[i]) != memo.end()) {
                continue;
            }
            if((1 << i) & limit) {
                picked.push_back(nums[i]);
                memo.insert(nums[i]);
                backtrack(nums, picked, (1 << i) ^ limit);
                picked.pop_back();
            }
        }
    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<int> picked;
        backtrack(nums, picked, (1 << nums.size()) - 1);
        return res;
    }
};
```



### 13 重新安排行程

[332. 重新安排行程 - 力扣（LeetCode）](https://leetcode.cn/problems/reconstruct-itinerary/)

- 保证机票行程有效；
- 字典序最小；

```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <map>

using namespace std;

class Solution {
    unordered_map<string, map<string, int>> tickets_map;
    vector<string> picked;
public:
    /*
        1. 每一个机场都有可能重复，所以我们可以让每一个起飞机场对应一个map存放目的地和其可访问的次数；
        2. 由于map是有序的，所以第一个合理的行程就是最小的字典序；  
        3. 对整理后的哈希表回溯遍历，找到第一个即可返回；
    */
    bool backtrack(string start, int N) {
        // base case
        if(picked.size() == N) {
            return true;
        }
        for(auto des : tickets_map[start]) {
            if(tickets_map[start][des.first]--) {
                picked.push_back(des.first);
                if(backtrack(des.first, N)) return true;
                picked.pop_back();
            }
            tickets_map[start][des.first]++;
        }
        return false;
    }

    vector<string> findItinerary(vector<vector<string>>& tickets) {
        for(auto ticket : tickets) {
            tickets_map[ticket[0]][ticket[1]]++;
        }
        picked.push_back("JFK");
        backtrack("JFK", tickets.size() + 1);
        return picked;
    }
};

int main() {
	Solution s;
	vector<vector<string>> tickets = {{"JFK","SFO"},{"JFK","ATL"},{"SFO","ATL"},{"ATL","JFK"},{"ATL","SFO"}};
	s.findItinerary(tickets);
}
```

#### Notes

注意，使用auto遍历时的返回值是值不是引用。

回溯暴力解（超时）：

```cpp
class Solution {
	vector<string> res;
public:
	vector<string> getMinDictSort(vector<string> &other) {
		if (res.empty()) return other;
		int i = 0;
		while (i < other.size() && other[i] == res[i]) i++;
		return i == other.size() ? res : other[i] < res[i] ? other : res;
	}

	void backtrack(vector<vector<string>>& tickets, string last, vector<string>& picked, vector<bool>& memo) {
		if (picked.size() == tickets.size()) {
			picked.push_back(last);
			res = getMinDictSort(picked);
			picked.pop_back();
			return;
		}
		//["JFK", "ATL", "JFK", "SFO", "ATL", "SFO"]
		for (int i = 0; i < tickets.size(); i++) {
			if (picked.empty() && "JFK" != tickets[i][0]) {
				continue;
			}
			if (memo[i]) {
				continue;
			}
			if (last == tickets[i][0]) {
				memo[i] = true;
				picked.push_back(tickets[i][0]);
				backtrack(tickets, tickets[i][1], picked, memo);
				picked.pop_back();
				memo[i] = false;
			}
		}
	}

	vector<string> findItinerary(vector<vector<string>>& tickets) {
		vector<bool> memo(tickets.size(), false);
		vector<string> picked;
		backtrack(tickets, "JFK", picked, memo);
		return res;
	}
};
```



### 14 N皇后

[51. N 皇后 - 力扣（LeetCode）](https://leetcode.cn/problems/n-queens/)

- 位图优化版；

```cpp
class Solution {
    vector<vector<string>> res;
public:
    int getIdx(int num) {
        int idx = -1;
        while(num) {
            num = num >> 1;
            idx++;
        }
        return idx;
    }

    void backtrack(int col_lim, int ldia_lim, int rdia_lim, int limit, int row, vector<string> &picked) {
        // base case : aready set down
        if(col_lim == limit) {
            res.push_back(picked);
            return ;
        }
        int pos = limit & (~(col_lim | ldia_lim | rdia_lim));
        while(pos != 0) {
            int most_right_one = pos & (~pos + 1);
            pos -= most_right_one;
            int idx = getIdx(most_right_one);
            picked[row][idx] = 'Q';
            backtrack((col_lim | most_right_one),
                (ldia_lim | most_right_one) << 1,
                (rdia_lim | most_right_one) >> 1,
                limit,
                row + 1,
                picked);
            picked[row][idx] = '.';
        }
    }

    vector<vector<string>> solveNQueens(int n) {
        vector<string> picked(n, string(n, '.'));
        backtrack(0, 0, 0, (1 << n) - 1, 0, picked);
        return res;
    }
};
```



### 15 解数独

二维递归

在这一题中，我们不仅需要在列上递归还要再行上递归。

#### 思路1

1. 既然在两个维度上递归，参数包含九宫格board、以及当前行列值row col；
2. 首先在列 col 上递归，每次从 1-9 中选择一个合理的数；
3. 当 col 越过边界时，行 row 进行一次递归跳转到下一行，如果递归返回false，返回上一行递归；
4. 将返回值 设置为 bool，找到一个合法的解法即返回；
5. 对于已经有数的位置，直接进行下一个位置递归，如果这个递归返回 false 说明解法不合理，不继续后面代码，向上回溯；
6. row越过 边界则说明找到正确结果，如果找不到合适的数 填写空白，返回false。

```cpp
#pragma once
#include <vector>
#include <iostream>
using namespace std;
 
class Solution {
public:
	void printBoard(vector<vector<char>>& board) {
		for (auto row : board) {
			for (auto ch : row) {
				std::cout << ch << " ";
			}
			std::cout << std::endl;
		}
		std::cout << std::endl;
	}

	bool isValid(vector<vector<char>>& board, int row, int col, int num) {
		// col
		char ch = '0' + num;
		for (int i = 0; i < 9; i++) {
			if (board[i][col] == ch) return false;
		}
		// row
		for (int j = 0; j < 9; j++) {
			if (board[row][j] == ch) return false;
		}
		// nine 
		int urow = 3 * (row / 3);
		int lcol = 3 * (col / 3);
		for (int i = urow; i < urow + 3; i++) {
			for (int j = lcol; j < lcol + 3; j++) {
				if (board[i][j] == ch) return false;
			}
		}
		return true;
	}

	bool backtrack(vector<vector<char>>& board, int row, int col) {
		printBoard(board);
		if (row >= 9) {
			return true;
		}
		if (col >= 9) {
            return backtrack(board, row + 1, 0);
		}
		if (board[row][col] != '.') {
			return backtrack(board, row, col + 1);
		}
		for (int i = 1; i < 10; i++) {
			if (!isValid(board, row, col, i)) {
				continue;
			}
			board[row][col] = '0' + i;
			if (backtrack(board, row, col + 1)) return true;
			board[row][col] = '.';
		}
		return false;
	}

	void solveSudoku(vector<vector<char>>& board) {
		backtrack(board, 0, 0);
	}
};
```

#### 思路2

1. 双层循环遍历数组；
2. 内层再嵌套一层循环选择数字；
3. 因为有唯一正确的解，所以如果算法正确一定能找到并结束递归；
4. 某一位置，如果9个数都不合法，说明前面的填写有误，需要向上回溯。

```cpp
bool backtrack(vector<vector<char>>& board) {
    for (int i = 0; i < board.size(); i++) {
        for (int j = 0; j < board.size(); j++) {
            if(board[i][j] != '.') continue;
            for(int k = 1; k < 10; k++) {
            // for(int k = '1'; k <= '9'; k++) { // 这样就不用转化了
                if(isValid(board, i, j, k)) {
                    board[i][j] = '0' + k;
                    if (backtrack(board)) return true;
                    board[i][j] = '.';
                }
            } 
            return false;
        }
    }
    return true;
}
```

