---
layout: post
title: 链表
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [链表]
categories: [算法&数据结构]
comments: true
---

# 链表

相对于数组而言，链表的插入效率更高。

## 链表解题技巧

- 额外的数据结构（哈希表）；
- 快慢指针；

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



### 打印两个链表的公共部分

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

## 复制含随机指针的链表

该链表节点的结构如下：

```cpp
class ListRandomNode {
public:
	ListRandomNode() : val(0), next(nullptr), random(nullptr) {}
	ListRandomNode(int v, ListRandomNode *n, ListRandomNode *r) : val(v), next(n), random(r) {}
public:
	int val;
	ListRandomNode *next;
	ListRandomNode *random;
};
```

要求：时间复杂度O(N)，空间复杂度O(1)；

### 方法1：哈希表

时间复杂度O(N)，空间复杂度O(N)；

具体思路：

- 遍历链表，复制每个节点并存入map，key为原节点，val为新节点；
- 再次遍历链表，每个新节点都可以通过源节点和map找到，据此连接next和random；
  - 新节点的next就是，map[源节点]的next；
  - 新节点的random就是，map[源节点]的random；

```cpp
ListRandomNode* LinkedList::copyListWithRandomByMap(ListRandomNode *head) {
	if (head == nullptr ) return head;
	std::unordered_map<ListRandomNode*, ListRandomNode*> ref;
	// create new node
	ListRandomNode *cur = head;
	while (cur != nullptr) {
		ListRandomNode *tmp = new ListRandomNode(cur->val, nullptr, nullptr);
		ref[cur] = tmp;
		cur = cur->next;
	}
	// joint new node
	cur = head;
	while (cur != nullptr) {
		ref[cur]->next = ref[cur->next];
		ref[cur]->random = ref[cur->random];
		cur = cur->next;
	}
	return ref[head];
}
```



### 方法2：next

时间复杂度O(N)，空间复杂度O(1)；

将每一个新节点放在原来节点的后面，并连接上下一个节点的方式，这样通过next就能够定位到新节点，通过next的next就能找到原来的下一个节点。

- 遍历一遍链表，遍历的过程中，为每一个节点创建一个新节点，新节点连接在原来两个节点之间；
- 再次遍历链表，为random赋值：新节点的random就是，源节点random的next；
- 再将源节点和新节点分离出来，返回新节点头即可。

```cpp
ListRandomNode* LinkedList::copyListWithRandom(ListRandomNode *head) {
	if (head == nullptr) return head;
	// create new node
	ListRandomNode *cur = head;
	while (cur != nullptr) {
		ListRandomNode *cur_ = new ListRandomNode(cur->val, nullptr, nullptr);
		ListRandomNode *tmp = cur->next;
		cur->next = cur_;
		cur_->next = tmp;
		cur = tmp;
	}
	// assign random pointers
	cur = head;
	while (cur != nullptr) {
		cur->next->random = cur->random ? cur->random->next : nullptr;
		cur = cur->next->next;
	}
	// split
	cur = head;
	ListRandomNode *new_head = head->next;
	ListRandomNode *cur_ = head->next;
	while (cur_->next != nullptr) {
		cur->next = cur_->next;
		cur = cur->next;
		cur_->next = cur->next;
		cur_ = cur_->next;
	}
	cur->next = nullptr;
	cur_->next = nullptr;
	return new_head;
}
```

### 辅助函数

根据数组生成带随机值的链表：

```cpp
ListRandomNode* LinkedList::generateListWithRandom(int *arr, int len) {
	if (arr == nullptr || len < 1) {
		return nullptr;
	}
	std::vector<ListRandomNode*> vec;
	ListRandomNode *head = new ListRandomNode(arr[0], nullptr, nullptr);
	ListRandomNode *cur = head;
	for (int i = 1; i < len; i++) {
		vec.push_back(cur);
		cur->next = new ListRandomNode(arr[i], nullptr, nullptr);
		cur = cur->next;
	}
	vec.push_back(cur);
	for (int i = 0; i < len; i++) {
		vec[i]->random = vec[rand() % len];
	}
	return head;
}
```

打印随机链表：

```cpp
void LinkedList::printListWithRandom(ListRandomNode *head) {
	while (head) {
		std::cout << head->val << "[" << head->random->val << "]" << (head->next ? "->" : " ") ;
		head = head->next;
	}
	std::cout << std::endl;
}
```



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

# 面试&笔试

在面试和笔试中，对算法的要求应有所区分。

在笔试中，题量多时间少，我们要尽量采取写出容易想到并且时间复杂度符合要求的算法，通常可以以空间换时间。

而在面试中的题，通常难度更小，为了给面试官留下深刻的影响，应尽量写出低时间复杂度，低空间复杂度，能体现代码水平的代码。