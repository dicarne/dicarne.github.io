---
title: pat - 甲 - 考完啦，暂告一段落
date: 2019-09-10 11:27:17
tags:
---
上周日的pat圆满结束，达到了预定目标100分，撒花！  

### 难度
甲级第一题有一点难度，后面三题都很简单。刚开始写第一题的时候，心中纳闷，第一题就这么诡异后面怎么办？有难度！于是写了半小时没写出来，运行超时，然后开始写后面的，想着回头再写。  
没想到后面啪啪啪写完一道两道三道，原来只是第一题难一点，后面都没有难度，幸好先跳了，保存了良好的心态。剩下一个半小时，怎么会写不出第一道！  
第一题换了三种思路，第一遍用数组排列组合递归计算，虽然思路还行，但是数组拷贝想想也会超时，pass；第二遍想着，暴力解会不会过，然后写了个简单粗暴的循环遍历，不出意外的超时了，pass；第三遍改进了第一遍的方法，我还是觉得这道题就是个排列组合的题，于是优化了一下，去掉了数组，从高位往低位递归的进行计算，最后排序输出，折腾了一会儿，终于过了，交卷走人。  
第一题的难点在于，给的数据量很大，很容易超时，而且自己根本无法构造测试数据，硬核地检测你逻辑的正确性。如果做一大堆骚操作做特例判断哪些数符合条件，哪些数跳过，往往费事又无法考虑周全，反而应该停一停，换个思路，做出更为通用简洁的算法。 
PS：当时做完所有题，会回到题目列表，一看第一题通过率0.02……可怕。以前做的真题最低也是0.2 

### 感悟
通过pat的练习，学到的最重要的一个思想就是，想不清了、乱了、复杂了，立刻换种思路，不要缝缝补补原先的程序，往往只有好处。不要被原先的思路局限，换换角度，往往能找到令自己都惊叹的思路。  
至于算法什么的，都是附带的，随便写写就会了。关键还是看问题的思路、经验。  

### 关于考试的建议
考前一共做了58道真题，前面39道，后面49道，前面的难，后面的简单。  
如果你是新手但以前好好学习敲过一些代码，还是建议先做前面的，再做后面的，先被奇怪的题恶心一下，积攒一点经验，再做后面的近些年的原题，会得心应手不少，舒服极了。经验还是很重要的，有些题一看就知道敲过几遍，就不慌了。  
至于要不要做完，如果你闲的话，随你，可以做，但没有必要。做过几十道题，我就懒得做了。知道一些题目套路，刷多了我自认为也只是浪费时间没有什么提高。到时候想不到的还是想不到的。  
当然，如果做了几十道题没啥感觉还是觉得很懵逼，或者以前没怎么写过代码的，还是建议多写一点，能写多少是多少。  
还有考试前所有算法还是要看看，我就翻了翻数据结构的书，看了看算法，然后这次考试那道图的题虽然我练习的时候从来没写过那个算法，但算法我会啊，挠了挠头就大致不离的写出来了，一下就过了。但如果你因为没练到，就压根没听说过这个算法，就只能呵呵了。

### 关于题解
---
后面几道题没什么可以说的，就说说第一题吧，其实也没什么难度，关键是想不想得到，理不理的清。

### 7-1 Forever (20 分)
"Forever number" is a positive integer A with K digits, satisfying the following constrains:  

the sum of all the digits of A is m;  
the sum of all the digits of A+1 is n; and the greatest common divisor of m and n is a prime number which i  s greater than 2.  
Now you are supposed to find these forever numbers.

#### Input Specification:
Each input file contains one test case. For each test case, the first line contains a positive integer N (≤5). Then N lines follow, each gives a pair of K (3<K<10) and m (1< m<90), of which the meanings are given in the problem description.  

#### Output Specification: 
For each pair of K and m, first print in a line Case X, where X is the case index (starts from 1). Then print n and A in the following line. The numbers must be separated by a space. If the solution is not unique, output in the ascending order of n. If still not unique, output in the ascending order of A. If there is no solution, output No Solution.  

##### Sample Input:
```
2
6 45
7 80
```
##### Sample Output:
```
Case 1
10 189999
10 279999
10 369999
10 459999
10 549999
10 639999
10 729999
10 819999
10 909999
Case 2
No Solution
```

#### 简介
给定数字的位数K，若存在K位的数，它的每一位加起来为m，且这个数+1后每一位加起来为n，而m和n的最大公因数是一个大于2的素数，那么输出所有这种数。

#### 思路
给定K和m。  
1. 假设首位为1，则第2位到第K位加起来的数只能是m-1才能符合要求。  
    1. 假设第2位为1，则第3位到第k位加起来的数只能是m-1-1才能符合要求。  
        1. 假设第三位为1，则第4位到第K位加起来只能是m-1-1-1才能符合要求。  
            1. ......  

可以看出，递归的思路十分简单，你给我一个K位数，要他们每一位加起来是m，那么我定了这个数最前面一位为a，后面跟着那个K-1位的数加起来只能是剩下来的m-a。如果到某一位剩下来的额度变成0了，那前面的数是之前确定的，后面的数就只能是000...（用0补齐，当然如果到最后一位才用完自然就没有0结尾了）。  
然后再判断这个数的m和n是否符合条件即可。

#### 代码
```c++
#include <vector>
#include <iostream>
#include <algorithm>
#include <cstdio>
#include <map>
using namespace std;
int N, K, n, m;
int powint(int n) {
	int data[] = { 1, 10, 100, 1000, 10000, 100000, 1000000, 10000000, 100000000, 1000000000 };
	return data[n-1];
}
int sumup(int k) {
	int sum = 0;
	for (int i = 0; i < K; i++) {
		sum += k % 10;
		k /= 10;
	}
	return sum;
}
bool isprime(int n) {
	if (n < 2) return false;
	for (int i = 2; i * i <= n; i++) {
		if (n%i == 0) return false;
	}
	return true;
}
bool find(int a, int b) {
	if (a > b) {
		int t = a;
		a = b;
		b = t;
	}
	int rr = -1;
	for (int i = 3; i <= a; i++) {
		if (b%i == 0 && a%i == 0) {
			rr = i;
		}
	}
	return isprime(rr);
}
struct node {
	int n, A;
};
vector<node>result;
map<int, int> n2n;
void func(int base, int digt, int tm) {
	for (int i = 0; i <= 9; i++) {
		if (digt == K && i == 0) continue;
		int resM = tm - i;
		if (resM == 0) {
			int t = base + i * powint(digt);
			int n = sumup(t+1);
			int fd = find(n, m);
			if (fd) {
				result.push_back({ n,t });
			}
		}
		if (resM < 0) {
			break;
		}
		if (resM > 0 && digt > 1 && resM <= digt*9) {
			func(base + i * powint(digt), digt-1, resM);
		}
	}
}

int main() {

	cin >> N;
	for (int in = 0; in < N; in++) {
		cin >> K >> m;
		int num = 1;
		for (int i = 0; i < K - 1; i++) {
			num *= 10;
		}
		cout << "Case " << in + 1 << "\n";
		result.clear();
			func(0, K, m);
		if (result.size() == 0) {
			cout << "No Solution\n";
		}
		else {
			sort(result.begin(), result.end(), [](node& l, node& r) {
				if (l.n != r.n) {
					return l.n < r.n;
				}
				else {
					return l.A < r.A;
				}
			});
			for (auto it : result) {
				printf("%d %d\n", it.n, it.A);
			}
		}
	}
	return 0;
}
```