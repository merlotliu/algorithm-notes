---
layout: post
title: 复制含随机指针的链表
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [链表]
categories: [算法&数据结构]
comments: true
---

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


