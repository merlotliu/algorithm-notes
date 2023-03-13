---
layout: post
title: 二叉树
date: 2023-12-13 00:14:33
updated: 2023-03-13 00:14:33
tags: [二叉树]
categories: [算法&数据结构]
comments: true
---

# 二叉树

## 基础

### 1 节点结构

含有三個成员变量，一个值域，两个指针域。

```cpp
class TreeNode {
public:
	TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int v, TreeNode *l, TreeNode *r) : val(v), left(l), right(r) {}
public:
	int val;
	TreeNode *left;
    TreeNode *right;
};
```

### 2 二叉树遍历

#### 递归

##### 递归序

```cpp
void recursion(TreeNode *root) {
	//[1]
	if(root == nullptr) {
		return;
	}
	//![1]
	//[2]
	recursion(root->left);
	//![2]
    //[3]
	recursion(root->right);
	//![3]
}
```

对于这一段递归代码，每个节点将会访问3次（每次的访问范围已经标注）。

对于使用以上递归代码遍历如下的二叉树：

​		1

​	2 	   3

4	5	6	7

访问的完整路径：1, 2, 4, 4, 4, 2, 5, 5, 5, 2, 1, 3, 6, 6, 6, 3, 7, 7, 7, 3, 1。

知道这一点之后，通过控制处理数据（如：打印）的在上面3个位置范围的中的一个，即可完成二叉树的前中后序遍历。

##### 前中后序遍历

```cpp
void BinaryTree::preorderTraversalRecursion(TreeNode *root) {
	if (root == nullptr) return;
	std::cout << root->val << " ";
	preorderTraversalRecursion(root->left);
	preorderTraversalRecursion(root->right);
}

void BinaryTree::inorderTraversalRecursion(TreeNode *root) {
	if (root == nullptr) return;
	inorderTraversalRecursion(root->left);
	std::cout << root->val << " ";
	inorderTraversalRecursion(root->right);
}

void BinaryTree::postorderTraversalRecursion(TreeNode *root) {
	if (root == nullptr) return;
	postorderTraversalRecursion(root->left);
	postorderTraversalRecursion(root->right);
	std::cout << root->val << " ";
}
```

#### 非递归

在二叉树的递归遍历中，系统会帮助我们压栈弹栈。而在非递归遍历中，前中后序遍历都需要我们自己完成压栈和弹栈的操作。

##### 前序遍历

在前序遍历中，我们首先将根节点压入栈中，因为根节点一定是有，无论他是否为null。然后我们就可以开始做一些重复的迭代工作直至栈空：

- 弹栈并处理该节点；
- 依次将右孩子、左孩子压入栈中（如果存在的话）；（为什么是先右孩子、再左孩子，因为前序遍历的顺序是根、左、右，而出栈的顺序与弹栈的顺序相反）

```cpp
void BinaryTree::preorderTraversal(TreeNode *root) {
	if (root == nullptr) return;
	std::stack<TreeNode*> stk;
	stk.push(root);
	while (!stk.empty()) {
		TreeNode *cur = stk.top();
		std::cout << cur->val << " ";
		stk.pop();
		if (cur->right) stk.push(cur->right);
		if (cur->left) stk.push(cur->left);
	}
}
```



##### 中序遍历

每一颗二叉树可以通过**左边界**进行拆分。

在中序遍历（**左根右**）的过程中，我们需要进行以下迭代过程：

- 将左边界压入栈中（当前节点及其左孩子、左孩子的左孩子......）；
- 弹栈，处理数据，并将其右孩子的左边界（右孩子及其左孩子......）压栈（如果存在）；

实际上，这一过程就是**左根右（左根右（左根右（null）））**，即右子树每次都被处理成了**左根**的形式。

```cpp
void BinaryTree::inorderTraversal(TreeNode *root) {
	if (root == nullptr) return;
	std::stack<TreeNode*> stk;
	TreeNode *cur = root;
	while (!stk.empty() || cur != nullptr) {
		if (cur != nullptr) {
			while (cur != nullptr) {
				stk.push(cur);
				cur = cur->left;
			}
		} else {
			cur = stk.top();
			stk.pop();
			std::cout << cur->val << " ";
			cur = cur->right;
		}
	}
}
```



##### 后序遍历

后序遍历的顺序是**左右根**，而前序遍历的顺序是**根左右**，我们根据前序遍历可以改变左右孩子入栈的顺序达到**根右左**的效果。而我们在以**根右左**的方式存入一个辅助栈中，最后统一出栈即可得到后序遍历的结果。

```cpp
void BinaryTree::postorderTraversal(TreeNode *root) {
	if (root == nullptr) return;
	std::stack<TreeNode*> stk;
	std::stack<TreeNode*> help;
	stk.push(root);
	while (!stk.empty()) {
		TreeNode *cur = stk.top();
		help.push(cur);
		stk.pop();
		if (cur->left) stk.push(cur->left);
		if (cur->right) stk.push(cur->right);
	}
	while (!help.empty()) {
		std::cout << help.top()->val << " ";
		help.pop();
	}
	return;
}
```



### 3 直观打印二叉树





### 4 二叉树宽度优先遍历

对于二叉树的宽度优先遍历，需要用到队列。首先将根节点加入队列，然后进行重复的迭代：

- 出队列，数据处理；
- 依次将左、右加入队列（如果存在）；

```cpp
void BinaryTree::levelTraversal(TreeNode *root) {
	if (root == nullptr) return;
	std::queue<TreeNode*> que;
	que.push(root);
	while (!que.empty()) {
		TreeNode *cur = que.front();
		std::cout << cur->val << " ";
		que.pop();
		if (cur->left) que.push(cur->left);
		if (cur->right) que.push(cur->right);
	}
	return;
}
```



#### 二叉树最大宽度

获取二叉树的最大宽度，要求我们知道每一层的宽度并比较大小。

##### 方法1：哈希表

准备几个变量，分别记录当前是第几层、当前层出现的节点总数、当前最大的宽度（初始值为INT_MIN），在使用哈希表记录<当前节点, 层数>。（约定根节点为第一层）

- 取出队首元素，通过map取得节点层数，判断是否与当前层一致：
  - 相同：该层计数++；
  - 不同：说明已经进入下一层，结算上一层计数（与max比较取大者），因为已经弹出了下一层的第一个元素了，所以计数为1，且将当前层数++；
- 依次将左孩子，右孩子进队列，并添加入map，层数为当前层+1；

跳出循环之后，在处理最后一层的宽度和当前最大值的比较即可。

```cpp
int BinaryTree::getMaxWidthByMap(TreeNode *root) {
	if (root == nullptr) return 0;
	std::queue<TreeNode*> que;
	que.push(root);
	std::unordered_map<TreeNode*, int> ref;
	int cur_level = 1;
	ref.insert({ root, 1 });
	int max = INT_MIN;
	int cur_count = 0;
	while (!que.empty()) {
		TreeNode *cur = que.front();
		que.pop();
		if (ref[cur] == cur_level) {
			cur_count++;
		}
		else {
			max = max > cur_count ? max : cur_count;
			cur_count = 1;
			cur_level++;
		}
		if (cur->left) {
			que.push(cur->left);
			ref.insert({ cur->left, cur_level + 1 });
		}
		if (cur->right) {
			que.push(cur->right);
			ref.insert({ cur->right, cur_level + 1 });
		}
	}
	max = max > cur_count ? max : cur_count;
	return max;
}
```

##### 方法2：仅使用有限几个变量

同样的，如果要计算宽度，我们需要知道每一层的结束和开始。

使用几个变量，记录当前层的最后一个节点（初始head）、下一层的最后一个节点（初始null）、当前层的节点数（初始0）以及当前最大的宽度（初始最小值）。在宽度优先遍历的基础之上：

- 弹出队首元素；
- 添加左右孩子时，更新下一层的最后一个节点；
- 判断当前节点是否为当前层最后一个元素：
  - 不是：当前节点数++；
  - 是：当前节点数++，与max比较，当前层最后一个节点赋值为下一层最后一个节点；

```cpp
int BinaryTree::getMaxWidth(TreeNode *root) {
	if (root == nullptr) return 0;
	std::queue<TreeNode*> que;
	que.push(root);
	int max = INT_MIN;
	int cur_count = 0;
	TreeNode *cur_end = root;
	TreeNode *next_end = nullptr;
	while (!que.empty()) {
		TreeNode *cur = que.front();
		que.pop();
		if (cur->left) {
			que.push(cur->left);
			next_end = cur->left;
		}
		if (cur->right) {
			que.push(cur->right);
			next_end = cur->right;
		}
		cur_count++;
		if (cur == cur_end) {
			max = max < cur_count ? cur_count : max;
			cur_count = 0;
			cur_end = next_end;
		}
	}
	return max;
}
```

## 二叉树相关概念

### 1 二叉搜索树（BST）

二叉搜索树：满足左子树的元素都小于当前节点，右子树的元素都大于当前节点的二叉树。

#### 方法1：数组判断是否升序

判断一棵树是否为二叉搜索树的简单方法是，对二叉树进行一次中序遍历，并将遍历的结果存入一个数组中，遍历结束后，对数组进行升序检查，只要出现一个逆序则不是BST，反之是BST。

#### 方法2：递归 & 比较前驱与后继的大小

使用一个值记录前驱节点的值，到达当前节点则比较：

- 前驱大：不是BST，返回；
- 后继大：继续遍历，直到为null；

base case 为 true

```cpp
int pre_val = INT_MIN;

bool isBST(TreeNode *root) {
	if (root == nullptr) return true;
	bool left = isBST(root->left);
	if (left == false) return false;
	if (pre_val >= root->val) {
		return false;
	} else {
		pre_val = root->val;
	}
	return isBST(root->right);
}
```



### 2 完全二叉树（CBT）

从根节点到最后一个叶子节点，中间没有节点空缺的二叉树。

- 有右孩子必须有左孩子；
- 如果一个节点开始，没有右孩子或左右孩子，那么后面的节点都是叶子节点。

所以判定一颗树是否为CBT，可以使用宽度优先遍历：

- 当前节点有右孩子没有左孩子：false；
- 当前节点没有孩子或没有右孩子，后面仍出现非叶子节点：false；

```cpp
bool isCBT(TreeNode *root) {
	std::queue<TreeNode*> que;
	que.push(root);
	bool leaf = false;
	while (!que.empty()) {
		TreeNode *cur = que.front();
		que.pop();
		if (cur->left) {
			que.push(cur->left);
		}
		if (cur->right) {
			que.push(cur->right);
		}
		if ((leaf && (cur->left != nullptr || cur->right != nullptr)) 
			|| (cur->left == nullptr && cur->right != nullptr)) {
			return false;
		}
		if (cur->right == nullptr) {
			leaf = true;
		}
	}
	return true;
}
```



### 3 满二叉树（FBT）

满二叉树：节点总数为n，树的深度为h，满足 2^h - 1 = n 的二叉树。

计算总结点数和树的深度，并比较其关系。

```cpp
int getTreeDepth(TreeNode *root) {
	if (root == nullptr) return 0;
	int left_depth = getTreeDepth(root->left) + 1;
	int right_depth = getTreeDepth(root->right) + 1;
	return left_depth >= right_depth ? left_depth : right_depth;
}

int getNodesCount(TreeNode *root) {
	if (root == nullptr) return 0;
	return getNodesCount(root->left) + getNodesCount(root->right) + 1;
}

bool isFBT(TreeNode *root) {
	return (getNodesCount(root) == ((1 << getTreeDepth(root)) - 1));
}
```



### 4 平衡二叉树（AVL）

空树或左右子树的高度差的绝对值不超过1，且左右子树也都是平衡二叉树。

**使用套路**，判定一棵树是否为AVL树：

- 判定一棵树是否为AVL，可以先判定左子树是否为AVL，在判定右子树是否为AVL，最后比较左右子树的高度即可；
- base case ： 空树认为是AVL；

```cpp
class RetVal {
public:
	RetVal(bool avl, int d) : is_avl(avl), depth(d) {}

public:
	bool is_avl;
	int depth;
};

RetVal isAVL(TreeNode *root) {
	if (root == nullptr) return RetVal(true, 0);

	RetVal left = isAVL(root->left);
	if (!left.is_avl) return RetVal(false, left.depth);
	RetVal right = isAVL(root->right);
	if (!right.is_avl) return RetVal(false, right.depth);
	
	bool is_avl = abs(left.depth - right.depth) <= 1;
	int max_depth = left.depth >= right.depth ? left.depth + 1 : right.depth + 1;
	return RetVal(is_avl, max_depth);
}
```

### 树型DP套路

对于二叉树的树形DP问题，均可采用此套路解题。所谓DP，即一个母问题可以拆解为若干子问题，且子问题与母问题同构。树型DP，即对于一棵树，可以通过左右子树的结果组成母问题的结果。

以获取当前树的最大深度为例：

- 当前树的最大深度即为，左右子树深度大的，再+1；
- 而左右子树，亦可是他们各自的左右子树的最大深度，再+1；
- base case : root == null, 返回 0；

**改成模板（套路）**：

- 将整棵树的问题拆分成左右子树的问题：该问题需要什么？可以从左右子树获得什么？
- 左右子树向下重复操作；
- base case 是什么？
- 由于是递归，需要保证从左右子树获取的信息是一样的，处理可以稍有不同；
- 获取的信息过多时可以，定义一个class，作为返回值。

## 经典题型

### 1 最低公共祖先

#### 方法1：记录每个节点的父节点

我们可以：

- 遍历一遍二叉树，使用哈希表记录每个节点的父节点；
- 再使用哈希表记录，从node1到根节点的路径（所有节点）；
- 让node2向跟节点移动，直到再node1-root的路径中发现有相同节点。

```cpp
void findParent(TreeNode *root, TreeNode *parent, std::unordered_map<TreeNode*, TreeNode*> &parentMap) {
	if (root == nullptr) return;
	parentMap.insert({ root, parent });
	findParent(root->left, root, parentMap);
	findParent(root->right, root, parentMap);
}

TreeNode* findCommonAncestor(TreeNode *root, TreeNode *node1, TreeNode *node2) {
	std::unordered_map<TreeNode*, TreeNode*> parentMap;
	findParent(root, nullptr, parentMap);
	
	std::unordered_set<TreeNode*> node1_path;
	TreeNode *cur = node1;
	while (cur != nullptr) {
		node1_path.insert(cur);
		cur = parentMap[cur];
	}

	cur = node2;
	while (cur != nullptr) {
		if(node1_path.find(cur) != node1_path.end()) return cur;
		cur = parentMap[cur];
	}
	return nullptr;
}
```

#### 方法2：递归写法

对于两个节点node1和node2，只有两种可能：

- node1为node2的祖先，或者node2为node1的祖先；
- node1和node2不是其他一个的祖先；

那么在递归的过程中：

- base case : 如果遇到null、node1、node2，就返回null、node1、node2；
- 如果左右孩子都不为null，则返回当前节点；
- 如果至少有一个为null，返回不null的那个，都为null，也会返回null；

```cpp
TreeNode* findCommonAncestorRecur(TreeNode *root, TreeNode *node1, TreeNode *node2) {
	if (root == nullptr || root == node1 || root == node2) return root;

	TreeNode *left = findCommonAncestorRecur(root->left, node1, node2);
	TreeNode *right = findCommonAncestorRecur(root->right, node1, node2);

	if (left != nullptr || right != nullptr) return root;
	return (left == nullptr ? right : left);
}
```



### 2 后继节点

前驱和后继的概念是基于中序遍历，对于一个节点的前驱即为中序遍历的前一个节点，后继则为中序遍历的后一个节点。

对于每一个节点的后继节点，在O(N)的时间复杂度内，通过中序遍历肯定是可以得到的。

那么如果在以下的二叉树节点类型情况下，能否在O(k)的时间复杂度下，找到他的后继节点？（k为当前节点到后继节点的实际距离）

根结点的父节点定义为null。

```cpp
class TreeNodeP {
public:
	TreeNodeP(int v) : val(v), left(nullptr), right(nullptr), parent(nullptr) {}

public:
	int val;
	TreeNodeP *left;
	TreeNodeP *right;
	TreeNodeP *parent;
};
```

对于一个节点的后继：

- 如果该节点有右树，则为右树的最左节点；
- 如果该节点无右树，则该节点一定存在于其后继节点的左子树上；
- 此外，整颗树的最右节点一定是无后继的（在判定是否为其后继的左子树最后会走到null）

```cpp
TreeNodeP* getSuccessor(TreeNodeP *node) {
	if (node == nullptr) return nullptr;
	// there is right tree
	TreeNodeP *successor = nullptr;
	if (node->right != nullptr) {
		successor = node->right;
		while (successor->left) {
			successor = successor->left;
		}
		return successor;
	}
	// not
	TreeNodeP *cur = node;
	TreeNodeP *successor = cur->parent;
	while(successor != nullptr && successor->left != cur) {
		cur = successor;
		successor = cur->parent;
	}
	return successor;
}
```



### 3 二叉树序列化和反序列化

序列化：将数据转化成字符串的形式；

反序列化：将字符串转化为数据的形式；

序列化转化成的字符串应该是与数据唯一对应的，以便反序列化的时候能还原原本的数据。

对于二叉树的序列化和反序列化：

- 可以使用任何一种遍历的方式完成序列化；
- 不管使用哪种方式，转化为字符串都应该是：val_ 的形式val表示节点的值或唯一对应的字符串，节点为空的时候使用一个特殊符号占位（如#），下划线_表示值的结束；
- 反序列化时，通过下划线_将值分割出来，然后根据对应的序列化的重建方法，对二叉树重建；

对于先序遍历，重建时，依次构建根节点、左子树和右子树；

```cpp
void serializing(TreeNode *root, std::string &res) {
	if (root == nullptr) {
		res += "#_";
		return;
	}
	res += std::to_string(root->val);
	res += "_";
	serializing(root->left, res);
	serializing(root->right, res);
}

void split(std::vector<std::string> &res, std::string &str, char delim) {
	std::stringstream ss(str);   //读取str到字符串流中
	std::string tmp;
	//使用getline函数从字符串流中读取,遇到分隔符时停止,和从cin中读取类似
	//注意,getline默认是可以读取空格的
	while (std::getline(ss, tmp, delim)) {
		res.push_back(tmp);
	}
	return ;
}

TreeNode* deserializing(std::vector<std::string> &arr, int &idx) {
	if (arr[idx] == "#") {
		idx++;  
		return nullptr;
	}
	TreeNode *root = new TreeNode(atoi(arr[idx++].c_str()));
	root->left = deserializing(arr, idx);
	root->right = deserializing(arr, idx);
	return root;
}

TreeNode* deserializing(std::string &str) {
	std::vector<std::string> res;
	char delim = '_';
	split(res, str, delim);
	int idx = 0;
	return deserializing(res, idx);
}
```



### 4 MS面试题：n次折纸的折痕朝向

折痕从上至下的顺序：

折1次：1条凹折痕，记作A；

折2次：2条凹折痕，1条凸折痕（在A的上方会有1条凹折痕记作B，下方一条凸折痕记作C）；（B（凹） A（凹） C（凸））

折3次：2条凹，1条凸，2条凹，2条凸（在B的上方会有一条凹折痕，下方一条凸折痕，C也是如此）；（D（凹）B（凹）E（凸） A（凹）F（凹） C（凸）G（凸））

使用二叉树画出来就是：

 									A（凹）

​					B（凹）						C（凸）

​			D（凹）	E（凸）		F（凹）	G（凸）

所以n次的情况下，即为高度为n的以上类型树的中序遍历。

这颗树的特点：

- 根节点和左孩子为凹；
- 右孩子为凸；

所以我们只需要按此特点构造出二叉树，再中序遍历即可。

```cpp
TreeNode* generateTree(TreeNode *cur, int n) {
	if (n == 1) return nullptr;
	cur->left = new TreeNode("down");
	cur->right = new TreeNode("up");
	generateTree(cur->left, n - 1);
	generateTree(cur->right, n - 1);
	return cur;
}

void inorderTraversal(TreeNode *root) {
	if (root == nullptr) return;
	inorderTraversal(root->left);
	std::cout << root->val_str << " ";
	inorderTraversal(root->right);
}

// down 凹 up 凸
void printPaperCreaseFacing(int n) {
	if (n < 1) return;
	TreeNode *root = new TreeNode("down");
	generateTree(root, n);
	inorderTraversal(root);
}
```

##### 更精简的版本

- 在二叉树递归中需要区分左右时，可以在进入左子树时，传入区分的参数；

```cpp
void printPaperFolds(int level, int N, bool down) {
	if (level > N) return;
	printPaperFolds(level + 1, N, true);
	std::cout << (down ? "down" : "up") << " ";
	printPaperFolds(level + 1, N, false);
}

void printPaperFolds(int n, bool down) {
	if (n < 1) return;
	printPaperFolds(n - 1, true);
	std::cout << (down ? "down" : "up") << " ";
	printPaperFolds(n - 1, false);
}
```



## 阅读别人的代码

在纸上写下所有声明的变量，依据算法，记录数据变动的方式。

- 设置的变量；
- 数据的变动；

