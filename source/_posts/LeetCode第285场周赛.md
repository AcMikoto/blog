---
title: LeetCode第285场周赛
toc: true
tags:
  - LeetCode
  - 周赛
categories:
  - - LeetCode
    - 周赛
date: 2022-03-21 15:38:05
updated:
---

**Rank** : 599/7501
**Solved** : 3/4

[竞赛链接](https://leetcode-cn.com/contest/weekly-contest-285/)

<!--more-->

## T1: [2210. 统计数组中峰和谷的数量](https://leetcode-cn.com/problems/count-hills-and-valleys-in-an-array/)  

### 思路
唯一坑点需要去重

### Code
```
class Solution {
public:
    int countHillValley(vector<int>& nums) {
        nums.erase(unique(begin(nums), end(nums)), end(nums));
        int n = size(nums), res = 0;
        for(int i = 1; i < n - 1; i ++) {
            if(nums[i - 1] > nums[i] && nums[i + 1] > nums[i]) res ++;
            else if(nums[i - 1] < nums[i] && nums[i + 1] < nums[i]) res ++;
        }
        return res;
        
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(1)$
----

## T2: [2211. 统计道路上的碰撞次数](https://leetcode-cn.com/problems/count-collisions-on-a-road/)  

### 思路
结论题，除了字符串开头连续的L和字符串结尾连续的R，剩余所有车辆最终都会停下并贡献一次碰撞。
### Code
```
class Solution {
public:
    int countCollisions(string directions) {
        int n = size(directions);
        int l = 0, r = n - 1;
        while(l < n && directions[l] == 'L') l ++;
        while(r >= 0 && directions[r] == 'R') r --;
        int res = 0;
        for(int i = l; i <= r; i ++) if(directions[i] != 'S') res ++;
        return res;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(1)$
----

## T3: [2212. 射箭比赛中的最大得分](https://leetcode-cn.com/problems/maximum-points-in-an-archery-competition/)  

### 思路
注意到计分区域仅有12个，因此可以暴力枚举Bob的得分状态，判断是否合法并寻找最大值；
### Code
```
class Solution {
public:
    vector<int> maximumBobPoints(int num, vector<int>& a) {
        int n = size(a);
        vector<int> res(n);
        int ma = 0;
        for(int i = 0; i < 1 << n; i ++) {
            int cur = 0, u = 0;
            for(int j = 0; j < n; j ++) {
                if(i >> j & 1) {
                    cur += a[j] + 1;
                    u += j;
                }
            }
            if(ma < u && cur <= num) {
                ma = u;
                fill(begin(res), end(res), 0);
                for(int j = 0; j < n; j ++) {
                    if(i >> j & 1) {
                        res[j] = a[j] + 1;
                    }
                }
                int tot = reduce(begin(res), end(res), 0);
                res.back() += num - tot;
            }
        }
        return res;
    }
};
```
### 复杂度分析
- 时间复杂度$O(12 * 2^12)$
- 空间复杂度$O(1)$ 
----

## T4: [2213. 由单个字符重复的最长子字符串](https://leetcode-cn.com/problems/longest-substring-of-one-repeating-character/)  

### 思路

- 无花里胡哨数据结构版本
由于这里区间的特殊性，不会存在重叠等情况，因此可以方便的用一个set维护区间[l, r)的集合，对于一个下标idx，如果我们确定set中存在某个区间跨过这个点，可以通过upper_bound找到所在区间的下一个迭代器（由于区间均不重叠），在此逻辑上，同时用一个multiset维护区间长度即可。对于每一个字母均可如此操作。
### Code
```
class Solution {
public:
    vector<int> longestRepeating(string s, string qc, vector<int>& q) {
        using pii = pair<int, int>;
        int n = size(s), m = size(q), INF = 0x3f3f3f3f;
        vector<set<pii>> seg(26);
        vector<multiset<int>> pq(26);
        //频繁的操作写成函数，不容易错也更好写
        auto add = [&](int u, int l, int r) {
            seg[u].emplace(l, r);
            pq[u].emplace(r - l);
        };
        auto sub = [&](int u, auto it) {
            pq[u].erase(pq[u].find(it -> second - it -> first));
            seg[u].erase(it);
        };

        for(int i = 0; i < n; i ++) {
            int j = i, u = s[i] - 'a';
            while(j < n && s[j] == s[i]) j ++;
            add(u, i, j);
            i = j - 1;
        }
        
        vector<int> res(m);
        for(int i = 0; i < m; i ++) {
            int idx = q[i], u = s[idx] - 'a', nxt = qc[i] - 'a';
            s[idx] = qc[i];

            if(u != nxt) {
                auto it = prev(seg[u].upper_bound({idx, INF}));
                auto [l, r] = *it;
                sub(u, it);
                if(idx > l) add(u, l, idx);
                if(r > idx + 1) add(u, idx + 1, r);
                
                int st = idx, ed = idx + 1;
                if(idx && s[idx - 1] == s[idx]) {
                    auto it = prev(seg[nxt].upper_bound({idx - 1, INF}));
                    auto [l, r] = *it;
                    sub(nxt, it);
                    st = l;
                }

                if(idx < n - 1 && s[idx + 1] == s[idx]) {
                    auto it = prev(seg[nxt].upper_bound({idx + 1, INF}));
                    auto [l, r] = *it;
                    sub(nxt, it);
                    ed = r;
                }
                add(nxt, st, ed);
            }

            //update answer
            for(int j = 0; j < 26; j ++) {
                if(pq[j].empty()) continue;
                res[i] = max(res[i], *rbegin(pq[j]));
            }
        }
        return res;       
        
    }
};
```

- 线段树版本
众所周知，线段树是维护区间问题的利器，比赛时想到线段树但是没想到到底如何维护区间信息。
这里是单点修改，适用不带懒标记的线段树
对于每一个区间，我们维护其最长前缀长度以及最长后缀长度还有最长相同字符串长度。另外为了方便，额外维护一个区间所有字母是否相同的标记以及区间左右端点字符值。两个区间合并时，根据左区间的右端点与右区间的左端点字符是否相同划分为两种情况。具体逻辑见代码；

### Code
```
const int N = 100010;

struct TR {
    int l, r;
    int ls, rs, ma;
    bool sm;
    char L, R;
} tr[N * 4];

void build(int u ,int l, int r) {
    tr[u] = {l, r};
    if(l == r) return;
    int mid = l + r >> 1;
    build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
}

void pushup(int u) {
    auto &l = tr[u << 1], &r = tr[u << 1 | 1];
    tr[u].ls = l.ls, tr[u].rs = r.rs;
    tr[u].L = l.L, tr[u].R = r.R;
    tr[u].sm = false;
    if(l.R == r.L) {
        if(l.sm && r.sm) {
            tr[u].ls = tr[u].rs = tr[u].ma = l.ma + r.ma;
            tr[u].sm = true;
        } else if(l.sm) {
            tr[u].ls = l.ma + r.ls;
            tr[u].ma = max(tr[u].ls, r.ma);
        } else if(r.sm) {
            tr[u].rs = l.rs + r.ma;
            tr[u].ma = max(tr[u].rs, l.ma);
        } else {
            tr[u].ma = max({l.ma, r.ma, l.rs + r.ls});
        }
    } else {
        tr[u].ma = max(l.ma, r.ma);
    }
    
}

int query(int u, int l, int r) {
    if(tr[u].l >= l && tr[u].r <= r) return tr[u].ma;
    int res = 0;
    int mid = tr[u].l + tr[u].r >> 1;
    if(l <= mid) res = query(u << 1, l, r);
    if(r > mid) res = max(res, query(u << 1 | 1, l, r));
    return res;
}

void modify(int u, int x, char v) {
    if(tr[u].l == x && tr[u].r == x) {
        tr[u].ls = tr[u].rs = tr[u].ma = 1;
        tr[u].sm = true;
        tr[u].L = tr[u].R = v;
    }
    else {
        int mid = tr[u].l + tr[u].r >> 1;
        if(x <= mid) modify(u << 1, x, v);
        else modify(u << 1 | 1, x, v);
        pushup(u);
    }
}

class Solution {
public:
    vector<int> longestRepeating(string s, string qc, vector<int>& q) {
        memset(tr, 0, sizeof tr);
        
        int n = size(s), m = size(q);
        build(1, 0, n);
        for(int i = 0; i < n; i ++) modify(1, i, s[i]);
        
        vector<int> res(m);
        for(int i = 0; i < m; i ++) {
            modify(1, q[i], qc[i]);
            res[i] = query(1, 0, n);
        }
        return res;
    }
};
```
### 复杂度分析
- 时间复杂度$O()$
- 空间复杂度$O()$
----

## 个人总结

越来越菜了，需要增加练习

----
**欢迎讨论指正**