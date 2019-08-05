---
title: pat - 1033 To Fill or Not to Fill (25 分)
date: 2019-08-05 22:46:49
tags:
category:
    - 计算机
    - 数据结构
    - pat
---
## 1033 To Fill or Not to Fill (25 分)
With highways available, driving a car from Hangzhou to any other city is easy. But since the tank capacity of a car is limited, we have to find gas stations on the way from time to time. Different gas station may give different price. You are asked to carefully design the cheapest route to go.
<!--more-->
### Input Specification:
Each input file contains one test case. For each case, the first line contains 4 positive numbers: C​max (≤ 100), the maximum capacity of the tank; D (≤30000), the distance between Hangzhou and the destination city; D​avg​​  (≤20), the average distance per unit gas that the car can run; and N (≤ 500), the total number of gas stations. Then N lines follow, each contains a pair of non-negative numbers: P​i​​ , the unit gas price, and D​i​​  (≤D), the distance between this station and Hangzhou, for i=1,⋯,N. All the numbers in a line are separated by a space.

### Output Specification:
For each test case, print the cheapest price in a line, accurate up to 2 decimal places. It is assumed that the tank is empty at the beginning. If it is impossible to reach the destination, print The maximum travel distance = X where X is the maximum possible distance the car can run, accurate up to 2 decimal places.

#### Sample Input 1:
```
50 1300 12 8
6.00 1250
7.00 600
7.00 150
7.10 0
7.20 200
7.50 400
7.30 1000
6.85 300
```

#### Sample Output 1:
```
749.17
```

#### Sample Input 2:
```
50 1300 12 2
7.10 0
7.00 600
```

#### Sample Output 2:
```
The maximum travel distance = 1200.00
```

## 这道题干啥呢
有一辆车，从杭州前往目的地，路上有n个加油站，每个加油站价格不一样，问最便宜的油费是多少？（或者实在到不了终点的话，能走多远？）

## 坑点
这道题没什么坑点，要说有的话，因为这辆车一开始邮箱是空的，因此如果没有位置为0的加油站，就动不了啦！  
一开始写的差不多了，结果发现忘记这车能便宜的地方多加点油，结果答案总是不对。于是干脆在注释中详细写出我考虑的几种情况，分别写出，然后就神奇的一遍通过了。

## 代码
```c++
#include <math.h>
#include <algorithm>
#include <cstdio>
#include <iostream>
#include <vector>
using namespace std;
struct gas {
    double price;
    int distance;
};
int prog() {
    double cap, avg, p;
    int dis, N, gd, len = 0;
    cin >> cap >> dis >> avg >> N;
    vector<gas> gas_station;
    for (int i = 0; i < N; i++) {
        cin >> p >> gd;
        gas_station.push_back({p, gd});
    }
    sort(gas_station.begin(), gas_station.end(),
         [](gas l, gas r) { return l.distance < r.distance; });
    if (gas_station[0].distance != 0) {
        printf("The maximum travel distance = 0.00");
        return 0;
    }
    gas_station.push_back({0, dis});
    int totalDis = 0, ind = 0;
    double totalCost = 0, needAdd;

    double remain = 0;
    while (true) {
        double max_dis_now = cap * avg;
        int new_ind = ind + 1;
        int min_ind = ind;
        while (new_ind < gas_station.size() &&
               gas_station[new_ind].distance - gas_station[ind].distance <
                   max_dis_now) {
            if (gas_station[min_ind].price > gas_station[new_ind].price) {
                min_ind = new_ind;
                break;
            }
            new_ind++;
        }
        // 找下一个比现在便宜的加油站
        // - 找到
        //   - 加油加到刚好能到下一个最便宜的站
        // - 没找到（当前范围内最便宜）
        //   - 可以直接到终点
        //   - 到不了任何站
        //   - 可以到别的站，于是到下一站再寻找更便宜的加油站
        if (min_ind > ind) {
            len = gas_station[min_ind].distance - gas_station[ind].distance;
            needAdd = max(len / avg - remain, 0.0);
            totalCost += gas_station[ind].price * needAdd;
            totalDis += len;
            ind = min_ind;
            if (needAdd == 0) {
                remain -= len / avg;
            } else {
                remain = 0;
            }

        } else if (min_ind == ind) {
            // 当前为最便宜的
            if (ind == gas_station.size() - 1) {
                // 到终点了
                printf("%.2lf", totalCost);
                return 0;
            }
            if (gas_station[ind + 1].distance - gas_station[ind].distance >
                cap * avg) {
                // 到不了下一站
                printf("The maximum travel distance = %.2lf",
                       cap * avg + gas_station[ind].distance);
                return 0;
            }
            totalCost += gas_station[ind].price * (cap - remain);
            totalDis +=
                gas_station[ind + 1].distance - gas_station[ind].distance;
            remain = cap - (gas_station[ind + 1].distance -
                            gas_station[ind].distance) /
                               avg;
            ind++;
        }
    }
    return 0;
}
int main() {
    prog();
    return 0;
}
```