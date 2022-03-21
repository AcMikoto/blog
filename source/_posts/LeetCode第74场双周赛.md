---
title: LeetCode第74场双周赛
toc: true
tags:
  - LeetCode
  - 周赛
categories:
  - - LeetCode
    - 周赛
date: 2022-03-21 14:55:15
updated:
---

**Rank** : 666/5442
**Solved** : 4/4

[竞赛链接](https://leetcode-cn.com/contest/biweekly-contest-74/)

<!--more-->

## T1: [2206. 将数组划分成相等数对](https://leetcode-cn.com/problems/divide-array-into-equal-pairs/)  

### 思路
计数，若存在奇数个相同元素则不可能，否则OK
### Code
```
class Solution {
public:
    bool divideArray(vector<int>& nums) {
        unordered_map<int, int> cnt;
        for(auto&& i: nums) cnt[i] ++;
        for(auto&& [k, v]: cnt) if(v & 1) return false;
        return true;
    }
};
```

### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(N)$
----

## T2: [6021. 字符串中最多数目的子字符串](https://leetcode-cn.com/problems/maximize-number-of-subsequences-in-a-string/)  

### 思路
仅考虑操作后的增加量，在某个位置插入一个首字符，可以与其后面的所有匹配串尾字符组成合法子序列，因此若插入首字符应当插入在text头，同理可得插入尾字符应当插入在text尾，两种情况取max即可。
### Code
```
class Solution {
public:
    long long maximumSubsequenceCount(string text, string pattern) {
        int n = size(text);
        vector<int> r(n + 1);
        for(int i = n - 1; i >= 0; i --) {
            r[i] = r[i + 1] + (text[i] == pattern[1]);
        }
        auto sum = 0ll;
        int cur = 0;
        for(int i = 0; i < n; i ++) {
            if(text[i] == pattern[0]) cur ++, sum += r[i + 1];
        }
        return max(cur, r[0]) + sum;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(N)$
----

## T3: [2208. 将数组和减半的最少操作次数](https://leetcode-cn.com/problems/minimum-operations-to-halve-array-sum/)  

### 思路
很显然的贪心，每次取最大的减半。
### Code
```
class Solution {
public:
    int halveArray(vector<int>& nums) {
        auto tot = reduce(begin(nums), end(nums), 0.0);
        double cur = tot;
        priority_queue<double> q;
        for(auto&& i: nums) q.push(i);
        int res = 0;
        while(true) {
            auto u = q.top();
            q.pop();
            q.push(u / 2);
            cur -= u / 2;
            res ++;
            if(cur <= tot / 2) break;
        }
        return res;
    }
};
```

### 复杂度分析
最坏$nlogn$,当全部数相等的时候，需要对每个数折半一次。

- 时间复杂度$O(NlogN)$
- 空间复杂度$O(N)$ 
----

## T4: [2209. 用地毯覆盖后的最少白色砖块](https://leetcode-cn.com/problems/minimum-white-tiles-after-covering-with-carpets/)  

### 思路
很坑，乍一看以为是贪心，关键贪心2700+用例最后只剩两个，莽了许久，摆烂wa 10次，完了才发现是动归，个人造反例的能力太差，有待加强。

dp方面是常见套路，状态定义为前i个元素中覆盖j次的最少白色砖块数量。
### Code
```
const int N = 1010;
int dp[N][N];

class Solution {
public:
    int minimumWhiteTiles(string f, int num, int c) {
        int n = size(f), res = 0;
        memset(dp, 0, sizeof dp);
        for(int i = 1; i <= n; i ++) dp[i][0] = dp[i - 1][0] + (f[i - 1] == '1');
        for(int i = 1; i <= n; i ++)
            for(int j = 1; j <= num; j ++)
                dp[i][j] = min(dp[i - 1][j] + (f[i - 1] == '1'), dp[max(0, i - c)][j - 1]);
        return dp[n][num];
    }
};
```

### 复杂度分析
- 时间复杂度$O(N^2)$
- 空间复杂度$O(N^2)$
----

## 个人总结
拉胯，需要重新增加练习了。


----
**欢迎讨论指正**