---
title: pat - 1017 Queueing at bank
date: 2019-07-22 21:43:32
tags: pat
---
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