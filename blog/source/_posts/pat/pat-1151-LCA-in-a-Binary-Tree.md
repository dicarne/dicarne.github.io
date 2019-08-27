---
title: pat - 1151 LCA in a Binary Tree
date: 2019-08-27 22:14:22
tags:
---
## 1151 LCA in a Binary Tree (30 分)
The lowest common ancestor (LCA) of two nodes U and V in a tree is the deepest node that has both U and V as descendants.

Given any two nodes in a binary tree, you are supposed to find their LCA.
<!--more-->
### Input Specification:
Each input file contains one test case. For each case, the first line gives two positive integers: M (≤ 1,000), the number of pairs of nodes to be tested; and N (≤ 10,000), the number of keys in the binary tree, respectively. In each of the following two lines, N distinct integers are given as the inorder and preorder traversal sequences of the binary tree, respectively. It is guaranteed that the binary tree can be uniquely determined by the input sequences. Then M lines follow, each contains a pair of integer keys U and V. All the keys are in the range of int.

### Output Specification:
For each given pair of U and V, print in a line LCA of U and V is A. if the LCA is found and A is the key. But if A is one of U and V, print X is an ancestor of Y. where X is A and Y is the other node. If U or V is not found in the binary tree, print in a line ERROR: U is not found. or ERROR: V is not found. or ERROR: U and V are not found..

#### Sample Input:
```
6 8
7 2 3 4 6 5 1 8
5 3 7 2 6 4 8 1
2 6
8 1
7 9
12 -3
0 8
99 99
```

#### Sample Output:
```
LCA of 2 and 6 is 3.
8 is an ancestor of 1.
ERROR: 9 is not found.
ERROR: 12 and -3 are not found.
ERROR: 0 is not found.
ERROR: 99 and 99 are not found.
```

## 坑点
一开始用的是比较蠢，比较老实的写法，建立二叉树，查找，看看有没有。用例2过不了，很生气，研究了半天，搞出用例2究竟是什么样的：M=1,N=1,中序：1，前序：1，查询：1 1。这棵树反正只有一个数，就当它是1好了。我建的树没考虑到两个查询项一样的情况，因此会输出错误一个。应该输`1 is an ancestor of 1.`（看起来很不合理，我自己是自己的祖先？我和我弟弟的父辈是我爸爸，但我和我的父辈是我，坑爹！）  

## 代码
很不优美，QaQ  
```c++
#include <cstdio>
#include <vector>
using namespace std;
vector<int> inorder;
vector<int> preorder;
struct node {
    int value;
    node *left = nullptr, *right = nullptr;
};
int findInorder(int n, int beg, int end) {
    for (int i = beg; i <= end; i++) {
        if (inorder[i] == n) {
            return i;
        }
    }
    return -1;
}
int pre_index = 1;
int N, M, t;
void build(node* root, int beg, int end, bool left) {
    if (!(pre_index < N)) return;
    int rp = findInorder(preorder[pre_index], beg, end);
    if (rp != -1) {
        node* n = new node();
        if (left)
            root->left = n;
        else
            root->right = n;
        n->value = preorder[pre_index];
        pre_index++;
        build(n, beg, rp - 1, true);
        build(n, rp + 1, end, false);
    }
}
struct res {
    node* tree = nullptr;
    bool left = false;
    bool right = false;
    bool yes = false;
    bool pass = false;
    bool num1 = false;
    bool num2 = false;
};
res find(node* root, int num1, int num2) {
    res rs;
    // 搜索左右子树
    if (root->value == num1) {
        rs.yes = true;
        rs.num1 = true;
    } else if (root->value == num2) {
        rs.yes = true;
        rs.num2 = true;
    }
    res l, r;
    if (root->left) {
        l = find(root->left, num1, num2);
        if (l.yes) l.left = true;
        if (l.pass) return l;
    }
    if (root->right && !(l.left && r.right)) {
        r = find(root->right, num1, num2);
        if (r.yes) r.right = true;
        if (r.pass) return r;
    }
    rs.left = l.left || l.right;
    rs.right = r.left || r.right;
    rs.num1 = l.num1 || r.num1 || rs.num1;
    rs.num2 = l.num2 || r.num2 || rs.num2;
    if (!rs.pass && rs.num1 && rs.num2) {
        rs.tree = root;
        rs.pass = true;
    }
    return rs;
}

int main() {
    scanf("%d%d", &M, &N);
    for (int i = 0; i < N; i++) {
        scanf("%d", &t);
        inorder.push_back(t);
    }
    for (int i = 0; i < N; i++) {
        scanf("%d", &t);
        preorder.push_back(t);
    }
    node root;
    root.value = preorder[0];
    int rp = findInorder(root.value, 0, N - 1);
    build(&root, 0, rp - 1, true);
    build(&root, rp + 1, N - 1, false);

    int a1, a2;
    for (int i = 0; i < M; i++) {
        scanf("%d%d", &a1, &a2);

        auto res = find(&root, a1, a2);
        // 临时补丁，解决相同数字的情况
        if (a1 == a2 && (res.num1 || res.num2)) {
            printf("%d is an ancestor of %d.\n", a1, a2);
            continue;
        }
        if (res.num1 && res.num2) {
            if (res.tree->value == a1) {
                printf("%d is an ancestor of %d.\n", a1, a2);
            } else if (res.tree->value == a2) {
                printf("%d is an ancestor of %d.\n", a2, a1);
            } else {
                printf("LCA of %d and %d is %d.\n", a1, a2, res.tree->value);
            }
        } else if (res.num1 == true && res.num2 == false) {
            printf("ERROR: %d is not found.\n", a2);
        } else if (res.num2 == true && res.num1 == false) {
            printf("ERROR: %d is not found.\n", a1);
        } else if (res.num1 == false && res.num2 == false) {
            printf("ERROR: %d and %d are not found.\n", a1, a2);
        }
    }
    return 0;
}
```
从[柳神那里学了一招](https://www.liuchuo.net/archives/6496)，不用建树，根据在中序数组中左右情况可以判断在左子树还是右子树。  