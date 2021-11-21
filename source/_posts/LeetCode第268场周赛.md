---
title: LeetCode第268场周赛
toc: true
tags:
  - LeetCode
  - 周赛
categories:
  - - LeetCode
    - 周赛
date: 2021-11-21 13:26:16
updated:
---

**Rank** : 251/4397  
**Solved** : 4/4  

[竞赛链接](https://leetcode-cn.com/contest/weekly-contest-268/)  

<!--more-->

## T1: [两栋颜色不同且距离最远的房子](https://leetcode-cn.com/problems/two-furthest-houses-with-different-colors/)  

### 思路
数据范围小，暴力即可，线性做法是分别从两头找一下  
### Code
```
class Solution {
public:
    int maxDistance(vector<int>& nums) {
        int n = nums.size(), res = 0;
        for(int i = 0; i < n; i ++) {
            for(int j = i + 1; j < n; j ++) {
                if(nums[i] != nums[j]) {
                    res = max(res, j - i);
                }
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

## T2: [给植物浇水](https://leetcode-cn.com/problems/watering-plants/)  

### 思路
简单模拟题，按题意模拟即可
### Code
```
class Solution {
public:
    int wateringPlants(vector<int>& p, int c) {
        int n = size(p);
        int cur = c, res = 0;
        for(int i = 0; i < n; i ++) {
            if(cur >= p[i]) res ++;
            else {
                cur = c;
                res += i << 1 | 1;
            }
            cur -= p[i];
        }
        return res;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(1)$
----

## T3: [区间内查询数字的频率](https://leetcode-cn.com/problems/range-frequency-queries/)  

### 思路
哈希表记录某个值出现的下标位置，二分即可
### Code
```
class RangeFreqQuery {
public:
    unordered_map<int, vector<int>> um;
    RangeFreqQuery(vector<int>& nums) {
        int n = size(nums);
        for(int i = 0; i < n; i ++) {
            um[nums[i]].push_back(i);
        }
    }
    
    int query(int l, int r, int v) {
        auto&& a = um[v];
        return upper_bound(begin(a), end(a), r) - lower_bound(begin(a), end(a), l);
    }
};
```
### 复杂度分析
- 时间复杂度$O(NlogN)$
- 空间复杂度$O(N)$ 
----

## T4: [k 镜像数字的和](https://leetcode-cn.com/problems/sum-of-k-mirror-numbers/)  

### 思路
所有的数都需要满足十进制下是回文数，因此先枚举所足够大范围内的十进制回文数，然后依次判断是否为K进制数即可。枚举回文数 常用技巧是枚举一半。
### Code
```
using ll = long long;
auto pten = []{
    vector<ll> res;
    for(int cnt = 1; cnt <= 6; cnt ++) {
        string suffix(cnt, ' ');
        auto dfs = [&](auto&& dfs, int idx) {
            if(idx == cnt) {
                for(int i: {0, 1}) {
                    string prefix = suffix.substr(i);
                    reverse(begin(prefix), end(prefix));
                    res.push_back(stoll(prefix + suffix));
                }
                return;
            }
            for(int i = idx == cnt - 1; i < 10; i ++) {
                suffix[idx] = '0' + i;
                dfs(dfs, idx + 1);
            }
        };
        dfs(dfs, 0);
    }
    sort(begin(res), end(res));
    return res;
}();
vector<ll> table[10];
auto __ = []{
    auto check = [](ll i, ll j) {
        string cur;
        while(i) {
            cur += '0' + i % j;
            i /= j;
        }
        int l = 0, r = cur.size() - 1;
        while(l < r) {
            if(cur[l] != cur[r]) return false;
            l ++, r --;
        }
        return true;
    };
    for(auto&& i: pten) {
        for(int j = 2; j <= 9; j ++) {
            if(size(table[j]) == 30) continue;
            if(check(i, j)) table[j].push_back(i);
        }
    }
    return 0;
}();
class Solution {
public:
    long long kMirror(int k, int n) {
        ll res = 0;
        for(int i = 0; i < n; i ++) {
            res += table[k][i];
        }
        return res;
    }
};
```
### 复杂度分析
打表时间复杂度约$O(NlogN), N = 1e6$,空间复杂度$O(N)$,打表操作可以放到本地，查询仅需要常数时间与空间。    
- 时间复杂度$O(1)$
- 空间复杂度$O(1)$
----

## 个人总结  
错误太多，第三题上来就想主席树，实际上不好写，没想明白，看到通过数很多才删掉重写。建议打lc周赛忘掉这玩意，考不了。另外第四题中间check传参的时候写成int，爆int后第一时间没看出来，debug花费时间，外加WA两发，建议多写auto，减少错误。


----
**欢迎讨论指正**