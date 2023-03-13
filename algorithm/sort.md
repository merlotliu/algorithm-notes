---
layout: post
title: 排序算法
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [排序算法]
categories: [算法&数据结构]
comments: true
---

## 时间复杂度

1. 常数操作：一个操作如果与样本数据量没有关系，每次都是固定时间内完成的操作；
2. 去掉低阶项，忽略高阶项系数，如果为f(N)，则时间复杂度为O(f(N))；
3. 多个算法理论值相同时（如都为O(N)），比较实际运行时间，即“常数项时间”；
4. O 表示最差情况下的时间复杂度（通常所说的时间复杂都也是指这个）；

## 选择排序

对于一组共有 N 个数的数组，共进行 N-1 轮移动：

- 第一轮：1~N中找到最小的数，移动到第1个位置；
- 第二轮：2~N中找到最小的数，移动到第2个位置；
- 第N-1轮：N-1~N中找到最小的数，移动到第N-1个位置；

### 时间复杂度

$$O(n^2)$$

### 代码

```c++
#pragma once

class Code01SelectionSort {
public:
	void static selectionSort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return ;
		}
		for (int i = 0; i < len - 1; i++) {
			int min_idx = i;
			for (int j = i + 1; j < len; j++) {
				if (arr[min_idx] > arr[j]) {
					min_idx = j;
				}
			}
			if (min_idx != i) {
				swap(arr, i, min_idx);
			}
		}
		return ;
	}

	void static swap(int *arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}
};
```



## 冒泡排序

对于一组共有 N 个数的数组，共进行 N-1 轮移动：

- 第一轮：12比较将大数换到2，23比较将大数换到3，直到 N-1 和 N 比较将本轮最大数换到 N 位置；
- 第二轮：直到本轮最大的数放置到 N-1；
- 第 N-1 轮：将12中最大的数放到 2 。

### 时间复杂度

$$O(n^2)$$

### 代码

```
#pragma once
class Code02BubbleSort {
public:
	void static bubbleSort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}

		for (int j = len - 1; j > 0; j--) {
			for (int i = 0; i < j; i++) {
				if (arr[i] > arr[i + 1]) {
					swap(arr, i, i + 1);
				}
			}
		}
		return;
	}

	void static swap(int *arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}
};
```



## 异或运算

- 相同为0，不同为1；
- 无进位相加；

性质：

- 0 ^ N = N, N ^ N = 0;
- 满足交换律和结合律；

### 交换两个数不引入新变量

```
void swap(int& a, int& b) {
	a = a ^ b;
	b = a ^ b; // b = a ^ b ^ b = a
	a = a ^ b; // a = a ^ b ^ a = b
}
```

#### Notes

- 两数值可以一样，但内存一样，异或会变成0；

### 题型一

一组数中，有一个数出现了奇数次，其他所有数出现了偶数次，求该出现了奇数次的数。

#### 解法

将这组数从头异或到尾，得到的结果就是该数。

```
int getOnlyOddTimesNum(int *arr, int len) {
	int eor = 0;
	for (int i = 0; i < len; i++) {
		eor ^= arr[i];
	}
	return eor;
}
```



### 题型二

一组数中，有两个数出现了奇数次，其他所有数出现了偶数次，求这两个出现了奇数次的数。

#### 解法

- 首先，全部异或得到两个数a和b的异或结果eor；
- 对于eor，进行eor&(~eor+1)的操作，获得最右侧为1的位置，这一位表示，a和b不相同的其中一位。对所有这一位为1或不为1的数异或，得到的结果就是其中一个数，在与eor异或可得到另外一个数。

```
std::vector<int> getTwoOddTimesNum(int *arr, int len) {
	int eor = 0;
	for (int i = 0; i < len; i++) {
		eor ^= arr[i];
	}
	int opp_byte = eor & (~eor + 1);
	int a = 0;
	for (int i = 0; i < len; i++) {
		if ((opp_byte & arr[i]) == 0) {
			a ^= arr[i];
		}
	}
	int b = eor ^ a;
	return {a, b};
}
```



## 插入排序

对于一组共有 N 个数的数组，共进行 N-1 轮移动。每一轮从当前位置向前看，依次比较前面的数，比前面的数小则交换，比前面的数大或者相等或前面没有数则停止，此时该部分的数已经有序：

- 第一轮：从第2个位置往前看，调整为有序；
- 第二轮：从第3个位置往前看，调整为有序

类似，打扑克抓牌顺牌的过程。

### 时间复杂度

数据状况会影响插入排序的时间复杂度

$$O(n^2)$$表示最差状况下的时间复杂度

### 代码

```
#pragma once
class Code03InsertionSort {
public:
	void static insertionSort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		for (int i = 1; i < len; i++) {
			for (int j = i; j > 0 && arr[j - 1] > arr[j]; j--) {
				swap(arr, j - 1, j);
			}
		}
	}

	void static swap(int *arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}
};

```



## 二分法

### 有序数组中某数是否存在

#### 时间复杂度

$$log_{2}n$$

### 有序数组中大于等于某数最左侧的位置

#### 解法

求大于等于时：记录大于等于的位置并保持更新；

求小于等于时：记录小于等于的位置并保持更新；

### 局部最小问题

无序数组，任何两个相邻数一定不相等，求一个局部最小（小于他的两个邻居）。



## 对数器

1. 有一个需要测的方法a，和一个暴力解法方法b；
2. 通过对数器测试方法a的正确性；
3. 小样本修改a、b保证正确性，大样本测试确定真的正确； 





## 递归

递归的时间复杂度分析。

### Master公式

$$T(N)=a*T(N/b)+O(N^d)$$

其中，N为母问题的规模，a为子问题的次数，N/b为子问题的规模，O(N^d)为除去递归子问题以外其他部分的时间复杂度。

在知道a、b、d的值以后，通过以下3种情况可以知道该问题的时间复杂度：

- $$log_{b}a>d$$时，时间复杂度为$$O(N*log_{b}a)$$；
- $$log_{b}a=d$$时，时间复杂度为$$O(N^d*logN)$$；
- $$log_{b}a<d$$时，时间复杂度为$$O(N^d)$$。

### 示例

对于求解一个数组中的最大值的递归解法：

可以通过二分递归的方法，将找数组中的最大值拆解为找数组左半部分的最大值和数组右半部分的最大值，在从这两个最大值的中找到最大值。对于左半部分和右半部分仍然可以这么做，直到每一部分只剩下一个数（递归结束条件），剩下一个数自然这个数就是这一部分最大值，返回即可。

```c++
#pragma once
class GetMax {
public:
	static int getMax(int *arr, int len) {
		return getMax(arr, 0, len - 1);
	}

	static int getMax(int *arr, int l, int r) {
		// end condition
		if (l == r) {
			return arr[l];
		}
		// recursion
		int m = l + (r - l) >> 1; // prevent overflow
		int left_max = getMax(arr, l, m);
		int right_max = getMax(arr, m+1, r);
		return max(left_max, right_max);
	}

	static int max(int a, int b) {
		return a > b ? a : b;
	}
};
```

#### 分析

在这一问题中，总问题每次被拆解为2个子问题，则子问题的规模为总问题的1/2，即b=2，每一次子问题被计算2次，即a=2。除去子问题的递归，我们每次需要计算一次中间值m，比较一次大小，这些操作都是常数操作，故这一部分时间复杂度为O(1)，即d=0。

所以，$$log_{b}a=log_{2}2=1>d=0$$，即时间复杂度为$$O(N*log_{2}2)=O(N)$$。



## 归并排序

将数组分为左右两部分，分别对这两部分进行排序，排序完成后，合并两部分的结果。

实际上，不断的将数组进行拆解，拆解到每一部分只剩下一个数的时候，一定是有序的。

合并时，首先申请一个额外的数组，然后同时遍历并依次比较两个部分的内容，每次将数小的添加进辅助数组，直到其中一部分内容遍历完，将另外一个部分全部添加到辅助数组的末尾。最后将辅助数组的内容赋值到原数组的对应位置即可。

```c++
class MergeSort{
public:
	void sort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		mergeSort(arr, 0, len - 1);
	}

	void mergeSort(int *arr, int l, int r) {
		if (l == r) {
			return;
		}
		int m = l + ( (r - l) >> 1 ); // >> 优先级低于 +，应该加括号
		mergeSort(arr, l, m);
		mergeSort(arr, m+1, r);
		merge(arr, l, m, r);
		return;
	}

	void merge(int *arr, int l, int m, int r) {
		int *help = new int[r - l + 1];
		int i = 0; 
		int p1 = l;
		int p2 = m+1;
		while (p1 <= m && p2 <= r) {
			help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
		}
		while (p1 <= m) {
			help[i++] = arr[p1++];
		}
		while (p2 <= r) {
			help[i++] = arr[p2++];
		}
		for (int j = l, i = 0; j <= r; ) { 
			arr[j++] = help[i++];
		}
		delete[] help;
		return;
	}
};
```

### 时间复杂度

时间复杂度O(nlogn)，空间复杂度O(n)



### 小和&逆序对

#### 小和问题

在一个数组中，每个数左边比当前数小的数累加起来，叫做这个数组的小和，求一个数组的小和。

例：[1, 3, 4, 2, 5] 1左边没有比1小的数；3左边比三小的数：1；4左边比4小的数：1, 3；2左边比2小的数：1；5左边比5小的数：1, 3, 4, 2；故小和：1+ 1+3 + 1+ 1 + 3 + 4 + 2=16

#### 逆序对问题

在一个数组中，左边的数比右边的数大，则两个数构成一个逆序对，请打印所有逆序对。

#### 解法

对于小和问题，我们发现，问题等价于，右边有几个数比当前数大则累加几次，结合归并排序，我们即可获得优于O(n^2)的解法。

对于合并部分的逻辑需要进行一些改动：

- 判断两部分大小的时候，如果相等，则不产生小和，并且先拷贝右侧内容；
- 如果左侧小于右侧，产生小和，右侧应该有右边界索引减去右侧当前指针索引下标再加1的数量，乘上当前左侧数，即为当前部分小和；例如：左侧为[1,3,4]，右侧为[2,5]时（合并时各部分一定是已经排好序的，这样才能确定后面有几个数比当前数大），对于1来说，右侧当前索引为0，边界索引为1，则小和为(1-0+1)*1=2；

```cpp
int getSmallSum(int *arr, int l, int r) {
	if (l == r) {
		return 0;
	}
	int m = l + ((r - l) >> 1);
	return getSmallSum(arr, l, m) + getSmallSum(arr, m + 1, r) + getPartSmallSum(arr, l, m, r);
}

int getPartSmallSum(int *arr, int l, int m, int r) {
	int *help = new int[r - l + 1];
	int i = 0;
	int p1 = l;
	int p2 = m + 1;
	int res = 0;
	while (p1 <= m && p2 <= r) {
		res += arr[p1] < arr[p2] ? (r - p2 + 1) * arr[p1] : 0;
		help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
	}
	while (p1 <= m) {
		help[i++] = arr[p1++];
	}
	while (p2 <= r) {
		help[i++] = arr[p2++];
	}
	for (int j = l, i = 0; j <= r; ) {
		arr[j++] = help[i++];
	}
	delete[] help;
	return res;
}
```



## 荷兰国旗问题

给定一个数组arr，和一个数num，请将小于num的数放在数组的左边，等于数num的数放在数组的中间，大于num的数放在数组的右边。

要求：额外空间复杂度O(1)，时间复杂度O(N)。

### 解法

首先，考虑一个更为简单的场景，在这个数组arr中，仅把小于等于num的数放在数组的左边，大于num的数放在右边。

要求：额外空间复杂度O(1)，时间复杂度O(N)。

对于这一问题，定义小于等于num的区域为L，起始区域为(-1, l]，标记该区域的右边界的指针l初始为-1。

然后开始遍历数组，数组中i位置的数包含两种情况：

- i 位置数 <= num：指针l+1的位置与i位置数交换，i++，从而区域L向右拓展；
- i 位置数 > num：i++。

遍历完数组后，则问题解决：

```
void easyNetherlandFlag(int num, int *arr, int len) {
	if (arr == nullptr || len < 2) {
		return;
	}
	int l = -1;
	for (int i = 0; i < len; i++) {
		if (arr[i] <= num) {
			swap(arr, i, ++l);
		}
		std::cout << arr[i] << std::endl;
	}
	return;
}
```



接着，解决荷兰国旗问题。定义小于num的区域为L，标记区域L的右边界的指针l初始值为-1，定义大于num的区域为R，标记区域R的左边界的指针r的初始值为n（数组长度）。

遍历数组时，会有三种情况：

- i 位置数 < num：指针l+1的位置与i位置数交换，i++（因为换过来的值一定是小于或等于num的数），从而区域L 向右拓展；
- i 位置数 = num：i++；
- i 位置数 > num：指针r-1的位置与i位置数交换，i不变（因为换过来的值尚未判断），从而区域R 向左拓展；

直到i遇到r时结束，问题也就解决：

```
void netherlandFlag(int num, int *arr, int len) {
	if (arr == nullptr || len < 2) {
		return;
	}
	int l = -1;
	int r = len;
	for (int i = 0; i < r; ) {
		if (arr[i] < num) {
			swap(arr, i++, ++l);
		} else if (arr[i] > num) {
			swap(arr, i, --r);
		} else {
			i++;
		}
	}
	return;
}
```



## 快排

影响快排效率的最重要因素就是，划分值的选取，选取的好时间复杂度趋向于O(n*logn)，反之趋向于O(n^2)。

具体步骤：

- 选取划分值：
  - 选择最后一个数作为划分值；
  - 每次随机选择一个数作为划分值；
- 根据划分值，划分区域（在这一部分遍历时要从区域的左端开始而不是从0开始）：
  - 小于划分值的部分在最左侧；
  - 等于划分值的部分在中间；
  - 大于划分值的部分在最右侧；
- 细分子区域（递归）；

```c++
#pragma once
#include <random>
#include <time.h>
#include <iostream>

class QuickSort {
public:
	void sort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		srand((unsigned int)time(nullptr));
		quickSort(arr, 0, len-1);
	}

private:
	void quickSort(int *arr, int L, int R) {
		// end condition
		if (L >= R) {
			return;
		}

		//int flag = arr[R]; // 2.0
		// random flag
		int flag = arr[(rand() % (R - L + 1)) + L]; // 3.0
		//std::cout << (R - L + 1) << " " << r << " " << flag << std::endl;
		// partition
		std::vector<int> bound = partition(flag, arr, L, R);
		// recursion
		quickSort(arr, L, bound[0]);
		quickSort(arr, bound[1], R);
	}

	std::vector<int> partition(int flag, int *arr, int L, int R) {
		// Notes: start from L, not 0
		int less = L - 1; 
		int more = R + 1;

		while (L < more) {
			if (arr[L] < flag) {
				swap(arr, L++, ++less);
			} else if (arr[L] > flag) {
				swap(arr, L, --more);
			} else {
				L++;
			}
		}
		return {less, more};
	}
};
```

### Notes

- 一定要特别注意边界条件；
- 每次遍历的的起始、终止条件；

### 时间复杂度

取决于选取的划分值

最好：O(n*logn) 最差：O(n^2) 

空间复杂度（主要用于记录划分值）：

最好：O(logn) 最差：O(n)



## 堆

本质是一颗完全二叉树，其结构为数组实现的完全二叉树结构。

> 完全二叉树《百度百科》
>
> 一棵深度为k的有n个结点的[二叉树](https://baike.baidu.com/item/二叉树/1602879?fromModule=lemma_inlink)，对树中的结点按从上至下、从左到右的顺序进行编号，如果编号为i（1≤i≤n）的结点与[满二叉树](https://baike.baidu.com/item/满二叉树/7773283?fromModule=lemma_inlink)中编号为i的结点在二叉树中的位置相同，则这棵二叉树称为完全二叉树。

> 满二叉树《百度百科》
>
> 一棵深度为k且有$$2^k-1$$个结点的二叉树称为满二叉树。

大根堆：每颗子树的顶点为最大值；

小根堆：每颗子树的顶点为最小值；

由于堆的底层结构为数组，所以每个数都有一个索引 index，通过这个索引，可以描述与其他位置的关系：

- (index * 2 + 1)表示其左孩子；
- (index * 2 + 2)表示其右孩子；
- ((index - 1) / 2)表示其父节点；

此外，会使用一个变量（如 len）记录堆的大小，在插入和删除数据时，更新该变量的值。这个值对应数组中的长度，数组中这一范围内的值为堆中值，之外的部分为堆外的区域。

### Heap Insert

对于已有的堆，将新的数字插入到堆中形成新的大根堆（或小根堆）的过程，称为heap insert。

对于大根堆的插入：

- 当前数据索引为 index，其父节点索引为 ((index - 1) / 2)。如果前者值大于其父节点值，则交换两者的值，并将 index 移动到父节点位置 ((index - 1) / 2)；

- 调整到什么时候停止？一种情况是 index 位置的值不大于其父节点值。另外一种情况是到达根节点为止。（实际上，因为代码中的使用((index - 1) / 2)计算父节点，所以当到达根节点时，((index - 1) / 2)=(-1/2)=0，判断条件不需要变动，此时计算出的父节点为他本身，他不大于它本身，自然就停下了）

```cpp
void heapInsert(int *arr, int index) {
	int parent = (index - 1) / 2;
	while (arr[index] > arr[parent]) {
		swap(arr, index, parent);
		index = parent;
	}
}
```



### Heapify

对于已经视为堆的数据（不是大根堆或小根堆），形成大根堆（或小根堆）的过程称为 heapify，也称堆化。

对于大根堆的 heapify：

- 对于当前索引为 index 的数据，只有三种可能：仅有左孩子、有左右孩子和没有孩子；
- 形成大根堆需保证每颗子树的根都是这棵树的最大值；
- 从根节点出发，与其左右孩子中的最大值比较，如果小于其中最大值，与最大值交换。在新位置重复此操作，直到到达一个位置，这个位置大于他的孩子或没有孩子为止；
- 时刻记得判断其孩子索引的合法性。

​		2									7								7

​	5 		7		===> 	5		2		===>		5		6

3	4	1	6				3	4	1	6				3	4	1	2

```cpp
void heapify(int *arr, int index, int heap_size) {
	int left = index * 2 + 1; // left child
	while (left < heap_size) { // left child exist
		int max_index = left + 1 < heap_size && arr[left + 1] > arr[left] ? left + 1 : left; // max of children
		max_index = arr[index] < arr[max_index] ? max_index : index; // max between parents & children
		if (max_index == index) {
			break;
		}
		swap(arr, index, max_index);
		index = max_index;
		left = index * 2 + 1;
	}
	return;
}
```



### 堆排序

通过大根堆可以将数组升序排列：

- 遍历数组，通过 heap insert 形成大根堆；
- 每次将根顶元素与堆的最后一个元素交换，堆大小 heap size 减一，并通过 heapify 操作将堆重新形成大根堆。重复该操作，直到剩下堆顶元素，数组有序。

```
#pragma once

class HeapSort{
public:
	void sort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		// construct big heap
		for (int i = 0; i < len; i++) { // O(N)
			heapInsert(arr, i); // O(N * logN)
		}
		int heap_size = len;
		swap(arr, 0, --heap_size); // O(1)
		while (heap_size > 0) { // O(N)
			heapify(arr, 0, heap_size); // O(N * logN)
			swap(arr, 0, --heap_size); // O(1)
		}
		return;
	}

	void heapInsert(int *arr, int index) {
		int parent = (index - 1) / 2;
		while (arr[index] > arr[parent]) {
			swap(arr, index, parent);
			index = parent;
		}
	}

	void heapify(int *arr, int index, int heap_size) {
		int left = index * 2 + 1; // left child
		while (left < heap_size) { // left child exist
			int max_index = left + 1 < heap_size && arr[left + 1] > arr[left] ? left + 1 : left; // max of children
			max_index = arr[index] < arr[max_index] ? max_index : index; // max between parents & children
			if (max_index == index) {
				break;
			}
			swap(arr, index, max_index);
			index = max_index;
			left = index * 2 + 1;
		}
		return;
	}
};
```

#### 时间复杂度

O(N*logN)

#### 构建大根堆

据分析得，遍历数组通过heap insert的方式构建大根堆，时间复杂度为 O(N*logN)。

```
for (int i = 0; i < len; i++) { // O(N)
	heapInsert(arr, i); // O(logN)
}
```

如果仅需构建大根堆且数据已经都知晓，我们可以采用，从后向前遍历数组，并采用 heapify 的方式构建，时间复杂度为O(N)。

```
for (int i = len - 1; i >= 0; i--) { 
	heapify(arr, i, len);
}
```

对于一个N个节点的大根堆构建，假设为满二叉树，最底层节点数为 (N+1)/2，也就是母问题为N时，可视为 N/2 ，且不用进行heapify（因为没有子节点，但仍进行了一些比较操作，视为一次操作），则我们可认为倒数第一层的时间复杂度为 T(N/2)。紧接着倒数第二层需要向下做一次heapify，另外到了底层仍需要额外的操作，所以时间复杂度为 T(N/4)\*2，倒数第三层为 T(N/8)*3 ... ... 

故 T(N) = T(N/2)\*1 + T(N/4)\*2 + T(N/8)\*3 + ... ...

然后，我们可以将式子 * 2，在错项减去原式，得到：

T(N) =  T(N/2) + T(N/4) + T(N/8) + ... ... = O(N)



### 优先级队列

仍然为堆结构。

已知一个几乎有序的数组，几乎有序是指，如果把数组排好序的话，每个元素移动的距离隔离不超过k，并且k相对于数组来说比较小。请选择一个合适的排序算法针对这组数据进行排序。

我们可以：

- 构建一个小根堆，将前k+1个数添加进去；
- 弹出堆顶元素，添加数组新元素，重复此操作，直至数组末尾；
- 将堆中所有元素依次弹出，数组有序；

```cpp
void sortDistanceLessK(int *arr, int len, int k) {
	std::priority_queue<int, std::vector<int>, std::greater<int>> q;
	int i = 0;
	for (; i <= std::min(k, len); i++) {
		q.push(arr[i]);
	}
	int j = 0;
	while (i < len) {
		arr[j++] = q.top();
		q.pop();
		q.push(arr[i++]);
	}
	while (!q.empty()) {
		arr[j++] = q.top();
		q.pop();
	}
}
```

#### Notes

- c++自带的优先级队列 priority_queue ，默认以大根堆存在，相当于传入 std::less<> 比较器；
- 传入 std::greater<> 表示构建小根堆；

### 数组扩容的策略

成倍扩容，即原来为 n，扩容为2*你，并将数组拷贝。

## 比较器

- 重载比较运算符；
- 帮助类这些复杂结构进行排序；

在 c++中，内置了 qsort 的方法帮助排序，而 qsort 的第四个参数就是所谓的比较器。

比较器的函数签名等于如下形式：`int cmp(const void *a, const void *b)`。

- 返回-1，第一个参数在前面；
- 返回1，第二个参数在前面；
- 返回0，无所谓谁前谁后；
- 参数表的顺序就是原来数组的前后顺序；
- 当第一个参数a小于b时（此时a本来就在b的前面），返回-1，则为升序；返回1（将小数放在后面了），则为降序；

```cpp
#pragma once
#include <string>

class Student {
public:
	Student(){}
	Student(std::string name, int no, int age): _name(name), _no(no), _age(age) {}

	void set(std::string name, int no, int age) {
		_name = name;
		_no = no;
		_age = age;
	}
public:
	std::string _name;
	int _no;
	int _age;
};

class Comp{
public:
	static void sort(int *arr, int len, int(*cmp)(const void*, const void*)) {
		qsort(arr, len, sizeof(int), cmp);
	}

	static void sortByDescendingOrder(int *arr, int len) {
		qsort(arr, len, sizeof(int), Comp::less);
	}

	static void sortByAscendingOrder(int *arr, int len) {
		qsort(arr, len, sizeof(int), Comp::greater);
	}

	static void studentSortByAgeAscendingOrder(Student *stus, int len) {
		qsort(stus, len, sizeof(Student), Comp::studentAgeGreater);
	}

	static int less(const void *a, const void *b) {
		// a < b 小的时候返回 1，即为降序
		const int arg1 = *static_cast<const int*>(a);
		const int arg2 = *static_cast<const int*>(b);
		
		/*if (arg1 < arg2) return 1;
		if (arg1 > arg2) return -1;
		return 0;*/
		return (arg1 < arg2) - (arg1 > arg2);
	}

	static int greater(const void *a, const void *b) {
		// a < b 小的时候返回 -1，即为升序
		const int arg1 = *static_cast<const int*>(a);
		const int arg2 = *static_cast<const int*>(b);
		/*if (arg1 < arg2) return -1;
		if (arg1 > arg2) return 1;
		return 0;*/
		return (arg1 > arg2) - (arg1 < arg2);
		//return (*static_cast<const int*>(a) - *static_cast<const int*>(b)); 
		// 存在 INT_MIN 时会出错
	}
	
	static int studentAgeGreater(const void *a, const void *b) {
		// 返回-1，第一个参数在前面
		// 返回1，第二个参数在前面
		// 参数表的顺序就是原来数组的前后顺序

		// 年龄升序
		const Student *stu1 = (static_cast<const Student*>(a));
		const Student *stu2 = (static_cast<const Student*>(b));
		if (stu1->_age < stu2->_age) {
			return -1;
		}
		if (stu1->_age > stu2->_age) {
			return 1;
		}
		// 如果年龄一样 学号小的在后面
		if (stu1->_no < stu2->_no) {
			return 1;
		}
		if (stu1->_no > stu2->_no) {
			return -1;
		}
		return 0;
		//return (arg1 > arg2) - (arg1 < arg2);
	}

	static void printStudents(Student *stus, int len) {
		if (stus == nullptr) {
			return;
		}
		for (int i = 0; i < len; i++) {
			std::cout << stus[i]._name << " " << stus[i]._no << " " << stus[i]._age << std::endl;
		}
		std::cout << std::endl;
	}
};
```



## 桶排序（计数排序）

对于员工年龄的排序。

由于员工年龄的大小是有大概的一个范围的（0-200），我们可以使用数组下标作为年龄，开辟一个长度201的数组，遍历员工年龄，出现对应的年龄则对应位置++，最后遍历该数组，打印员工年龄即可。

计数排序的时间复杂度为O(n)。

但其应用范围并不是很广，因为每一个计数排序算法，都是基于数据状况的，我们需要对特定问题做特定分析，修改代码后才能得到合适的解决方案。比如我们不对年龄进行排序了，而是对星期几进行排序，那么我们仅需要7个桶即可计数。

## 基数排序

对一组数进行排序（十进制、三进制......）。

- 找出数组中最大的数，并计算其位数，该位数即为后面出入桶的次数；
- 准备与进制等长的数组记录词频（十进制为10，三进制为3），并处理成前缀和的形式；
  - 前缀和的形式，即 i 位置的值为 0-i 位置的值的总和；
- 准备需要排序数据等长的辅助数组，帮助出入桶；

```cpp
#pragma once
#include <iostream>

#include "ISort.h"

class RadixSort : public ISort {
public:
	void sort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		// get max bit
		int bits = getBits(getMax(arr, 0, len-1));
		radixSort(arr, 0, len - 1, bits);
	}

	void radixSort(int *arr, int L, int R, int bits, int radix = 10) {
		int *help = new int[R - L + 1]; // 辅助数组
		for (int bit = 1; bit <= bits; bit++) { // 最大位为几出入桶几次
			//std::cout << "Bit : " << bit << std::endl;
			int *count = new int[radix](); // 统计词频
			// 遍历arr以统计词频
			for (int i = L; i <= R; i++) {
				int bit_num = gitBitNum(arr[i], bit, radix);
				count[bit_num]++;
			}
			// 遍历count处理成前缀和
			for (int i = 1; i < radix; i++) {
				count[i] += count[i - 1];
				//std::cout << count[i] << " ";
			}
			//std::cout << std::endl;
			// 为满足先进先出,从后往前遍历原数组，将数据填在count前缀和得出的辅助数组位置
			for (int i = R; i >= L; i--) {
				int bit_num = gitBitNum(arr[i], bit, radix);
				help[--count[bit_num]] = arr[i];
			}
			// 复制回原数组以保存该次处理结果
			for (int i = L, j = 0; i <= R; i++, j++) {
				arr[i] = help[j];
				//std::cout << arr[i] << " ";
			}
			//std::cout << std::endl;
			delete[] count;
		}
		delete[] help;
		return;
	}

	int getMax(int *arr, int L, int R) {
		int max_index = 0;
		for (int i = L; i <= R; i++) {
			max_index = arr[max_index] < arr[i] ? i : max_index;
		}
		return arr[max_index];
	}

	// 获取数字的位数
	int getBits(int num, int radix = 10) {
		int res = 0;
		while (num > 0) {
			num /= radix;
			res++;
		}
		return res;
	}

	// 获取bit位上的数字
	// 1 个位
	// 2 十位
	// ...
	int gitBitNum(int num, int bit, int radix = 10) {
		int res = 0;
		while(bit-- > 0) {
			res = num % radix;
			num /= radix;
		}
		return res;
	}
};
```

#### Notes

- 目前该版本仅支持正数的排序，负数一定报错。

## 排序算法总结

### 稳定性

所谓稳定性，是指相同值在排序前后，其相对位置是否改变。发生改变则不存在稳定性，不发生变化则稳定。

例如：{1, 5, 3, 5, 2, 4}，如果在通过排序算法A排序后，第一个5移动到了第二个后面，则不稳定，反之稳定。

#### 不稳定排序

选择排序：如{3, 3, 3, 1, 3, 3}，在第一轮变化后，第一个3会移动到1的位置。

快速排序：如{5, 5, 5, 3, 2}，在基准值为5的情况下，第一次划分后，第一个5会移动到3的位置，即原来第三个5的右边。

堆排序：如{5, 4, 4, 6}，在进行 heap insert的过程中，6会与第一个4交换，导致第一个4到达原来第二个4的右边。

#### 稳定排序

冒泡排序、插入排序、归并排序、基于桶的排序

### 排序算法时间、空间复杂度及稳定性

|          | 时间复杂度 | 空间复杂度 | 稳定性 |
| :------: | :--------: | :--------: | :----: |
| 选择排序 |   O(N^2)   |    O(1)    |   X    |
| 冒泡排序 |   O(N^2)   |    O(1)    |   √    |
| 插入排序 |   O(N^2)   |    O(1)    |   √    |
| 归并排序 | O(N*logN)  |    O(N)    |   √    |
| 快速排序 | O(N*logN)  |  O(logN)   |   X    |
|  堆排序  | O(N*logN)  |    O(1)    |   X    |

### Tips

目前，不低于时间复杂度O(N*logN)的排序算法。

也不存在时间复杂度O(N*logN)、空间复杂度O(1)且稳定的排序算法。

所以，目前来说后三种算法在不同场合下各有优劣。

- 小样本使用**插入排序**；
- 需要稳定性且高效时，使用**归并排序**；
- 普通大量数据使用**快速排序**（通常都是用快排，因为在大量测试下，快排的常数项是最小的）；
- 有内存限制的时候，使用**堆排序**；

### 工程应用中的排序

- 结合不同算法的优劣；
- 稳定性的考虑；

一些语言中或工程中内置的排序算法，通常是结合不同算法优劣产生的算法。

如在大量数据的时候采用快排划分数据，当数据到达小样本的范围，在使用插入排序。

或是普通数据采用快排，类等复杂结构使用归并（这是为了利用其稳定性，比如筛选商品，先筛选好评多的，在筛选便宜的，这样我们就能在好评多的基础上得到最便宜的商品）。

### 常见的坑

1. 归并排序内部缓存；（降低归并的空间复杂度为O(1)，会变得不稳定）
2. 01 stable sort；（快排稳定版，会使得空间复杂度上升为O(N)）
3. 奇数放在数组左边，偶数放在数组右边，保证原始次序不变。（01 stable sort）
