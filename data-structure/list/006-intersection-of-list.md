---
layout: post
title: 找到两个链表相交的第一个节点
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [链表]
categories: [算法&数据结构]
comments: true
---

## 找到两个链表相交的第一个节点

给定两个链表，这两个链表可能有环，可能无环。判断这两个链表是否相交，相交则返回第一个相交的节点，不相交则返回nullptr。

首先，需要判断两个链表是否有环，有环的话入环节点的位置在哪？

然后，分情况判断两个链表是否相交：

- 两个无环链表：对于两个无环链表；
- 一个有环一个无环：**不可能相交**；
- 两个有环链表：入环点相同（一定相交）、入环点不相同；

```cpp
ListNode* LinkedList::findFirstIntersection(ListNode *head1, ListNode *head2) {
	if (head1 == nullptr || head2 == nullptr) {
		return nullptr;
	}
	// has cycle
	ListNode *loop1 = hasCycle(head1);
	ListNode *loop2 = hasCycle(head2);
	// 
	if (loop1 == nullptr && loop2 == nullptr) return findFirstIntersectionWithNoLoop(head1, head2);
	if (loop1 != nullptr && loop2 != nullptr) return findFirstIntersectionWithLoops(head1, loop1, head2, loop2);
	return nullptr;
}
```



### 链表是否有环

#### 方法1：哈希表（时：O(N)，空：O(N)）

使用哈希表，在链表遍历过程中，判断该节点是否在哈希表中：

- 该节点在表中，则说明有环，此时为入环第一个节点，返回该节点并退出；
- 该节点不在表中，说明无环，将节点指针加入表中，继续遍历；
- 遍历到空，则说明无环。

```cpp
ListNode* LinkedList::hasCycleBySet(ListNode *head) {
	if (head == nullptr || head->next == nullptr) return nullptr;
	std::unordered_set<ListNode*> set;
	ListNode *cur = head;
	while (cur) {
		if (set.find(cur) != set.end()) {
			return cur;
		}
		set.insert(cur);
		cur = cur->next;
	}
	return nullptr;
}
```

#### 方法2：快慢指针（时：O(N), 空：O(1)）

在快慢指针遍历链表的过程中，如果快指针遍历到nullptr，则说明无环。

如果有环，快慢指针一定会在环内相遇，当相遇发生之后，快指针回到头节点，慢指针不动，快慢指针同时一次一步的移动，直至相遇，相遇的位置即为入环的第一个节点。

```cpp
ListNode* LinkedList::hasCycle(ListNode *head) {
	if (head == nullptr || head->next == nullptr) return nullptr;
	ListNode *slow = head;
	ListNode *fast = head;
	while (fast != nullptr && fast->next != nullptr) {
		slow = slow->next;
		fast = fast->next->next;
		if (slow == fast) break;
	}
	if (fast == nullptr || fast->next == nullptr) return nullptr;
	
	fast = head;
	while (fast != slow) {
		slow = slow->next;
		fast = fast->next;
	}
	return slow;
}
```

##### Notes

fast和slow必须同时从head出发！（fast=head->next，slow=head这样的出发就会差一个节点，永远无法结束）

### 链表相交

#### 无环链表相交

最好理解和想到的方法就是，遍历两个链表，计算出两个链表的长度差值，让长链表先移动差值步，接着继续同时遍历，直到相遇或都为nullptr。

```
ListNode* LinkedList::findFirstIntersectionWithNoLoop(ListNode *head1, ListNode *head2) {
	ListNode *cur1 = head1;
	ListNode *cur2 = head2;
	int diff = 0;
	while (cur1) {
		cur1 = cur1->next;
		diff++;
	}
	while (cur2) {
		cur2 = cur2->next;
		diff--;
	}
	cur1 = diff > 0 ? head1 : head2; // cur1 -> the longer list
	cur2 = cur1 == head2 ? head1 : head2;
	diff = diff < 0 ? -diff : diff; // abs
	// cur1 move diff steps first
	while (diff--) cur1 = cur1->next;
	
	while (cur1 != cur2) {
		cur1 = cur1->next;
		cur2 = cur2->next;
	}
	return cur1;
}
```

或者使用交换遍历的方式，因为无论如何两个链表都遍历的话，最后要不就相交，要不就都为nullptr。

注意，两个节点和一个节点相交时的循环判断。

```
ListNode* LinkedList::findFirstIntersectionWithNoLoopEx(ListNode *head1, ListNode *head2) {
	ListNode *cur1 = head1;
	ListNode *cur2 = head2;
	while (cur1 != cur2) {
		cur1 = cur1->next;
		cur2 = cur2->next;
		if (cur1 == nullptr && cur2 == nullptr) return nullptr;
		if (cur1 == nullptr) cur1 = head2;
		if (cur2 == nullptr) cur2 = head1;
	}
	return cur1;
}
```

#### 有环链表相交

有环链表有入环节点相同和入环节点不同两种情况：

- 入环节点相同的话肯定相交，交点也一定在头节点到入环节点之间，等价于无环链表相交；
- 入环节点不同的情况下：如果相交则，这两个节点一定都在一个环上，从其中一个节点开始遍历，到回到这个节点的过程中，如果没有遇到另外一个节点，则说明不相交，反之相交，随意返回其中一个节点即可。

```cpp
ListNode* LinkedList::findFirstIntersectionWithLoops(ListNode *head1, ListNode *loop1, ListNode *head2, ListNode *loop2) {
	if (loop1 == loop2) {
		ListNode *cur1 = head1;
		ListNode *cur2 = head2;
		while (cur1 != cur2) {
			cur1 = cur1->next == loop1->next ? head2 : cur1->next;
			cur2 = cur2->next == loop1->next ? head1 : cur2->next;
		}
		return cur1;
	} else {
		ListNode *cur = loop1->next;
		while (cur != loop1) {
			cur = cur->next;
			if (cur == loop2) return cur;
		}
		return nullptr;
	}
}
```


