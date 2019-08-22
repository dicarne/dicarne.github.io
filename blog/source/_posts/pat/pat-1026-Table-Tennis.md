---
title: pat - 1026 Table Tennis
date: 2019-08-03 13:37:54
tags:
category:
    - 计算机
    - 数据结构
    - pat
---
## 1026 Table Tennis (30 分)
A table tennis club has N tables available to the public. The tables are numbered from 1 to N. For any pair of players, if there are some tables open when they arrive, they will be assigned to the available table with the smallest number. If all the tables are occupied, they will have to wait in a queue. It is assumed that every pair of players can play for at most 2 hours.

Your job is to count for everyone in queue their waiting time, and for each table the number of players it has served for the day.
<!--more-->
One thing that makes this procedure a bit complicated is that the club reserves some tables for their VIP members. When a VIP table is open, the first VIP pair in the queue will have the priviledge to take it. However, if there is no VIP in the queue, the next pair of players can take it. On the other hand, if when it is the turn of a VIP pair, yet no VIP table is available, they can be assigned as any ordinary players.

### Input Specification:
Each input file contains one test case. For each case, the first line contains an integer N (≤10000) - the total number of pairs of players. Then N lines follow, each contains 2 times and a VIP tag: HH:MM:SS - the arriving time, P - the playing time in minutes of a pair of players, and tag - which is 1 if they hold a VIP card, or 0 if not. It is guaranteed that the arriving time is between 08:00:00 and 21:00:00 while the club is open. It is assumed that no two customers arrives at the same time. Following the players' info, there are 2 positive integers: K (≤100) - the number of tables, and M (< K) - the number of VIP tables. The last line contains M table numbers.

### Output Specification:
For each test case, first print the arriving time, serving time and the waiting time for each pair of players in the format shown by the sample. Then print in a line the number of players served by each table. Notice that the output must be listed in chronological order of the serving time. The waiting time must be rounded up to an integer minute(s). If one cannot get a table before the closing time, their information must NOT be printed.

#### Sample Input:
```
9
20:52:00 10 0
08:00:00 20 0
08:02:00 30 0
20:51:00 10 0
08:10:00 5 0
08:12:00 10 1
20:50:00 10 0
08:01:30 15 1
20:53:00 10 1
3 1
2
```
#### Sample Output:
```
08:00:00 08:00:00 0
08:01:30 08:01:30 0
08:02:00 08:02:00 0
08:12:00 08:16:30 5
08:10:00 08:20:00 10
20:50:00 20:50:00 0
20:51:00 20:51:00 0
20:52:00 20:52:00 0
3 3 2
```

## 这道题干啥呢
有一些网球场，你需要为顾客分配场地，和前面的银行问题比较相似，唯一不同的是这里还会有一些VIP场地和VIP客户，你需要考虑为他们优先分配的问题。

## 坑点
这道题提交了大部分对却错了一个，挠头。研究了半天，居然在网上找到一个博客描述了我错的第4点，这才发现，原来我漏看了第一段的` It is assumed that every pair of players can play for at most 2 hours.`**每对用户不能超过两小时。**

因为这句话是在第一段没有仔细看，一般规定都会在后面详细的说，没想到这次不一样，是我太不注意了，给自己一个差评。

还有，输出时间是**四舍五入**而不是简单取整。

## 思路
本来想参照银行那样到一个顾客算一次，但是要考虑到等待过程中是否会出现VIP顾客和VIP场地，没有第一时间理清楚关系。因此本着简单的原则，我就每一秒模拟一次，处理这一秒的等待队列，就很方便。

对于每一秒，

1. 首先把此刻到达的用户加入队尾

2. 再看看队列中有没有VIP用户和场地，如果既有VIP场地也有VIP用户，就分配VIP用户到VIP场地。

3. 那么剩下的人，要么因为不是VIP不能优先分配，要么虽然是VIP但是没有VIP场地只能和大家一样。所以接下来就依次把队列中的人安排到所有场地中。（*注意是所有场地，包括VIP场地。当队列中有VIP用户的时候必然不存空的VIP场地；当没有VIP用户时却可能存在VIP场地，不需要分开讨论。*）

4. 当时间到了，就不再分配了。输出即可。

## 代码
看了看[柳婼](https://www.liuchuo.net/archives/2955)的代码，果然比我短不少，不过大致扫了一眼没看懂，看她的分析也有好多分类讨论。我的思路就更加简单直白一点，先分配VIP，没有位置再分配剩下的顾客。没有任何思维负担和难度。

但是我的代码缺点在于多了很多很多次无用的循环，影响效率。
```c++
#include <math.h>
#include <algorithm>
#include <cstdio>
#include <iostream>
#include <queue>
#include <vector>
using namespace std;
struct player {
    int arrived_time, play_time, serve_time, wait_time = -1, id;
    bool VIP;
};
struct table {
    bool VIP;
    int free_time, served = 0;
};
vector<player> playerlist;
vector<table> tables;
vector<player> que;

int findVipTable(int time) {
    for (int i = 0; i < tables.size(); i++) {
        if (tables[i].VIP && tables[i].free_time <= time) {
            return i;
        }
    }
    return -1;
}

int findAllTable(int time) {
    for (int i = 0; i < tables.size(); i++) {
        if (tables[i].free_time <= time) {
            return i;
        }
    }
    return -1;
}

int hasVipInQue() {
    for (int i = 0; i < que.size(); i++) {
        if (que[i].VIP) return i;
    }
    return -1;
}

int main() {
    int n, K, M, hh, mm, ss, vv, Mo;
    cin >> n;
    for (int i = 0; i < n; i++) {
        player p;
        scanf("%d:%d:%d", &hh, &mm, &ss);
        p.arrived_time = hh * 3600 + mm * 60 + ss;
        cin >> p.play_time;
        // 游玩时间不得超过2小时
        if (p.play_time > 120) p.play_time = 120;
        p.play_time *= 60;
        cin >> vv;
        if (vv == 0)
            p.VIP = false;
        else
            p.VIP = true;
        playerlist.push_back(p);
    }
    sort(playerlist.begin(), playerlist.end(),
         [](player l, player r) { return l.arrived_time < r.arrived_time; });
    cin >> K >> M;

    for (int i = 0; i < K; i++) {
        tables.push_back({false, 8 * 3600});
    }
    for (int i = 0; i < M; i++) {
        cin >> Mo;
        tables[Mo - 1].VIP = true;
    }
    int index = 0;

    // 加入所有提前到的人
    while (index < playerlist.size() &&
           playerlist[index].arrived_time < 8 * 3600) {
        playerlist[index].id = index;
        que.push_back(playerlist[index]);
        index++;
    }
    // 模拟到达
    for (int time = 8 * 3600; time < 21 * 3600; time++) {
        if (index < playerlist.size() &&
            playerlist[index].arrived_time == time) {
            playerlist[index].id = index;
            que.push_back(playerlist[index]);
            index++;
        }
        int tableid, vipno;
        player front;

        // 处理VIP
        while ((vipno = hasVipInQue()) >= 0 &&
               (tableid = findVipTable(time)) >= 0) {
            tables[tableid].free_time = time + que[vipno].play_time;
            tables[tableid].served++;
            playerlist[que[vipno].id].wait_time =
                time - playerlist[que[vipno].id].arrived_time;
            que.erase(que.begin() + vipno);
        }
        // 处理剩下队列中的人（找不到VIP桌的VIP用户和普通人）
        while (!que.empty() && (tableid = findAllTable(time)) >= 0) {
            tables[tableid].free_time = time + que[0].play_time;
            playerlist[que[0].id].wait_time =
                time - playerlist[que[0].id].arrived_time;
            tables[tableid].served++;
            que.erase(que.begin());
        }
    }
    // 根据服务时间排序
    sort(playerlist.begin(), playerlist.end(), [](player l, player r) {
        if (l.arrived_time < 0)
            return false;
        else if (r.arrived_time < 0)
            return true;
        else
            return l.arrived_time + l.wait_time < r.arrived_time + r.wait_time;
    });

    for (int i = 0; i < playerlist.size(); i++) {
        if (playerlist[i].wait_time >= 0) {
            const player& p = playerlist[i];
            int min = p.wait_time / 60.0;
            if (p.wait_time % 60 >= 30) min++;
            int tt;

            hh = p.arrived_time / 3600;
            mm = (p.arrived_time - hh * 3600) / 60;
            tt = p.arrived_time - hh * 3600 - mm * 60;
            printf("%02d:%02d:%02d ", hh, mm, tt);
            hh = (p.arrived_time + p.wait_time) / 3600,
            mm = ((p.arrived_time + p.wait_time) - hh * 3600) / 60,
            tt = (p.arrived_time + p.wait_time) - hh * 3600 - mm * 60;
            printf("%02d:%02d:%02d %d\n", hh, mm, tt, min);
        }
    }

    for (int i = 0; i < tables.size(); i++) {
        if (i == 0)
            cout << tables[i].served;
        else
            cout << " " << tables[i].served;
    } 
    return 0;
}
```