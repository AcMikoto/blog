---
title: LeetCode第65场双周赛
toc: true
tags:
  - LeetCode
  - 周赛
  - 模拟
  - 二分套二分
categories:
  - - LeetCode
    - 周赛
date: 2021-11-15 13:35:09
updated:
---

**Rank** : 518/2676  
**Solved** : 3/4 

[竞赛链接](https://leetcode-cn.com/contest/biweekly-contest-65/)

<!--more-->

## T1: [检查两个字符串是否几乎相等](https://leetcode-cn.com/problems/check-whether-two-strings-are-almost-equivalent/)  

### 思路
用两个数组分别统计字符数量，随后判断
### Code
```
class Solution {
public:
    bool checkAlmostEquivalent(string w1, string w2) {
        int c1[26] {}, c2[26] {};
        for(auto&& i: w1) c1[i - 'a'] ++;
        for(auto&& i: w2) c2[i - 'a'] ++;
        for(int i = 0; i < 26; i ++) {
            if(abs(c1[i] - c2[i]) > 3) return false;
        }
        return true;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(N)$
----

## T2: [模拟行走机器人 II](https://leetcode-cn.com/problems/walking-robot-simulation-ii/)  

### 思路
按题意模拟，注意数据范围比较大，需取模
### Code
```
int dy[] = {0, -1, 0, 1}, dx[] = {1, 0, -1, 0};
string dir[] = {"East", "South", "West", "North"};
class Robot {
public: 
    int w, h, u = 0;
    int x = 0, y = 0, c;
    Robot(int width, int height) {
        w = width, h = height;
        c = 2 * (w - 1) + 2 * (h - 1);
    }
    
    void move(int num) {
        if(num > 0) {
            int nx = x + dx[u], ny = y + dy[u];
            if(nx < 0 || ny < 0 || nx >= w || ny >= h)
                u = ((u - 1) % 4 + 4) % 4;
        }
        int d;
        if(u == 0) d = min(num, w - x - 1);
        else if(u == 2) d = min(num, x);
        else if(u == 1) d = min(num, y);
        else d = min(num, h - y - 1);
        num -= d;
        x = x + d * dx[u], y = y + d * dy[u];
        if(num > 0) {
            if(num > c) num = num % c;
            move(num);
        } 
    }
    
    vector<int> getPos() {
        return {x, y};
    }
    
    string getDir() {
        return dir[u];
    }
};
```
### 复杂度分析

- 时间复杂度$O(1)$
- 空间复杂度$O(1)$
----

## T3: [每一个查询的最大美丽值](https://leetcode-cn.com/problems/most-beautiful-item-for-each-query/)  

### 思路
求$x \leq c$时$f(x)$的值，经典树状数组可以解决的问题，离散化+树状数组解决
### Code
```
const int N = 2e5 + 10;
int tr[N];

int lowbit(int x) {
    return x & -x;
}

void add(int x, int c) {
    for(int i = x; i < N; i += lowbit(i)) tr[i] = max(tr[i], c);
}

int query(int x) {
    int res = 0;
    for(int i = x; i; i -= lowbit(i)) res = max(res, tr[i]);
    return res;
}

class Solution {
public:
    vector<int> maximumBeauty(vector<vector<int>>& items, vector<int>& queries) {
        fill_n(tr, N, 0);
        vector<int> alls;
        for(auto&& i: items) alls.push_back(i[0]);
        for(auto&& i: queries) alls.push_back(i);
        sort(alls.begin(), alls.end());
        alls.erase(unique(alls.begin(), alls.end()), alls.end());
        auto find = [&](int x) {
            return upper_bound(begin(alls), end(alls), x) - alls.begin();
        };
        for(auto&& i: items) add(find(i[0]), i[1]);
        vector<int> res;
        for(auto&& i: queries) res.push_back(query(find(i)));
        return res;
    }
};
```
### 复杂度分析
- 时间复杂度$O(NlogN)$
- 空间复杂度$O(N)$ 
----

## T4: [你可以安排的最多任务数目](https://leetcode-cn.com/problems/maximum-number-of-tasks-you-can-assign/)  

### 思路
首先，答案显然具有**单调性**，进一步地，如果可以完成x个任务，那么**x个最强壮的工人嗑药一定可以完成x个最弱的任务**，反之如果x个最强壮的工人嗑药无法完成x个最弱的任务，则其它方案必然也无法满足条件。
因此确定主体框架：可以先分别对工人和任务排序，然后二分答案。  
二分最关键的是check函数的实现，可以**从大到小**考虑每一个任务，对于每一个任务首先找不嗑药满足要求的最弱工人，如果没有，则寻找一个嗑药能刚好满足要求的最弱工人
### Code
```
class Solution {
public:
    int maxTaskAssign(vector<int>& ts, vector<int>& ws, int ps, int str) {
        int n = ts.size(), m = ws.size();
        sort(begin(ts), end(ts));
        sort(begin(ws), end(ws), greater{});
        auto check = [&](int x) {
            if(x > m) return false;
            int cnt = 0;
            multiset<int> st(begin(ws), begin(ws) + x);
            for(int i = x - 1; i >= 0; i --) {
                auto pos = st.lower_bound(ts[i]);
                if(pos != st.end()) st.erase(pos);
                else if(cnt < ps) {
                    pos = st.lower_bound(ts[i] - str);
                    if(pos != st.end()) st.erase(pos), cnt ++;
                    else return false;
                }
                else return false;
            }
            return true;
        };
        int l = 0, r = n;
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(check(mid)) l = mid;
            else r = mid - 1;
        }
        return l;
    }
};
```
### 复杂度分析
- 时间复杂度$O(Nlog^2N)$
- 空间复杂度$O(N)$
----

## 个人总结  
主要是第二题模拟题写炸，花费太久 + 罚时太多，需要增加模拟题的训练。  

----
**欢迎讨论指正**