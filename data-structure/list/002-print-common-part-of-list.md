---
layout: post
title: 打印两个链表的公共部分
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [链表]
categories: [算法&数据结构]
comments: true
---

## 打印两个链表的公共部分

两个链表有序

要求：长度为N的链表，时间复杂度为O(N)，额外空间复杂度为O(1)。

两个链表只要有一个为空或到达空，则后续无需在遍历，直接返回。

使用head1和head2遍历两个链表：

- head1->val == head2->val：打印数据，两个指针同时向后移动一步；
- head1->val < head2->val：head1指针同时向后移动一步；
- head1->val > head2->val：head2指针同时向后移动一步；

```cpp
void LinkedList::printCommonPart(LinkedNode *head1, LinkedNode *head2) {
	if (head1 == nullptr || head2 == nullptr) {
		return;
	}
	while (head1 && head2) {
		if (head1->val == head2->val) {
			std::cout << head1->val << " ";
			head1 = head1->next;
			head2 = head2->next;
		}
		else if (head1->val < head2->val) {
			head1 = head1->next;
		}
		else {
			head2 = head2->next;
		}
	}
	std::cout << std::endl;
	return;
}
```