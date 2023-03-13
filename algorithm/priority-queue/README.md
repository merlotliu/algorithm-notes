---
layout: post
title: 优先级队列
date: 2022-12-13 22:49:55
updated: 2023-02-27 22:49:55
tags: [优先级队列, 大根堆, 小根堆]
categories: [算法&数据结构]
comments: true
---

## 优先级队列

大根堆和小根堆

## 重写比较器

当我们需要向优先级队列中添加复杂数据结构时，需要重写比较器。比较器的重写可以通过类或者lambda表达式。

**优先级比较器返回 false 第一个参数放在前面，反之后面的数放在前面**（有些困惑，因为在 `sort`对`vector`等容器排序时，返回 `true` 第一个参数放前面）。如 `lhs` 和 `rhs`，在 比较器为 `return lhs > rhs` 的情况下，`lhs` 和 `rhs` 中较小的数在前面，较大的数在后面。

### 类

如果通过类的形式，则应该像下面一样创建一个类并重写其 `()` 运算：

```c++
template<typename T>
class comparator {
public:
    bool operator()(const T &lhs, const T &rhs) {
        return lhs < rhs; 
        /* 如果是复杂数据结构，则还需要参考 T 的 < 操作，如果没有重写或为简单数据结构，则构建大根堆 */
        // return lhs > rhs;
        // 小根堆
    }
};

# 使用
priority_queue<T, vector<T>, comparator<T>> q;
```

### lambda 表达式

由于 `stl` 优先级队列的第三个参数为模板类类型，所以需要通过 `decltype` 进行一个自动推导出表达式函数指针的类型。

```c++
# 定义比较器
auto cmp = [](const T &left, const T &right) { return (left) < (right);};
# 使用
# decltype 自动推到出类型
# 并将比较器传给 优先级队列
priority_queue<T, vector<T>, decltype(cmp)> q(cmp);

# 等价于
# bool (*cmp)(const int &a, const int &b) = [](const int &a, const int &b){
#		return a > b;
#	};
# priority_queue<T, vector<T>, bool (*)(const int &a, const int &b)> q(cmp);
# 即 decltype(cmp) 被推导为了 bool (*)(const int &a, const int &b)
```

### 其他

事实上，应该还有一种方式间接的修改比较器的结果。我们知道 `stl` 提供了 `std::greater<T>` 和 `std::less<T>` 两个模板比较器，那么如果我们重写 `T` 的 `>` （greater）或 `<` （less），配合上对应的模板比较器，是不是也能实现对应的比较功能？答案当然是 OK 的。
