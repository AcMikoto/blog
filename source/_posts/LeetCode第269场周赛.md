---
title: LeetCode第269场周赛
toc: true
tags:
  - LeetCode
  - 周赛
categories:
  - - LeetCode
    - 周赛
date: 2021-11-28 11:14:40
updated:
---

**Rank** : 68/4285
**Solved** : 4/4

[竞赛链接](https://leetcode-cn.com/contest/weekly-contest-269)

<!--more-->

## T1: [找出数组排序后的目标下标](https://leetcode-cn.com/contest/weekly-contest-269/problems/find-target-indices-after-sorting-array/)  

### 思路
数据范围小，暴力即可 
### Code
```
class Solution {
public:
    vector<int> targetIndices(vector<int>& nums, int t) {
        sort(begin(nums), end(nums));
        vector<int> res;
        for(int i = 0; i < size(nums); i ++) {
            if(nums[i] == t) res.push_back(i);
        }
        return res;
    }
};
```
### 复杂度分析

- 时间复杂度$O(NlogN)$
- 空间复杂度$O(logN)$
----

## T2: [半径为 k 的子数组平均值](https://leetcode-cn.com/contest/weekly-contest-269/problems/k-radius-subarray-averages/)  

### 思路
前缀和，枚举即可，坑点在于会爆int,还可以滑动窗口，不想滑。
### Code
```
class Solution {
public:
    vector<int> getAverages(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> res(n, -1);
        vector<long> sum(n + 1);
        for(int i = 0; i < n; i ++) sum[i + 1] = sum[i] + nums[i];
        for(int i = k; i + k < n; i ++) {
            res[i] = (sum[i + k + 1] - sum[i - k]) / (2 * k + 1);
        }
        return res;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(N)$
----

## T3: [从数组中移除最大值和最小值](https://leetcode-cn.com/contest/weekly-contest-269/problems/removing-minimum-and-maximum-from-array/)  

### 思路
注意数组元素互不相同，因此最大值最小值唯一，对于每个值，均可以从左边或右边移除，因此总共就四种情况，取个最小值即可。  
### Code
```
class Solution {
public:
    int minimumDeletions(vector<int>& nums) {
        int n = nums.size();
        if(n <= 2) return n;
        auto [minv, maxv] = minmax_element(begin(nums), end(nums));
        int l = minv - begin(nums), r = maxv - begin(nums);
        auto res = min({n - r + l + 1, n - l + r + 1, max(l + 1, r + 1), max(n - l, n - r)});
        return res;
    }
};
```
### 复杂度分析
- 时间复杂度$O(N)$
- 空间复杂度$O(1)$ 
----

## T4: [找出知晓秘密的所有专家](https://leetcode-cn.com/contest/weekly-contest-269/problems/find-all-people-with-secret/)  

### 思路
套dijkstra板子的题，两个专家在t时间开会，建两条权值为t的双向边(这里的权值是广义上的，代表由某些边到该专家的时间点)，额外地，从0到第一个专家建一条权值为0的边代表专家0在0时刻将秘密分享给第一个专家。用一个小根堆维护出堆时间和序号，根据dijkstra的思想，某个专家第一次出堆时，必然是消息传递给他的最早时间。此时将该专家标记并加入结果，随后用该专家去更新其它专家即可。  
### Code
```
class Solution {
public:
    vector<int> findAllPeople(int n, vector<vector<int>>& meet, int st) {
        vector<int> res;
        unordered_map<int, vector<pair<int, int>>> g;
        for(auto&& i: meet) {
            int a = i[0], b = i[1], c = i[2];
            g[a].emplace_back(b, c), g[b].emplace_back(a, c);
        }
        g[0].emplace_back(st, 0);
        vector<bool> vis(n, 0);
        auto q = priority_queue{greater{}, vector{pair(0, 0)}};
        while(q.size()) {
            auto [ut, u] = q.top();
            q.pop();
            if(vis[u]) continue;
            vis[u] = 1;
            res.push_back(u);
            for(auto&& [ne, net]: g[u]) {
                if(vis[ne]) continue;
                if(net >= ut) {
                    q.emplace(net, ne);
                }
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

## 个人总结
写错vis位置导致wa一发外加debug时间++，总体还可以。


----
**欢迎讨论指正**