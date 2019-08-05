---
title: pat - 1029 Median
date: 2019-08-03 22:41:11
tags:
category:
    - 计算机
    - 数据结构
    - pat
---
## 1029 Median (25 分)
Given an increasing sequence S of N integers, the median is the number at the middle position. For example, the median of S1 = { 11, 12, 13, 14 } is 12, and the median of S2 = { 9, 10, 15, 16, 17 } is 15. The median of two sequences is defined to be the median of the nondecreasing sequence which contains all the elements of both sequences. For example, the median of S1 and S2 is 13.

Given two increasing sequences of integers, you are asked to find their median.
<!--more-->
### Input Specification:
Each input file contains one test case. Each case occupies 2 lines, each gives the information of a sequence. For each sequence, the first positive integer N (≤2×10
​5
​​ ) is the size of that sequence. Then N integers follow, separated by a space. It is guaranteed that all the integers are in the range of long int.

### Output Specification:
For each test case you should output the median of the two given sequences in a line.

#### Sample Input:
```
4 11 12 13 14
5 9 10 15 16 17
```
#### Sample Output:
```
13
```

## 这道题干啥呢
输出两个数组合并后的中位数。

## 坑点
首先数据量很大，如果全部读进去排序就内存炸了，因此不能这么做。开一个int数组，足够容纳第一个数组即可。  
其次，只能一边读入第二个数组一边计算，观察容易发现数组都是排好序的，总量也是知道的，因此可以很容易计算出中位数的位置`mid`，接下来就是寻找这个位置的数。  
相对于第一个数组，我们可以认为中位数所在的位置有三种情况：1，在第一个数组的前面，2，在第一个数组中，3，在第一个数组的后面。  
相对于第二个数组，里面的每个数字都可以被插入到第一个数组的前面、中间、后面。  
那么，我们可以知道，mid这个位置由两部分组成：第一个数组的前面一部分数字和第二个数组的前面一部分数字。

## 代码
```c++

#include <cstdio>
#include <iostream>
using namespace std;
void Mid() {
    int list[200005];
    int n1, n2, t;
    cin >> n1;
    for (int i = 0; i < n1; i++) {
        scanf("%d", &list[i]);
    }
    cin >> n2;
    auto mid = (n1 + n2 - 1) / 2;
    int ind = 0;
    int count = 0;
    for (int i = 0; i < n2; i++) {
        scanf("%d", &t);
        while (t > list[ind] && ind < n1) {
            if (count == mid) {
                cout << list[ind];
                return;
            }
            ind++;
            count++;
        }

        if (count == mid) {
            cout << t;
            return;
        }
        count++;
    }
    while (ind < n1) {
        if (count == mid) {
            cout << list[ind];
            return;
        }
        ind++;
        count++;
    }
}
int main() {
    Mid();
    return 0;
}
```