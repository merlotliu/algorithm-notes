---
layout: post
title: 判断链表是否回文
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [链表]
categories: [算法&数据结构]
comments: true
---

## 判断链表是否回文

要求：时间辅助度O(N)，空间复杂度O(1)

### 方法1：栈（不考虑空间复杂度）

1. 遍历一次链表，将节点地址依次压栈；
2. 再此遍历链表，每遍历一个节点，与栈顶元素比对，相等则栈顶元素出栈。
3. 如果直到链表结束和栈空元素都相等，则为回文，中间只要有一个不相等，返回false。

```cpp
bool LinkedList::isPalindromeListWithStack(ListNode *head) {
	if (head == nullptr) return false;
	if (head->next == nullptr) return true;
	// push into stack
	std::stack<ListNode*> stk;
	ListNode *cur = head;
	while (cur) {
		stk.push(cur);
		cur = cur->next;
	}
	// pop out stack & compare
	cur = head;
	while (!stk.empty()) {
		if (cur->val != stk.top()->val) return false;
		cur = cur->next;
		stk.pop();
	}
	return true;
}
```

### 方法2：快慢指针 & 栈

相对于方法1节省一半的空间，但时间复杂度和空间复杂度不变。

1. 快慢指针，快指针一次走两步，慢指针一次走一步。第一次快指针遍历结束时，慢指针应到达中间位置（奇数时到达中间位置，偶数时向上取整的位置），记录当前慢指针的位置；
2. 慢指针继续移动，并依次将节点指针添加进栈；
3. 依次出栈并重新遍历链表，直至栈空；
4. 遍历过程中出现不相同的情况则为false，反之为true。

```cpp
bool LinkedList::isPalindromeListWithStackAndTwoPoints(ListNode *head) {
	if (head == nullptr) return false;
	if (head->next == nullptr) return true;
	// find middle position
	ListNode *slow = head;
	ListNode *fast = head->next;
	while (fast != nullptr && fast->next != nullptr) {
		slow = slow->next;
		fast = fast->next->next;
	}
	if (fast != nullptr) slow = slow->next; // even
	// push into stack
	std::stack<ListNode*> stk;
	while (slow != nullptr) {
		stk.push(slow);
		slow = slow->next;
	}
	// pop out stack & compare
	while (!stk.empty()) {
		if (head->val != stk.top()->val) return false;
		stk.pop();
		head = head->next;
	}
	return true;
}
```

#### Notes

特别注意，奇数和偶数情况下的指针定位。

如果要满足，奇数时到达中间位置，偶数时向上取整的位置。我们应该在快指针遍历完之后，判断是否为偶数，可以通过快指针是否为nullptr判断，然后偶数情况下慢指针先往后移动一步，然后在开始遍历剩下部分入栈。

### 方法3：快慢指针 & 反转后半链表

该方法，时间复杂度仍为O(N)，空间复杂度降低为O(1)。

1. 快慢指针，快指针一次走两步，慢指针一次走一步。第一次快指针遍历结束时，慢指针应到达中间位置（奇数时到达中间位置，偶数时向**下**取整的位置），记录当前慢指针的位置（记为mid）；
2. 从慢指针到末尾的位置反转，并记录末尾的位置（记为tail）；
3. 从两端开始遍历，左边是从head，右边从tail。奇数情况下都会遍历到mid，偶数情况下，当左边遍历到mid，即遍历完成。
   1. 遍历过程中，一旦出现不一样的值，即false，反之true。

```cpp
bool LinkedList::isPalindromeListWithTwoPoints(ListNode *head) {
	if (head == nullptr) return false;
	if (head->next == nullptr) return true;
	// find middle position
	ListNode *slow = head;
	ListNode *fast = head->next;
	while (fast != nullptr && fast->next != nullptr) {
		slow = slow->next;
		fast = fast->next->next;
	}
	ListNode *mid = slow;
	// reverse
	fast = slow->next;
	ListNode *tail = LinkedList::reverse(slow->next);
	fast->next = mid;
	mid->next = nullptr;
	// traverse & compare
	bool flag = true;
	slow = head;
	fast = tail;
	while (slow != mid) {
		if (slow->val != fast->val) {
			flag = false;
			break;
		}
		slow = slow->next;
		fast = fast->next;
	}
	// odd : the same node
	// even : the last middle node
	if (slow->val != fast->val) flag = false;
	// reverse back
	LinkedList::reverse(tail);

	return flag;
}
```

#### Notes

其中，反转后半部分链表的函数，即为上文的反转单链表算法。再返回之前需要把链表复原。


