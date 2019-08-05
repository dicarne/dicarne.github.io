---
title: pat - 1025 PAT Ranking
date: 2019-08-02 20:11:55
tags:
category:
    - 计算机
    - 数据结构
    - pat
---
## 1025 PAT Ranking (25 分)
Programming Ability Test (PAT) is organized by the College of Computer Science and Technology of Zhejiang University. Each test is supposed to run simultaneously in several places, and the ranklists will be merged immediately after the test. Now it is your job to write a program to correctly merge all the ranklists and generate the final rank.
<!--more-->
### Input Specification:
Each input file contains one test case. For each case, the first line contains a positive number N (≤100), the number of test locations. Then N ranklists follow, each starts with a line containing a positive integer K (≤300), the number of testees, and then K lines containing the registration number (a 13-digit number) and the total score of each testee. All the numbers in a line are separated by a space.

### Output Specification:
For each test case, first print in one line the total number of testees. Then print the final ranklist in the following format:

registration_number final_rank location_number local_rank
The locations are numbered from 1 to N. The output must be sorted in nondecreasing order of the final ranks. The testees with the same score must have the same rank, and the output must be sorted in nondecreasing order of their registration numbers.

#### Sample Input:
```
2
5
1234567890001 95
1234567890005 100
1234567890003 95
1234567890002 77
1234567890004 85
4
1234567890013 65
1234567890011 25
1234567890014 100
1234567890012 85
```

#### Sample Output:
```
9
1234567890005 1 1 1
1234567890014 1 2 1
1234567890001 3 1 2
1234567890003 3 1 2
1234567890004 5 1 4
1234567890012 5 2 2
1234567890002 7 1 5
1234567890013 8 2 3
1234567890011 9 2 4
```

## 这道题干啥呢
给你几组成绩。分别输出他在组内的排名、在所有人中的排名、所在组别（当然还有他的id）

## 坑点
没有什么吧，硬要说的话，数字id前面要用0填充到13位，不然最后一组数据会错。

## 代码
```c++
#include <algorithm>
#include <cstdio>
#include <iostream>
#include <vector>
using namespace std;
struct testee {
    long long int id;
    int score, pos, final_rank, local_rank;
};
struct Rank {
    int last_score, rank_now, same_rank, pos;
};
void check_rank(Rank &finalr, vector<testee>::iterator it) {
    if (finalr.last_score < 0) {
        finalr.last_score = it->score;
        finalr.rank_now = 1;
        finalr.same_rank = 0;
    } else {
        if (finalr.last_score == it->score) {
            finalr.same_rank++;
        } else {
            finalr.rank_now += finalr.same_rank + 1;
            finalr.same_rank = 0;
            finalr.last_score = it->score;
        }
    }
    if (finalr.pos == 0)
        it->final_rank = finalr.rank_now;
    else
        it->local_rank = finalr.rank_now;
}
int main() {
    int n, k;
    vector<testee> list;
    cin >> n;
    vector<Rank> posrank;
    // 用第0个位置保存总排名
    posrank.push_back({-1, 0, 0, 0});
    for (int pos = 0; pos < n; pos++) {
        cin >> k;
        for (int l = 0; l < k; l++) {
            testee test;
            cin >> test.id;
            cin >> test.score;
            test.pos = pos + 1;
            list.push_back(test);
        }
        posrank.push_back({-1, 0, 0, pos + 1});
    }
    sort(list.begin(), list.end(), [](testee l, testee r) {
        if (l.score == r.score) {
            return l.id <= r.id;
        } else {
            return l.score > r.score;
        }
    });
    cout << list.size() << endl;
    for (auto it = list.begin(); it != list.end(); it++) {
        check_rank(posrank[0], it);
        check_rank(posrank[it->pos], it);
        printf("%013lld %d %d %d\n", it->id, it->final_rank, it->pos,
               it->local_rank);
    }
    return 0;
}
```
看了看我写的还真是长啊，想了想，计算排名的地方有些复杂了点。我使用了一个结构数组保存每个组的排名状态，然而看了柳神的，现在想想如果每一组分别排名的话，循环变量就是当前名次，如果和上一个人相等，那么名次不变，完全没有必要保存名次、保存成绩。
可惜看了看我的代码，我是建立在所有人的成绩保存为一个列表以后再进行成绩计算，并不能简单的如此更改。

## 学习学习涨涨姿势
[原文引用](https://www.liuchuo.net/archives/2180)
``` c++
#include <cstdio>
#include <algorithm>
#include <vector>
using namespace std;
struct student {
    long long int no;
    int score, finrank, loca, locarank;
};
bool cmp1(student a, student b) {
    return a.score != b.score ? a.score > b.score : a.no < b.no;
}
int main() {
    int n, m;
    scanf("%d", &n);
    vector<student> fin;
    for(int i = 1; i <= n; i++) {
        scanf("%d", &m);
        vector<student> v(m);
        for(int j = 0; j < m; j++) {
            scanf("%lld %d", &v[j].no, &v[j].score);
            v[j].loca = i;
        }
        sort(v.begin(), v.end(), cmp1);
        v[0].locarank = 1;
        fin.push_back(v[0]);
        for(int j = 1; j < m; j++) {
            v[j].locarank = (v[j].score == v[j - 1].score) ? (v[j - 1].locarank) : (j + 1);
            fin.push_back(v[j]);
        }
    }
    sort(fin.begin(), fin.end(), cmp1);
    fin[0].finrank = 1;
    for(int j = 1; j < fin.size(); j++)
        fin[j].finrank = (fin[j].score == fin[j - 1].score) ? (fin[j - 1].finrank) : (j + 1);
    printf("%d\n", fin.size());
    for(int i = 0; i < fin.size(); i++)
        printf("%013lld %d %d %d\n", fin[i].no, fin[i].finrank, fin[i].loca, fin[i].locarank);
    return 0;
}
```