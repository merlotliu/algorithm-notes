## 生成随机数组

### rand()

- rand() 位于 **cstdlib** 头文件；
- 任何一次对 rand 的调用，都将得到一个 0~RAND_MAX (c 定义的一个宏)之间的伪随机；
- rand 每次调用会查询是否调用 srand(seed) 设置随机数种子，随机数种子不变，生成的随机数序列不变，系统默认的随机数种子为 1，即 srand(1)。
  这也就是为什么直接使用 rand，每次执行的结果一样。

### srand(time(0))

- srand 使用设置随机数种子，为了保证每次产生的随机数不一样，通常配合 time(0) 使用。

- time(0) 能够获取当前时间，为当前格林尼治标准时间与格林尼治标准时间1970年0分0秒的时间间隔秒数。

### 常见应用

```c++
	// 获取 [ 0 , x ) 的随机整数
	rand() % x;

	// 获取 [ 0 , x ] 的随机整数
	rand() % (x+1);
  
  // 获取 [ a , b ) 的随机整数
  rand() % (b - a) + a;

  // 获取 ( a , b ] 的随机整数
  rand() % (b - a) + a + 1;

  // 获取 [ a , b ] 的随机整数：
  rand() % (b - a + 1) + a;

  // 获取 0 - 1 之间的浮点数：
  rand() / double(RAND_MAX)。
```

### 生成随机数组

```cpp
#include <cstdlib>


```





## Reference

1. [C++随机数](https://blog.csdn.net/EmileJiao/article/details/123351432);
2. [RAND_MAX](https://baike.baidu.com/item/RAND_MAX/1651444?fr=aladdin)
