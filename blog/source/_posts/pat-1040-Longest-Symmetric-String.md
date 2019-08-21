---
title: pat - 1040 Longest Symmetric String（动态规划）
date: 2019-08-16 20:50:19
tags: 动态规划
category:
    - 计算机
    - 数据结构
    - pat
---
## 1040 Longest Symmetric String (25 分)
Given a string, you are supposed to output the length of the longest symmetric sub-string. For example, given Is PAT&TAP symmetric?, the longest symmetric sub-string is s PAT&TAP s, hence you must output 11.
<!--more-->
### Input Specification:
Each input file contains one test case which gives a non-empty string of length no more than 1000.

### Output Specification:
For each test case, simply print the maximum length in a line.

#### Sample Input:
```
Is PAT&TAP symmetric?
```

#### Sample Output:
```
11
```

## 这道题干啥呢
给定一个字符串，计算这个字符串包含的最长回文子串的长度。

## 代码
一开始我用递归暴力的解，果然几个用例超时了……  
然后研究研究，发现是动态规划问题。  
递推关系：更长的回文串=短一点的回文串&&两边的字符相同。  
利用一个二维数组，储存位置i到位置j是否是回文串。  
初始化：  
1. 单个字母是回文串，因此`dp[i][i]=1`  
2. 左右两个字母相同的时候是回文串，因此`dp[i][i+1]=1`  

这样考虑了回文串奇数长还是偶数长的情况。  
接着遍历长度从L=3~字符串长，看看假设回文串为L长度的时候是否成立。  
取每个从i开始长为L的子串，检查它是否是回文串，根据动态规划的思想，不必真的**一个个字符去对比**这个字符串是否是回文串，而是看这个子串**去头去尾后**是否是字串。*（而因为L是递增遍历的，因此需要判断L去头去尾的L-2是否是子串的时候，L-2必然已经计算过了。）*这就是为什么判断`dp[i+1][i+l-2]?=1`的原因了。然后如果子串去头去尾是回文串，并且字串左右两端相同，那么可以认为这个子串是一个长一点的回文串，记录到`dp[i][i+l-1]=1`  
记录每次最大的字串长度，最后直接输出答案即可。
```c++
#include <iostream>
#include <string>
using namespace std;
int dp[1002][1002];
int main() {
    string str;
    getline(cin, str);
    
    int maxlen = 1;
    int sl = str.length();
    for (int i = 0; i < sl; i++) {
        dp[i][i] = 1;
        if (i + 1 < sl && str.at(i) == str.at(i + 1)) {
            dp[i][i + 1] = 1;
            maxlen = 2;
        }
    }
    for (int l = 3; l <= sl; l++) {
        for (int i = 0; i + l - 1 < sl; i++) {
            // l = 3
            // i i+1 i+2 i+3
            // ?  h   ?
            // 回文串的左右两个相邻字符要相等
            if (dp[i + 1][i + l - 2] == 1 && i + l - 1 < sl &&
                str.at(i) == str.at(i + l - 1)) {
                    maxlen = l;
                    dp[i][i+l-1] = 1;
            }
        }
    }
    cout << maxlen;
    return 0;
}
```

## 总结
这道题复习了动态规划的基本思想。所谓动态规划，根据我直观的理解，就是：若想解决一个问题A，并发现这个问题居然还是由A构成的，**只是它的规模更小，但是解法完全一样**，并且A可以由它的子问题小A的结论直接决定（不需要考虑小A计算的过程），而且这样的答案是最优的，那么这便是一个动态规划问题。  
动态规划可以利用子问题计算的答案，而不必每次重新计算子问题大大提升了效率，重复利用了结论。