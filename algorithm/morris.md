---
layout: post
title: Morrris 遍历
date: 2022-12-13 22:49:55
updated: 2023-03-13 22:49:55
tags: [Morrris, 二叉树]
categories: [算法&数据结构]
comments: true
---

# Morris 遍历

时间复杂度 O(n)，空间复杂度 O(1)。

## 主要逻辑

假设当前节点为 cur ，cur 的初始值为根节点 root；

然后开始遍历二叉树，直至 cur 为空：

1) 如果 cur 没有左孩子， cur 向右移动 cur = cur.right；
2) 如果 cur 有左孩子，找到左子树上最右的节点 mostRight ：
   1) 如果 mostRight 的右指针为 null ，让其指向 cur，然后 cur 向左移动到左孩子（cur = cur.left）；
   2) 如果 mostRight 的右指针不为 null，将 mostRight 的指向改为 null，然后 cur 向右移动（cur = cur.right）;

值得注意的是，有左孩子的节点会遍历两次，没有左孩子的节点只会被遍历一次。

```c++
/* morris 遍历，有左孩子的节点会遍历两次，没有左孩子的节点只会被遍历一次 */
void BinaryTree::morrisTraversal(TreeNode *root) {
	if (root == nullptr) {
		return;
	}
	TreeNode *cur = root;
	TreeNode *mostRight = nullptr;
	while (cur != nullptr) {
		if (cur->left != nullptr) {/* 有左孩子 */
			/* 找到左子树最右孩子 */
			mostRight = cur->left;
			/* 右指针不为null，不等于当前节点时继续 */
			while (mostRight->right != nullptr && mostRight->right != cur) {
				mostRight = mostRight->right;
			}
			if (mostRight->right == nullptr) { /* 第一次遍历此节点 */
				//![1] 操作节点 
				printf("%d ", cur->val);
				//[1]
				mostRight->right = cur;
				cur = cur->left;
				continue;
			} else if (mostRight->right == cur) { /* 第二次遍历此节点 */
				//![2] 操作节点

				//[2]
				mostRight->right = nullptr;
			}
		}
		//![3] 操作节点
		printf("%d ", cur->val);
		//[3]
		cur = cur->right; /* 左子树遍历完或没有左子树则遍历右子树 */
	}
	printf("\n");
	return;
}
```

通过 morris 遍历，可以修改出前中后需遍历。其中，前序和中序遍历非常简单：

- 前序遍历：不管是遍历几次的节点，都在第一次遍历时候处理节点（打印）；
- 中序遍历: 遍历一次的节点直接处理（打印），遍历两次的节点第二次遍历时才处理节点（打印）；

## morris 后序遍历

相对而言，后序遍历的代码稍微难一些。因为为了保证 O(1) 的空间复杂度，是不应该使用栈的。具体可以：

- 不管是遍历几次的节点，在第一次遍历时，不进行任何处理；
- 而在遍历两次的节点，遍历到第二次时，**逆序打印该节点的左孩子的右边界**；
- 遍历结束后，单独打印整棵树的右边界；

### 那么如何逆序打印右边界？

实际上，这就是一个单链表逆序打印的问题，将链表逆序打印，再逆序回来即可

### 代码

```c++
/* morris 后序遍历 ：
- 不管是遍历几次的节点，在第一次遍历时，不进行任何处理；
- 而在遍历两次的节点，遍历到第二次时，**逆序打印该节点的左孩子的右边界** （单链表逆序）；
- 遍历结束后，单独打印整棵树的右边界； */

/* 反转右边界仅操作 right指针 */
TreeNode *reverse(TreeNode *root) {
	TreeNode *next = root->right;
	root->right = nullptr;
	while (next != nullptr) {
		TreeNode *tmp = next->right;
		next->right = root;
		root = next;
		next = tmp;
	}
	return root;
}

void printfEdge(TreeNode *root) {
	TreeNode *tmp = reverse(root);
	TreeNode *cur = tmp;
	while (cur != nullptr) {
		printf("%d ", cur->val);
		cur = cur->right;
	}
	reverse(tmp);
}

void BinaryTree::morrisPostorderTraversal(TreeNode *root) {
	if (root == nullptr) {
		return;
	}
	TreeNode *cur = root;
	TreeNode *mostRight = nullptr;
	while (cur != nullptr) {
		if (cur->left != nullptr) {/* 有左孩子 */
			/* 找到左子树最右孩子 */
			mostRight = cur->left;
			/* 右指针不为null，不等于当前节点时继续 */
			while (mostRight->right != nullptr && mostRight->right != cur) {
				mostRight = mostRight->right;
			}
			if (mostRight->right == nullptr) { /* 第一次遍历此节点 */
				mostRight->right = cur;
				cur = cur->left;
				continue;
			}
			else if (mostRight->right == cur) { /* 第二次遍历此节点 */
				mostRight->right = nullptr;
				//![1] 操作节点 
				printfEdge(cur->left);
				//[1]
			}
		}
		cur = cur->right; /* 左子树遍历完或没有左子树则遍历右子树 */
	}
	printfEdge(root);
	printf("\n");
	return;
}
```