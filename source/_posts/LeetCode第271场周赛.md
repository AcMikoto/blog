---
title: LeetCode第271场周赛
toc: true
tags:
  - LeetCode
  - 周赛
categories:
  - - LeetCode
    - 周赛
date: 2021-12-12 12:32:12
updated:
---

**Rank** : 562/4561
**Solved** : 4/4

[竞赛链接](https://leetcode-cn.com/contest/weekly-contest-271/)

<!--more-->

## T1: [5952. 环和杆](https://leetcode-cn.com/problems/rings-and-rods/)  

### 思路
哈希表统计即可
### Code
```
class Solution {
public:
    int countPoints(string rings) {
        unordered_set<char> st[10];
        int n = rings.size();
        for(int i = 0; i < n; i += 2) {
            st[rings[i + 1] - '0'].insert(rings[i]);
        }
        int res = 0;
        for(int i = 0; i < 10; i ++) if(st[i].size() == 3) res ++;
        return res;
    }
};
```
### 复杂度分析
线性扫一遍即可，仅需要常数空间统计
- 时间复杂度$O(N)$
- 空间复杂度$O(1)$
----

## T2: [5953. 子数组范围和](https://leetcode-cn.com/problems/sum-of-subarray-ranges/)  

### 思路
数据范围只有1000，因此暴力遍历两次即可，进阶的可以用单调栈做到$O(n)$统计
### Code
```
class Solution {
public:
    long long subArrayRanges(vector<int>& nums) {
        using ll = long long;
        ll res = 0;
        int n = nums.size();
        for(int i = 0; i <n; i ++) {
            int maxv = nums[i], minv = nums[i];
            for(int j = i; j >= 0; j --) {
                maxv = max(maxv, nums[j]);
                minv = min(minv, nums[j]);
                res += maxv - minv;
            }
        }
        return res;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N^2)$
- 空间复杂度$O(1)$
----

## T3: [5954. 给植物浇水 II](https://leetcode-cn.com/problems/watering-plants-ii/)  

### 思路
双指针模拟即可  
### Code
```
class Solution {
public:
    int minimumRefill(vector<int>& p, int a, int b) {
        int n = p.size();
        int res = 0, ua = a, ub = b;
        for(int i = 0, j = n - 1; i <= j; i ++, j --) {
            if(i == j) {
                if(max(ua, ub) < p[i]) res ++;
            }
            else {
                if(ua < p[i]) ua = a, res ++;
                if(ub < p[j]) ub = b, res ++;
                ua -= p[i];
                ub -= p[j];
            }
        }
        return res;
    }
};
```

### 复杂度分析
- 时间复杂度$O(N)$
- 空间复杂度$O(1)$ 
----

## T4: [5955. 摘水果](https://leetcode-cn.com/problems/maximum-fruits-harvested-after-at-most-k-steps/)  

### 思路
显然最多转弯一次即可，因此先求一遍前缀和然后枚举暴力枚举开始向左或向右走的步数即可。  
### Code
```
class Solution {
public:
    int maxTotalFruits(vector<vector<int>>& f, int st, int k) {
        const int N= 4e5 + 5;
        using ll = long long;
        ll sum[N] {};
        for(auto&& i: f) sum[i[0] + 1] += i[1];
        for(int i = 1; i < N; i ++) sum[i] += sum[i - 1];
        ll res = 0;
        //枚举左边步数
        for(int l = 0; l <= k; l ++) {
            int right = max(st, st + k - 2 * l), left = st - l;
            res = max(res, sum[right + 1] - sum[max(0, left)]);
        }
        //枚举右边步数
        for(int r = 0; r <= k; r ++) {
            int right = st + r, left = min(st, st - (k - 2 * r));
            res = max(res, sum[right + 1] - sum[max(0, left)]);
        }
        return res;
    }
};
```

### 复杂度分析
- 时间复杂度$O(N)$
- 空间复杂度$O(N)$
----

## 个人总结
日常前三题想到复杂的方式解题，比如这个第二题第一反应用线段树解，虽然确实复杂度是$N^2logN$，理论上可以过掉该题，但是由于线段树的常数过大，加上lc上计算总时间，因此tle掉两发。自此后头脑不清，开始摆烂。个人很大的问题在于卡题之后就急，急了后头脑非常SB，以至于后两题细节连续WA，开始摆烂。以后lc前三题不应该乱想，最多用到一个树状数组好吧，此外心态的问题需要继续锻炼。


----
**欢迎讨论指正**