---
title: pta - 1010 Radix
date: 2019-07-17 22:20:19
tags: 
    - pta
---
## 1010 Radix (25 分)
Given a pair of positive integers, for example, 6 and 110, can this equation 6 = 110 be true? The answer is yes, if 6 is a decimal number and 110 is a binary number.

Now for any pair of positive integers N1 and N​2, your task is to find the radix of one number while that of the other is given.

### Input Specification:
Each input file contains one test case. Each case occupies a line which contains 4 positive integers:
N1 N2 tag radix

Here N1 and N2 each has no more than 10 digits. A digit is less than its radix and is chosen from the set { 0-9, a-z } where 0-9 represent the decimal numbers 0-9, and a-z represent the decimal numbers 10-35. The last number radix is the radix of N1 if tag is 1, or of N2 if tag is 2.

### Output Specification:
For each test case, print in one line the radix of the other number so that the equation N1 = N2 is true. If the equation is impossible, print Impossible. If the solution is not unique, output the smallest possible radix.

>Sample Input 1:
>6 110 1 10
>Sample Output 1:
>2
>Sample Input 2:
>1 ab 1 2
>Sample Output 2:
>Impossible

## 这道题干啥呢
给两个数，告诉你其中一个数的进制，然后问你另一个数是几进制的时候和另一个相等？

## 坑点
1. 计算进制转换的数的时候会溢出 - 即使用`long long int`也不够。我是令溢出的数等于最大值（因为之前的代码不好改），如果从头开始写，可以在转换过程中与目标数比较，如果超过则直接返回过大，而不必完整计算下去。
2. 直接遍历范围内的基数会超时 - 用二分法查找，基数过小导致转换出来的数太小，基数过大导致转换出来的数太大。
3. 基数搜索范围应该从`max(2, 另一个数中每一位所代表的数的最大值+1)`到`指定数的最大十进制值`。这个范围可能很大，相关的数需要用`long long int`表示。

## 代码
``` c++
#include <iostream>
#include <string>
using namespace std;

typedef long long int lint;

// 计算整数pow
lint pow_int(lint a, lint n)
{
    lint sum = 1;
    for (int i = 0; i < n; i++)
    {
        sum *= a;
    }
    return sum;
}

// 字符转换为数字
int CharToInt(char c)
{
    if (c >= '0' && c <= '9')
        return c - '0';
    return c - 'a' + 10;
}

// 转换为十进制，若溢出，则默认为long long max
lint ConvertToDynamic(string number, lint radix)
{
    int len = number.size();
    lint sum = 0;
    for (int i = 0; i < len; i++)
    {
        int cur = CharToInt(number[i]);
        sum += cur * pow_int(radix, len - 1 - i);
    }
    if (sum < 0)
        sum = 9223372036854775807;
    return sum;
}

// 找最大数值
int FindMinRadix(string number)
{
    int max = 0;
    int len = number.size();
    for (int i = 0; i < len; i++)
    {
        int cur = CharToInt(number[i]);
        if (cur > max)
            max = cur;
    }
    return max + 1;
}

// 二分查找符合条件的radix
lint CheckUnderRadix(lint target, lint minRadix, lint maxRadix, string number)
{
    lint len = number.size();
    while (minRadix < maxRadix)
    {
        lint k = (minRadix + maxRadix) / 2;
        lint newnum = ConvertToDynamic(number, k);
        if (newnum == target)
            return k;
        if (newnum < target)
        {
            if (k - minRadix != 0)
                minRadix = k;
            else
                minRadix++;
        }
        else
            maxRadix = k;
    }
    lint newnum = ConvertToDynamic(number, maxRadix);
    if (newnum == target)
        return maxRadix;
    return -1;
}

int main()
{
    string snum1, snum2;
    int tag, radix;
    cin >> snum1 >> snum2 >> tag >> radix;

    if (tag == 2)
    {
        string t = snum1;
        snum1 = snum2;
        snum2 = t;
    }
    lint num1 = ConvertToDynamic(snum1, radix);
    int minRadix = FindMinRadix(snum2);
    if (minRadix < 2)
        minRadix = 2;
    lint tnum = num1 + 1;
    if (tnum < 2)
        tnum = 2;
    lint maxRadix = tnum;
    lint res = CheckUnderRadix(num1, minRadix, maxRadix, snum2);
    if (res == -1)
    {
        cout << "Impossible";
    }
    else
    {
        cout << res;
    }

    return 0;
}
```