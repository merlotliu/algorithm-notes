---
layout: post
title: 堆排序
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [排序算法, 堆排序]
categories: [算法&数据结构]
comments: true
---

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