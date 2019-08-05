---
title: pat - 1028 List Sorting
date: 2019-08-03 14:16:18
tags:
category:
    - 计算机
    - 数据结构
    - pat
---
## 1028 List Sorting (25 分)
Excel can sort records according to any column. Now you are supposed to imitate this function.
<!--more-->
### Input Specification:
Each input file contains one test case. For each case, the first line contains two integers N (≤10
​5
​​ ) and C, where N is the number of records and C is the column that you are supposed to sort the records with. Then N lines follow, each contains a record of a student. A student's record consists of his or her distinct ID (a 6-digit number), name (a string with no more than 8 characters without space), and grade (an integer between 0 and 100, inclusive).

### Output Specification:
For each test case, output the sorting result in N lines. That is, if C = 1 then the records must be sorted in increasing order according to ID's; if C = 2 then the records must be sorted in non-decreasing order according to names; and if C = 3 then the records must be sorted in non-decreasing order according to grades. If there are several students who have the same name or grade, they must be sorted according to their ID's in increasing order.

#### Sample Input 1:
```
3 1
000007 James 85
000010 Amy 90
000001 Zoe 60
```

#### Sample Output 1:
```
000001 Zoe 60
000007 James 85
000010 Amy 90
```

#### Sample Input 2:
```
4 2
000007 James 85
000010 Amy 90
000001 Zoe 60
000002 James 98
```

#### Sample Output 2:
```
000010 Amy 90
000002 James 98
000007 James 85
000001 Zoe 60
```

#### Sample Input 3:
```
4 3
000007 James 85
000010 Amy 90
000001 Zoe 60
000002 James 90
```

#### Sample Output 3:
```
000001 Zoe 60
000007 James 85
000002 James 90
000010 Amy 90
```

## 坑点
如果使用cin和cout最后一个用例会超时，改用scanf和printf吧！

## 代码
给排序函数传入lambda函数可简化编写。
```c++
#include <algorithm>
#include <cstdio>
#include <iostream>
#include <string>
#include <vector>
using namespace std;
struct record {
    int id, grade;
    string name;
};
int main() {
    int N, C;
    cin >> N >> C;
    vector<record> list;
    char buffer[100];
    for (int i = 0; i < N; i++) {
        record r;
        scanf("%d %s %d", &r.id, buffer, &r.grade);
        r.name = string(buffer);
        list.push_back(r);
    }
    if (C == 1) {
        sort(list.begin(), list.end(),
             [](record l, record r) { return l.id < r.id; });
    } else if (C == 2) {
        sort(list.begin(), list.end(), [](record l, record r) {
            if (l.name == r.name) return l.id < r.id;
            else return l.name < r.name;
        });
    } else {
        sort(list.begin(), list.end(), [](record l, record r) {
            if (l.grade == r.grade) return l.id < r.id;
            else return l.grade < r.grade;
        });
    }
    for (int i = 0; i < N; i++) {
        printf("%06d %s %d\n", list[i].id, list[i].name.c_str(), list[i].grade);
    }
    return 0;
}
```