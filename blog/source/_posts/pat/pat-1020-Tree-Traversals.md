---
title: pat - 1020 Tree Traversals
date: 2019-07-30 19:18:07
tags:
    - 二叉树
category:
    - 计算机
    - 数据结构
    - pat
---
## 1020 Tree Traversals (25 分)
Suppose that all the keys in a binary tree are distinct positive integers. Given the postorder and inorder traversal sequences, you are supposed to output the level order traversal sequence of the corresponding binary tree.
<!--more-->
### Input Specification:
Each input file contains one test case. For each case, the first line gives a positive integer N (≤30), the total number of nodes in the binary tree. The second line gives the postorder sequence and the third line gives the inorder sequence. All the numbers in a line are separated by a space.

### Output Specification:
For each test case, print in one line the level order traversal sequence of the corresponding binary tree. All the numbers in a line must be separated by exactly one space, and there must be no extra space at the end of the line.

#### Sample Input:
7
2 3 1 5 7 6 4
1 2 3 4 5 6 7

#### Sample Output:
4 1 6 3 5 7 2

## 这道题干啥呢
两行数字分别是二叉树后序和中序遍历的结果，要求你输出这个二叉树层次遍历（BFS）的结果。

## 算法
很久没写二叉树的代码了思路混乱写了一大堆，然后看到了一个看起来[很萌的小姐姐的博客](https://www.liuchuo.net/archives/2100)，写的好简单，比其他人写了一大堆相比真是赏心悦目！学习学习！
主要为利用中序和后序的结果，进行前序遍历，记录这个节点在满二叉树中的位置。最后把结果利用这个位置排序，直接就是层次遍历的结果啦，不需要建树，也不需要BFS，很简单。

## 代码
```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
vector<int> in, post;
struct node
{
    int value, index;
};
vector<node> ans;

void pre(int root, int start, int end, int index)
{
    if (start > end)
        return;
    // i用来找前序in中与post[root]相等的值
    int i = start;
    while (i < end && in[i] != post[root])
        i++;
    // 现在找到了根节点便输出，再在左右子树中递归
    ans.push_back({post[root], index});
    index++;
    // 左子树，左子树的根节点=当前根节点减去-右子树节点数量-1（当前根节点），
    // index为在满二叉树中的位置，用来排序，并不一定连续
    pre(root - (end - i + 1), start, i - 1, index * 2 + 1);
    // 右子树
    pre(root - 1, i + 1, end, index * 2 + 2);
}

int main()
{
    int n, t;
    cin >> n;
    for (int i = 0; i < n; i++)
    {
        cin >> t;
        post.push_back(t);
    }
    for (int i = 0; i < n; i++)
    {
        cin >> t;
        in.push_back(t);
    }
    pre(n - 1, 0, n - 1, 0);
    sort(ans.begin(), ans.end(), [](node l, node r) { return l.index < r.index; });
    cout << ans[0].value;
    for (int i = 1; i < n; i++)
    {
        cout << " " << ans[i].value;
    }
    return 0;
}
```