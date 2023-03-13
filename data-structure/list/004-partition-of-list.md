---
layout: post
title: 链表划分
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [链表]
categories: [算法&数据结构]
comments: true
---

## 链表划分

将单向链表值划分为左边小、中间相等、右边大的形式。中间值为pivot划分值。

要求：调整之后节点的相对次序不变，时间复杂度不高于O(N)，空间复杂度不高于O(1)。

### 方法1：数组 & 快排

整体思路就是，遍历一遍链表，把节点存入数组，对数组快排，然后再遍历数组，生成将节点重新连接。

该方法，**时间复杂度为O(N*logN)，空间复杂度为O(N)，且会改变相对次序。**

但最容易想到和实现。

```cpp
ListNode* LinkedList::partitionWithPivotAndArray(ListNode *head, int pivot) {
	if (head == nullptr || head->next == nullptr) return head;
	// push into array
	ListNode *cur = head;
	std::vector<ListNode*> arr;
	while (cur != nullptr) {
		arr.push_back(cur);
		cur = cur->next;
	}
	// partition
	int less = -1;
	int more = (int)arr.size();
	for (int i = 0; i < more; ) {
		if (arr[i]->val < pivot) {
			swap(arr[++less], arr[i++]);
		} else if (arr[i]->val > pivot) {
			swap(arr[--more], arr[i]);
		} else {
			i++;
		}
	}
	// rejoint
	int i = 1;
	for (; i < (int)arr.size(); i++) {
		arr[i - 1]->next = arr[i];
	}
	arr[i-1]->next = nullptr;
	return arr[0];
}

void LinkedList::swap(ListNode *a, ListNode *b) {
	ListNode tmp = *a;
	*a = *b;
	*b = tmp;
}
```

### 方法2：多个指针

主要是使用6个指针记录3个部分的头、尾位置。

**在判定完一个节点属于3个部分的哪个部分后：**

- 如果是当前这部分的第一个节点：将该部分头部head和tail的位置均赋值为该节点；
- 如果不是第一个节点，将该部分尾部tail的next指向当前节点，tail在移动到该节点；

**三部分连接：**

- 第1部分存在：
  - 第2部分存在：1尾部连接2头部；
  - 第2部分不存在：1尾部连接3头部；
- 不论第一部分存在与否：
  - 第2部分存在：2尾部连接3头部；

**判断头节点：**

- 返回less、pivot和more中不为空，且在前面的指针（即less不为空返回less，否则pivot不为空返回pivot，否则才返回more）。

```cpp
ListNode* LinkedList::partitionWithPivot(ListNode *head, int pivot) {
	if (head == nullptr || head->next == nullptr) return head;
	ListNode *less_head, *less_tail, *pivot_head, *pivot_tail, *more_head, *more_tail;
	less_head = less_tail = pivot_head = pivot_tail = more_head = more_tail = nullptr;
	// partition
	ListNode *cur = head;
	while (cur) {
		if (cur->val < pivot) {
			if (less_head == nullptr) {
				less_head = less_tail = cur;
			} else {
				less_tail->next = cur;
				less_tail = cur;
			}
		} else if (cur->val == pivot) {
			if (pivot_head == nullptr) {
				pivot_head = pivot_tail = cur;
			}
			else {
				pivot_tail->next = cur;
				pivot_tail = cur;
			}
		} else {
			if (more_head == nullptr) {
				more_head = more_tail = cur;
			}
			else {
				more_tail->next = cur;
				more_tail = cur;
			}
		}
		cur = cur->next;
	}
	// joint
	if (less_head != nullptr) {
		less_tail->next = pivot_head != nullptr ? pivot_head : more_head;
	}
	if (pivot_head != nullptr) {
		pivot_tail->next = more_head;
	}
	// final head
	head = less_head ? less_head : (pivot_head ? pivot_head : more_head);
	return head;
}
```

#### Notes

注意处理，小于部分、等于部分、大于部分有缺失的情况。
