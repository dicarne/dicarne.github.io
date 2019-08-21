---
title: pat - 1034 Head of a Gang
date: 2019-08-15 20:07:40
tags:
category:
    - 计算机
    - 数据结构
    - pat
---
## 1034 Head of a Gang (30 分)
One way that the police finds the head of a gang is to check people's phone calls. If there is a phone call between A and B, we say that A and B is related. The weight of a relation is defined to be the total time length of all the phone calls made between the two persons. A "Gang" is a cluster of more than 2 persons who are related to each other with total relation weight being greater than a given threthold K. In each gang, the one with maximum total weight is the head. Now given a list of phone calls, you are supposed to find the gangs and the heads.
<!--more-->
### Input Specification:
Each input file contains one test case. For each case, the first line contains two positive numbers N and K (both less than or equal to 1000), the number of phone calls and the weight threthold, respectively. Then N lines follow, each in the following format:
Name1 Name2 Time
where Name1 and Name2 are the names of people at the two ends of the call, and Time is the length of the call. A name is a string of three capital letters chosen from A-Z. A time length is a positive integer which is no more than 1000 minutes.

### Output Specification:
For each test case, first print in a line the total number of gangs. Then for each gang, print in a line the name of the head and the total number of the members. It is guaranteed that the head is unique for each gang. The output must be sorted according to the alphabetical order of the names of the heads.

#### Sample Input 1:
```
8 59
AAA BBB 10
BBB AAA 20
AAA CCC 40
DDD EEE 5
EEE DDD 70
FFF GGG 30
GGG HHH 20
HHH FFF 10
```

#### Sample Output 1:
```
2
AAA 3
GGG 3
```

#### Sample Input 2:
```
8 70
AAA BBB 10
BBB AAA 20
AAA CCC 40
DDD EEE 5
EEE DDD 70
FFF GGG 30
GGG HHH 20
HHH FFF 10
```

#### Sample Output 2:
```
0
```

## 代码
我的思路比较简单，每个人名指向一个组，这个组储存着所以关联的人，当输入一个新的人名的时候，如果两个人名查到在不同组，就合并这两个组；如果一个有组一个没有，就添加到一个组中；如果两个人都查不到组，就新建一个组。  
实现的有一点点复杂了，感觉不如网上图的解法短。不过原理更直白一点，就是第一感觉……
```c++
#include <algorithm>
#include <iostream>
#include <map>
#include <set>
#include <string>
#include <vector>
using namespace std;

struct gang {
    int id;
    int totalTime = 0;
    map<string, int> members;
    string head;
};
struct deleg{
    gang* g;
};
struct group {
    deleg* d;
};

int main() {
    int N, T;
    cin >> N >> T;
    // 已知所以相互联系的可能是一个gang，所以把相互联系的人放在一堆，再统计总的通信时间。
    int group_id;
    map<string, group*> mem_to_group;
    int index = 0;
    for (int i = 0; i < N; i++) {
        string from, to;
        int time;
        cin >> from >> to >> time;
        auto ff = mem_to_group.find(from);
        auto tt = mem_to_group.find(to);
        if (ff == mem_to_group.end() && tt == mem_to_group.end()) {
            // 不存在这两条记录，新建一个组
            group* tg = new group();
            tg->d = new deleg();
            tg->d->g = new gang();
            tg->d->g->totalTime = time;
            tg->d->g->id = index++;
            tg->d->g->members[from] = time;
            tg->d->g->members[to] = time;
            mem_to_group[from] = tg;
            mem_to_group[to] = tg;
        } else if (ff != mem_to_group.end() && tt == mem_to_group.end()) {
            auto tg = ff->second;
            tg->d->g->totalTime += time;
            tg->d->g->members[from] += time;
            tg->d->g->members[to] = time;
            mem_to_group[to] = tg;
        } else if (ff == mem_to_group.end() && tt != mem_to_group.end()) {
            auto tg = tt->second;
            tg->d->g->totalTime += time;
            tg->d->g->members[from] = time;
            tg->d->g->members[to] += time;
            mem_to_group[from] = tg;
        } else if (ff->second->d->g == tt->second->d->g) {
            ff->second->d->g->totalTime += time;
            ff->second->d->g->members[from] += time;
            ff->second->d->g->members[to] += time;
        } else {
            auto g1 = ff->second->d->g;
            auto g2 = tt->second->d->g;
            g1->totalTime += g2->totalTime;
            for (auto it = g2->members.begin(); it != g2->members.end(); it++) {
                auto to1 = g1->members.find(it->first);
                if (to1 == g1->members.end()) {
                    // 没找到，添加
                    g1->members[it->first] = it->second;
                } else {
                    to1->second += it->second;
                }
            }
            if(g1->members.find(from)!=g1->members.end()){
                g1->members[from]+=time;
            }else{
                g1->members[from] = time;
            }
            if(g1->members.find(to)!=g1->members.end()){
                g1->members[to]+=time;
            }else{
                g1->members[to] = time;
            }
            for(auto it = mem_to_group.begin(); it != mem_to_group.end(); it++){
                if(it->second->d->g == g2){
                    it->second = ff->second;
                }
            }
        }
    }
    // 找到达到阈值的组
    vector<gang*> result;
    set<int> all_group;
    for (auto it = mem_to_group.begin(); it != mem_to_group.end(); it++) {
        if (it->second->d->g->totalTime > T && it->second->d->g->members.size() > 2) {
            if (all_group.find(it->second->d->g->id) == all_group.end()) {
                all_group.insert(it->second->d->g->id);
                auto gt = it->second->d->g;
                string maxn;
                int maxt = -1;
                for (auto ii = gt->members.begin(); ii != gt->members.end();
                     ii++) {
                    if (ii->second > maxt) {
                        maxt = ii->second;
                        maxn = ii->first;
                    }
                }
                gt->head = maxn;
                result.push_back(gt);
            }
        }
    }
    sort(result.begin(), result.end(),
         [](gang* l, gang* r) { return l->head < r->head; });
    cout << result.size() << endl;
    for (int i = 0; i < result.size(); i++) {
        cout << result[i]->head << " " << result[i]->members.size() << endl;
    }
    return 0;
}
```