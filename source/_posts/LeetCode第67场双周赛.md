---
title: LeetCode第67场双周赛
toc: true
tags:
  - LeetCode
  - 周赛
categories:
  - - LeetCode
    - 周赛
date: 2021-12-12 13:03:40
updated:
---

**Rank** : 63/2923 
**Solved** : 4/4

[竞赛链接](https://leetcode-cn.com/contest/biweekly-contest-67/ranking/)

<!--more-->

## T1: [5934. 找到和最大的长度为 K 的子序列](https://leetcode-cn.com/problems/find-subsequence-of-length-k-with-the-largest-sum/)  

### 思路
弄一个可删除堆存住前K大的所有元素，然后顺序遍历原数组，判断是否在堆里，若在则加入答案并在堆中删除一个该元素。
### Code
```
class Solution {
public:
    vector<int> maxSubsequence(vector<int>& nums, int k) {
        vector<int> res, a = nums;
        sort(begin(a), end(a), greater<>());
        multiset<int> st(begin(a), begin(a) + k);
        for(auto&& i: nums) {
            if(st.count(i)) {
                st.erase(st.find(i));
                res.push_back(i);
            }
        }
        return res;
    }
};
```
### 复杂度分析

- 时间复杂度$O(NlogN)$
- 空间复杂度$O(N)$
----

## T2: [5935. 适合打劫银行的日子](https://leetcode-cn.com/problems/find-good-days-to-rob-the-bank/)  

### 思路
左右各遍历一遍数组，统计左右两边满足条件的最大长度，然后顺序遍历原数组枚举每个点即可。 
### Code
```
class Solution {
public:
    vector<int> goodDaysToRobBank(vector<int>& s, int t) {
        int n = size(s);
        vector<int> res, l(n), r(n);
        int cnt = 0;
        for(int i = 1; i < n; i ++) {
            if(s[i - 1] >= s[i]) cnt ++; 
            else  cnt = 0;
            l[i] = cnt;
        }
        cnt = 0;
        for(int i = n - 2; i >= 0; i --) {
            if(s[i + 1] >= s[i]) cnt ++;
            else cnt = 0; 
            r[i] = cnt;
        }
        for(int i = 0; i < n; i ++) {
            if(l[i] >= t && r[i] >= t) res.push_back(i);
        }
        return res;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(N)$
----

## T3: [5936. 引爆最多的炸弹](https://leetcode-cn.com/problems/detonate-the-maximum-bombs/)  

### 思路
题目乍一看很唬人，破局点在于数据范围，n仅为100，因此依次枚举每个点作为第一个爆炸的炸弹时可以引爆的数量，取最大值即可。 
### Code
```
class Solution {
public:
    int maximumDetonation(vector<vector<int>>& b) {
        int n = b.size();
        int res = 0;
        //引爆x能不能引爆y
        using ll = long long;
        auto check = [&] (int x, int y) {
            ll x1 = b[x][0], x2 = b[x][1], rx = b[x][2];
            ll y1 = b[y][0], y2 = b[y][1];
            return ((x1 - y1) * (x1 - y1) + (x2 - y2) * (x2 - y2)) <= rx * rx;
        };
        vector<bool> st(n);
        auto dfs = [&](auto&& dfs, int x) -> int {
            int res = 1;
            st[x] = true;
            for(int i = 0; i < n; i ++) {
                if(st[i]) continue;
                if(check(x, i)) {
                    res += dfs(dfs, i);
                }
            }
            return res;  
        };
        for(int i = 0; i < n && res < n; i ++) {
            fill_n(begin(st), n, 0);
            res = max(dfs(dfs, i), res);
        }
        return res;
    }
};
```
### 复杂度分析
- 时间复杂度$O(N^3)$
- 空间复杂度$O(N)$ 
----

## T4: [5937. 序列顺序查询](https://leetcode-cn.com/problems/sequentially-ordinal-rank-tracker/)  

### 思路
pbds或SortedList应用题，但是由于查询一直是有序的也可以用set维护迭代器来解决
### Code
```
#include<ext/pb_ds/assoc_container.hpp>
#include<ext/pb_ds/tree_policy.hpp>
using namespace __gnu_pbds;

class SORTracker {
    tree<pair<int, string>, null_type, less<>, rb_tree_tag, tree_order_statistics_node_update> tr;
public:
    int cnt = 0;
    SORTracker() {
    }
    
    void add(string name, int score) {
        tr.insert({-score, name});
    }
    
    string get() {
        return tr.find_by_order(cnt ++) -> second;
    }
};

```
### 复杂度分析
- 时间复杂度$O(NlogN)$
- 空间复杂度$O(N)$
----

## 个人总结
犯错严重，前两题因为zz问题各wa一次，wa率太高，整体表现还可以。  

## 里程碑事件
本次周赛是是自2021年3月份零基础利用业余时间学习编程以来参加的第28场周赛，通过该场周赛积分也终于是提高到了2200+，获得了LeetCoede的guardian蓝牌子，值得纪念，基本上完成了1年内上guardian的目标。后续的时间中还需要继续努力，短期目标是稳定住guardian，稳扎稳打，慢慢冲分。


----
**欢迎讨论指正**