---
title: 'pat - 1044 Shopping in Mars'
date: 2019-08-19 20:39:05
mathjax: true
tags: 二分查找
category:
    - 计算机
    - 数据结构
    - pat
---
## 1044 Shopping in Mars (25 分)
Shopping in Mars is quite a different experience. The Mars people pay by chained diamonds. Each diamond has a value (in Mars dollars M\$). When making the payment, the chain can be cut at any position for only once and some of the diamonds are taken off the chain one by one. Once a diamond is off the chain, it cannot be taken back. For example, if we have a chain of 8 diamonds with values M\$3, 2, 1, 5, 4, 6, 8, 7, and we must pay M$15. We may have 3 options:

Cut the chain between 4 and 6, and take off the diamonds from the position 1 to 5 (with values 3+2+1+5+4=15).
Cut before 5 or after 6, and take off the diamonds from the position 4 to 6 (with values 5+4+6=15).
Cut before 8, and take off the diamonds from the position 7 to 8 (with values 8+7=15).
Now given the chain of diamond values and the amount that a customer has to pay, you are supposed to list all the paying options for the customer.

If it is impossible to pay the exact amount, you must suggest solutions with minimum lost.
<!--more-->

### Input Specification:
Each input file contains one test case. For each case, the first line contains 2 numbers: N (≤10^​5), the total number of diamonds on the chain, and M (≤10^​8), the amount that the customer has to pay. Then the next line contains N positive numbers D​1⋯D​N(D​i≤10^​3 for all i=1,⋯,N) which are the values of the diamonds. All the numbers in a line are separated by a space.

### Output Specification:
For each test case, print i-j in a line for each pair of i ≤ j such that Di + ... + Dj = M. Note that if there are more than one solution, all the solutions must be printed in increasing order of i.

If there is no solution, output i-j for pairs of i ≤ j such that Di + ... + Dj >M with (Di + ... + Dj −M) minimized. Again all the solutions must be printed in increasing order of i.

It is guaranteed that the total value of diamonds is sufficient to pay the given amount.

#### Sample Input 1:
```
16 15
3 2 1 5 4 6 8 7 16 10 15 11 9 12 14 13
```

#### Sample Output 1:
```
1-5
4-6
7-8
11-11
```

#### Sample Input 2:
```
5 13
2 4 5 7 9
```

#### Sample Output 2:
```
2-4
4-5
```

## 这题讲啥呢
题目相当于取一串数字中连续的一段，这一段数字之和要和给的值一样，输出所有这些段。如果没有一样的，输出和最接近（但大于）的段。  

## 坑点
直接算有两个用例会超时，研究研究发现，对于每个位置的数字，它到最前面的和是递增的，两个数字到最前面的和相减 加上数字本身（也相当于到后面那个数字的和 减去 前面那个数字再前面一个数字的和 的差） 就是两个数字之间的所有数字的和，单调数列找一个数字还不简单，二分！  
假设输入序列为：$a_1,a_2,a_3,a_4......a_n$，设函数$sum(x)=\sum_1^x a_i$，则两个数字之间的所有数字的和为$a_{i_0}+a_{i_0+1}......a_{j_0}$=$\sum_1^{j_0}a_i-\sum_1^{i_0}a_i+a_{i_0}$=$\sum_1^{j_0}a_i-\sum_1^{i_0-1}a_i$=$sum(j_0)-sum(i_0-1)$  

## 代码
```c++
#include <cstdio>
#include <iostream>
#include <map>
#include <vector>
using namespace std;
struct ans {
    int i, j;
    int value = 9999999;
};
vector<int> sum;
vector<ans> answer;
ans mini;
vector<ans> mins;
int N, S, t;
int FindS(int i, int &the_sum){
    int beg = i, end = N, index = i;
    while(beg < end){
        
        index = (end + beg)/2;
        if(sum[index]-sum[i-1] == S){
            the_sum = S;
            return index;
        }else if(sum[index]-sum[i-1] > S){
            end = index;
        }else{
            beg = index + 1;
        }
    }
    the_sum = sum[index]-sum[i-1];
    return index;
}

int main() {
    
    cin >> N >> S;

    int t_sum = 0;
    sum.push_back(0);
    for (int ind = 0; ind < N; ind++) {
        scanf("%d", &t);
        t_sum += t;
        sum.push_back(t_sum);
    }

    for (int i = 1; i <= N; i++) {
        int the_sum, j;
        j = FindS(i, the_sum);
        if(the_sum == S){
            answer.push_back({i,j});
        }else if(the_sum > S){
            if(mini.value  > the_sum){
                mins.clear();
                mini.value = the_sum;
                mins.push_back({i,j});
            }else if(mini.value == the_sum){
                mins.push_back({i,j});
            }
        }
    }

    if (answer.size() != 0) {
        for (int i = 0; i < answer.size(); i++) {
            printf("%d-%d\n", answer[i].i, answer[i].j);
        }
    } else {
        for (int i = 0; i < mins.size(); i++) {
            printf("%d-%d\n", mins[i].i, mins[i].j);
        }
    }
    return 0;
}
```