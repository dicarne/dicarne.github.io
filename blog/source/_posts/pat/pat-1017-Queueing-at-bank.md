---
title: pat - 1017 Queueing at bank
date: 2019-07-22 21:43:32
tags: 
category:
    - 计算机
    - 数据结构
    - pat
---

## 1017 Queueing at Bank (25 分)
Suppose a bank has K windows open for service. There is a yellow line in front of the windows which devides the waiting area into two parts. All the customers have to wait in line behind the yellow line, until it is his/her turn to be served and there is a window available. It is assumed that no window can be occupied by a single customer for more than 1 hour.

Now given the arriving time T and the processing time P of each customer, you are supposed to tell the average waiting time of all the customers.
<!--more-->

### Input Specification:
Each input file contains one test case. For each case, the first line contains 2 numbers: N (≤10
​4
​​ ) - the total number of customers, and K (≤100) - the number of windows. Then N lines follow, each contains 2 times: HH:MM:SS - the arriving time, and P - the processing time in minutes of a customer. Here HH is in the range [00, 23], MM and SS are both in [00, 59]. It is assumed that no two customers arrives at the same time.

Notice that the bank opens from 08:00 to 17:00. Anyone arrives early will have to wait in line till 08:00, and anyone comes too late (at or after 17:00:01) will not be served nor counted into the average.

### Output Specification:
For each test case, print in one line the average waiting time of all the customers, in minutes and accurate up to 1 decimal place.

#### Sample Input:
7 3
07:55:00 16
17:00:01 2
07:59:59 15
08:01:00 60
08:00:00 30
08:00:02 2
08:03:00 10

#### Sample Output:
8.2

## 这道题干啥呢
依旧是模拟银行排队，要求输出等待平均时间。

## 坑点
只要在截止时间之前到达就能被服务，和之前那道题排到了再判断时间不太一样。

## 算法
搞一个数组记录每个窗口能接待下一个顾客的时间。
那么对于一个新顾客来说，它的到达时间是T0，它找到当前所有窗口中能最早提供服务的窗口（记录时间为T1）。

· 若T0<=T1，说明到达的时候还没有窗口能服务，则需要等待T1-T0这么长的时间

· 若T0>T1，说明到达的时候可以立即被服务，不需要等待，那么更新这个窗口下个可服务时间为T0+处理时间P。

## 代码
```c++
#include <iostream>
#include <algorithm>
#include <vector>
#include <stdio.h>
#include <iomanip>
using namespace std;
struct cus
{
    int arrived_time;
    int process_time;
};

int main()
{
    int N, K;
    cin >> N >> K;
    vector<cus> line;
    int h, m, s, p;
    // 输入
    for (int i = 0; i < N; i++)
    {
        cus c;
        scanf("%d:%d:%d", &h, &m, &s);
        c.arrived_time = s + m * 60 + h * 3600;
        cin >> p;
        c.process_time = p * 60;
        if (c.arrived_time <= 17 * 3600)
            line.push_back(c);
    }
    // 根据到达顺序排序
    sort(line.begin(), line.end(), [](cus l, cus r) -> bool { return l.arrived_time < r.arrived_time; });
    N = line.size();
    vector<int> windows(K, 8 * 3600);
    double wait = 0;
    for (int cn = 0; cn < N; cn++)
    {
        cus &cur = line[cn];
        int win_no = min_element(windows.begin(), windows.end()) - windows.begin();
        if (windows[win_no] >= cur.arrived_time)
        {
            wait += -cur.arrived_time + windows[win_no];
            windows[win_no] += cur.process_time;
        }
        else
        {
            windows[win_no] = cur.arrived_time + cur.process_time;
        }
    }
    if (N == 0)
        cout << fixed << setprecision(1) << 0.0;
    else
        cout << fixed << setprecision(1) << wait / (double)N / 60.0;
    return 0;
}
```