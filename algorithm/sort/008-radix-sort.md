---
layout: post
title: 基数排序
date: 2023-12-13 00:14:33
updated: 2023-02-27 00:14:33
tags: [排序算法, 基数排序]
categories: [算法&数据结构]
comments: true
---

## 基数排序

对一组数进行排序（十进制、三进制......）。

- 找出数组中最大的数，并计算其位数，该位数即为后面出入桶的次数；
- 准备与进制等长的数组记录词频（十进制为10，三进制为3），并处理成前缀和的形式；
    - 前缀和的形式，即 i 位置的值为 0-i 位置的值的总和；
- 准备需要排序数据等长的辅助数组，帮助出入桶；

```cpp
#pragma once
#include <iostream>

#include "ISort.h"

class RadixSort : public ISort {
public:
	void sort(int *arr, int len) {
		if (arr == nullptr || len < 2) {
			return;
		}
		// get max bit
		int bits = getBits(getMax(arr, 0, len-1));
		radixSort(arr, 0, len - 1, bits);
	}

	void radixSort(int *arr, int L, int R, int bits, int radix = 10) {
		int *help = new int[R - L + 1]; // 辅助数组
		for (int bit = 1; bit <= bits; bit++) { // 最大位为几出入桶几次
			//std::cout << "Bit : " << bit << std::endl;
			int *count = new int[radix](); // 统计词频
			// 遍历arr以统计词频
			for (int i = L; i <= R; i++) {
				int bit_num = gitBitNum(arr[i], bit, radix);
				count[bit_num]++;
			}
			// 遍历count处理成前缀和
			for (int i = 1; i < radix; i++) {
				count[i] += count[i - 1];
				//std::cout << count[i] << " ";
			}
			//std::cout << std::endl;
			// 为满足先进先出,从后往前遍历原数组，将数据填在count前缀和得出的辅助数组位置
			for (int i = R; i >= L; i--) {
				int bit_num = gitBitNum(arr[i], bit, radix);
				help[--count[bit_num]] = arr[i];
			}
			// 复制回原数组以保存该次处理结果
			for (int i = L, j = 0; i <= R; i++, j++) {
				arr[i] = help[j];
				//std::cout << arr[i] << " ";
			}
			//std::cout << std::endl;
			delete[] count;
		}
		delete[] help;
		return;
	}

	int getMax(int *arr, int L, int R) {
		int max_index = 0;
		for (int i = L; i <= R; i++) {
			max_index = arr[max_index] < arr[i] ? i : max_index;
		}
		return arr[max_index];
	}

	// 获取数字的位数
	int getBits(int num, int radix = 10) {
		int res = 0;
		while (num > 0) {
			num /= radix;
			res++;
		}
		return res;
	}

	// 获取bit位上的数字
	// 1 个位
	// 2 十位
	// ...
	int gitBitNum(int num, int bit, int radix = 10) {
		int res = 0;
		while(bit-- > 0) {
			res = num % radix;
			num /= radix;
		}
		return res;
	}
};
```

#### Notes

- 目前该版本仅支持正数的排序，负数一定报错。