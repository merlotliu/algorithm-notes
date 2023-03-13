---
layout: post
title: 反转链表
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [链表]
categories: [算法&数据结构]
comments: true
---

## 反转链表

分别实现单向链表和双向链表的反转。

要求：长度为N的链表，时间复杂度为O(N)，额外空间复杂度为O(1)。

### 反转单向链表：

#### 方法1（使用栈，时：O(N)，空：O(N)）：

1. 第一次遍历将数据添加至栈中；
2. 定义一个空节点，tmp记录，cur指向该节点；
3. 栈不为空开始循环出栈：
   1. cur的next指向的栈顶元素；
   2. 栈顶元素出栈；
   3. cur移动到next的位置；
4. cur现在在最后一个位置，将其next赋值为nullptr；
5. cur指向tmp（空节点）的next位置，并删除tmp；
6. 返回cur（新的头节点）；

```cpp
LinkedNode* LinkedList::reverseWithStack(LinkedNode *head) {
	if (head == nullptr || head->next == nullptr) {
		return head;
	}
	std::stack<LinkedNode*> stk;
	LinkedNode* cur = head;
	while (cur) {
		stk.push(cur);
		cur = cur->next;
	}
	LinkedNode* tmp = new LinkedNode();
	cur = tmp;
	while (!stk.empty()) {
		cur->next = stk.top();
		stk.pop();
		cur = cur->next;
	}
	cur->next = nullptr;
	cur = tmp->next;
	delete tmp;
	return cur;
}
```

#### 方法2（双指针，时：O(N)，空：O(1)）：

双指针解法：

1. 定义两个指针new_head，cur，初始new_head指向head，cur指向head的next；
2. cur不为nullptr则开始循环：
   1. head的next赋值为cur的next；
   2. cur的next赋值为new_head；
   3. new_head移动到cur；
   4. cur移动到head的next；
3. 最后返回new_head即可。

```cpp
LinkedNode* LinkedList::reverse(LinkedNode *head) {
	if (head == nullptr || head->next == nullptr) {
		return head;
	}
	LinkedNode *new_head = head;
	LinkedNode *cur = head->next;
	while (cur) {
		head->next = cur->next;
		cur->next = new_head;
		new_head = cur;
		cur = head->next;
	}
	return new_head;
}
```

### 反转双链表

```cpp
DoubleLinkedNode* LinkedList::reverseDoubleLinkedList(DoubleLinkedNode *head) {
	if (head == nullptr || head->next == nullptr) {
		return head;
	}
	DoubleLinkedNode *pre = head;
	DoubleLinkedNode *cur = head->next;
	while (cur) {
		DoubleLinkedNode *tmp = pre->next;
		pre->next = pre->pre;
		pre->pre = tmp;
		
		pre = cur;
		cur = cur->next;
	}
	return pre;
} 
```
