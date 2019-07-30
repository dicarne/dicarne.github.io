---
title: pat - 1014 Waiting in Line
date: 2019-07-18 21:27:32
tags:
    - pat
---
## 1014 Waiting in Line (30 分)
Suppose a bank has N windows open for service. There is a yellow line in front of the windows which devides the waiting area into two parts. The rules for the customers to wait in line are:

The space inside the yellow line in front of each window is enough to contain a line with M customers. Hence when all the N lines are full, all the customers after (and including) the (NM+1)st one will have to wait in a line behind the yellow line.
Each customer will choose the shortest line to wait in when crossing the yellow line. If there are two or more lines with the same length, the customer will always choose the window with the smallest number.
Customer_i will take Ti minutes to have his/her transaction processed.
The first N customers are assumed to be served at 8:00am.
Now given the processing time of each customer, you are supposed to tell the exact time at which a customer has his/her business done.

For example, suppose that a bank has 2 windows and each window may have 2 custmers waiting inside the yellow line. There are 5 customers waiting with transactions taking 1, 2, 6, 4 and 3 minutes, respectively. At 08:00 in the morning, customer1 is served at window​1 while customer2 is served at window2. Customer3 will wait in front of window1 and customer4 will wait in front of window2. Customer​5 will wait behind the yellow line.

At 08:01, customer1 is done and customer5 enters the line in front of window​1 since that line seems shorter now. Customer2 will leave at 08:02, customer4 at 08:06, customer3 at 08:07, and finally customer5 at 08:10.
<!--more-->
### Input Specification:
Each input file contains one test case. Each case starts with a line containing 4 positive integers: N (≤20, number of windows), M (≤10, the maximum capacity of each line inside the yellow line), K (≤1000, number of customers), and Q (≤1000, number of customer queries).

The next line contains K positive integers, which are the processing time of the K customers.

The last line contains Q positive integers, which represent the customers who are asking about the time they can have their transactions done. The customers are numbered from 1 to K.

### Output Specification:
For each of the Q customers, print in one line the time at which his/her transaction is finished, in the format HH:MM where HH is in [08, 17] and MM is in [00, 59]. Note that since the bank is closed everyday after 17:00, for those customers who cannot be served before 17:00, you must output Sorry instead.

#### Sample Input:
2 2 7 5
1 2 6 4 3 534 2
3 4 5 6 7

#### Sample Output:
08:07
08:06
08:10
17:00
Sorry

## 这道题干啥呢
就是模拟银行前面的窗口，顾客可以排队等着，每个窗口一个个处理客户，然后客户问你几点能做完= =。但是银行17:00就下班了，后面到达的顾客就不处理了，但是就算前面处理着的顾客超时了依然会兢兢业业地干活而不是提早下班。

## 坑点
1. 输出Sorry是根据顾客的开始处理时间而不是处理完毕时间。
2. 选择的不是最短时间队列而是最短队列。

模拟有很多种手法，看到有人一分钟处理一次的，我是处理当前所需最短时间顾客，然后以这个时间模拟一次。
因此处理过程分为三部分，1.填充满窗口前的队列，2.处理完一个顾客补充一个顾客，3.处理剩下的所有顾客。
1. 选一个最短的队伍把新顾客加进去。
2. 每次弹出一个顾客，进入一个顾客，耗时`t`，然后其他所有队列头的顾客所需时间扣除`t`。弹出顾客的耗费总时间就是`当前时间+t`，这个t和输入给的时间不一样，这个会根据处理进程减小。`当前时间+=t`。
3. 接下来没有新顾客了，对于每个队列中的顾客，所需要的总时间就是`当前顾客剩余时间+上一个顾客耗费时间+当前时间`，因为当前时间是几个队列公用的，在这里分别处理各个队列的时候就不便修改当前时间了，于是当前时间便恒定为开始处理剩余顾客的时间。相应的，弹出一个顾客的时候要将他剩余时间加到下一个顾客的剩余时间上面去。

## 代码
```c++
#include <iostream>
#include <vector>
#include <queue>
#include <iomanip>
using namespace std;
struct user
{
    int id;
    int used_time;
    int cost_time = 0;
};

int main()
{
    int N, M, K, Q;
    cin >> N >> M >> K >> Q;
    int cm;
    vector<int> mins;
    for (int i = 0; i < K; i++)
    {
        cin >> cm;
        mins.push_back(cm);
    }
    vector<queue<user>> que;
    vector<int> prepare;
    int current_time = 0;
    for (int i = 0; i < N; i++)
    {
        queue<user> q;
        que.push_back(q);
        prepare.push_back(0);
    }

    // 初始化，试图将队列用顾客填满
    for (int i = 0; i < N * M; i++)
    {
        if (i >= K)
            break;
        // 找到一个人最少且不空的队列
        int min = 0;
        for (int j = 0; j < N; j++)
        {
            if (que[j].size() < que[min].size() && que[j].size() < M)
            {
                min = j;
            }
        }

        user u;
        u.id = i + 1;
        u.used_time = mins[i];
        que[min].push(u);
    }

    // 将剩余顾客依次加入队列 —— 同时完成一个所需时间最短的顾客的交易
    vector<user> result;
    for (int i = N * M; i < K; i++)
    {
        // 找到第一个顾客所需时间最短的队列
        int min = 0;
        int mint = INT32_MAX;
        for (int f = 0; f < N; f++)
        {
            int ut = 0;
            if (que[f].size() > 0)
                if (que[f].front().used_time < mint)
                {
                    min = f;
                    mint = que[f].front().used_time;
                }
        }

        // 将所有队列头扣除最短顾客所需时间
        auto popuser = que[min].front();
        current_time += popuser.used_time;
        for (int k = 0; k < N; k++)
        {
            if (que[k].size() > 0)
                que[k].front().used_time -= popuser.used_time;
        }
        popuser.cost_time = current_time;
        que[min].pop();
        result.push_back(popuser);

        // 查找最短队列供新顾客加入
        min = 0;
        for (int j = 0; j < N; j++)
        {
            if (que[j].size() < que[min].size() && que[j].size() < M)
            {
                min = j;
            }
        }
        user u;
        u.id = i + 1;
        u.used_time = mins[i];
        que[min].push(u);
    }

    // 计算所有队列中剩余顾客
    for (int i = 0; i < N; i++)
    {
        while (!que[i].empty())
        {
            auto popu = que[i].front();
            que[i].pop();
            popu.cost_time = popu.used_time + current_time;
            if (que[i].size() > 0)
            {
                // 下一个所需时间 = 自己所需时间 + 上一个耗费时间
                que[i].front().used_time += popu.used_time;
            }
            result.push_back(popu);
        }
    }
    // 查询
    int totalTime = (17 - 8) * 60;
    int cus_num;
    int len = result.size();
    for (int i = 0; i < Q; i++)
    {
        cin >> cus_num;
        for (int j = 0; j < len; j++)
        {
            if (result[j].id == cus_num)
            {

                if (result[j].cost_time - mins[result[j].id - 1] >= totalTime)
                {
                    cout << "Sorry" << endl;
                }
                else
                {
                    cout << setw(2) << setfill('0') << result[j].cost_time / 60 + 8 << ":" << setw(2) << setfill('0') << result[j].cost_time % 60 << endl;
                }
            }
        }
    }

    return 0;
}
```