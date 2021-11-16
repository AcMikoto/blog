---
title: LeetCode第267场周赛
toc: true
tags:
  - LeetCode
  - 周赛
  - 链表
  - 模拟
  - 并查集
categories:
  - - LeetCode
    - 周赛
date: 2021-11-15 14:03:46
updated:
---

**Rank** : 374/4364  
**Solved** : 4/4  

[竞赛链接](https://leetcode-cn.com/contest/weekly-contest-267/)

<!--more-->

## T1: [买票需要的时间](https://leetcode-cn.com/problems/time-needed-to-buy-tickets/)  

### 思路
数据范围小，按题意模拟即可
### Code
```
class Solution {
public:
    int timeRequiredToBuy(vector<int>& ts, int k) {
        queue<pair<int, int>> q;
        for(int i = 0; i < ts.size(); i ++) q.push({i, ts[i]});
        int t = 1;
        while(q.size()) {
            auto [u, ut] = q.front();
            q.pop();
            if(u == k && ut == 1) return t;
            if(ut > 1) q.push({u, ut - 1});
            t ++;
        }
        return 0;
    }
};
```
### 复杂度分析
M为购买票数的最大值  
- 时间复杂度$O(NM)$
- 空间复杂度$O(N)$
----

## T2: [反转偶数长度组的节点](https://leetcode-cn.com/problems/reverse-nodes-in-even-length-groups/)  

### 思路
扒到数组中处理，然后按题意模拟   
看错题，以为偶数编号翻转，导致wa  
### Code
```
class Solution {
public:
    ListNode* reverseEvenLengthGroups(ListNode* head) {
        vector<int> nums;
        while(head) {
            nums.push_back(head -> val);
            head = head -> next;
        }
        int n = nums.size();
        for(int i = 0, j = 1; i < n ; j ++) {
            int l = i, r = min(i + j - 1, n - 1);
            if((r - l + 1) % 2 == 0) {
                while(l < r) {
                    swap(nums[l], nums[r]);
                    l ++, r --;
                }
            }    
            i += j;
        }
        auto hair = new ListNode(0);
        auto cur = hair;
        for(auto&& i: nums) {
            cur -> next = new ListNode(i);
            cur = cur -> next;
        }
        return hair -> next;
    }
};
```
### 复杂度分析

- 时间复杂度$O(N)$
- 空间复杂度$O(N)$
----

## T3: [解码斜向换位密码](https://leetcode-cn.com/problems/decode-the-slanted-ciphertext/)  

### 思路
按题意模拟即可，枚举对角线  
### Code
```
class Solution {
public:
    string decodeCiphertext(string s, int rows) {
        int n = rows, m = s.size() / n;
        string res;
        for(int i = 0; i < m; i ++) {
            int x = 0, y = i;
            while(x < n && y < m) {
                res += s[x * m + y];
                x ++, y ++;
            }
        }
        while(res.size() > 1 && res.back() == ' ') res.pop_back();
        return res;
    }
};
```
### 复杂度分析
- 时间复杂度$O(N)$
- 空间复杂度$O(N)$ 
----

## T4: [处理含限制条件的好友请求](https://leetcode-cn.com/problems/process-restricted-friend-requests/)  

### 思路
朋友关系显然具有传递性，因此可以用并查集来维护，维护时顺便将集合内的所有元素用一个哈希表维护，处理一个新的朋友请求时，如果双方本来就在一个集合内，则true，如果不在，则可以暴力枚举所有的敌对关系，如果某一对敌对关系的两端刚好分别在两个集合内，则朋友关系无法成立。  
比赛时写的是两层循环两个集合，判断是否元素间是否敌对，多一层循环,由于数据的原因实际效率比较高，但依旧不推荐。这也提醒了，需要根据数据范围决定我们考虑的角度，枚举元素理论上超时，枚举关系更加合理。
### Code
```
const int N = 1010;
int p[N];
int find(int x) {
    if(x != p[x]) p[x] = find(p[x]);
    return p[x];
}
class Solution {
public:
    vector<bool> friendRequests(int n, vector<vector<int>>& rts, vector<vector<int>>& rqs) {
        vector<bool> res;
        iota(p, p + N, 0);
        vector<unordered_set<int>> f(n);
        for(int i = 0; i < n; i ++) f[i].emplace(i);
        for(auto&& i: rqs) {
            int a = find(i[0]), b = find(i[1]);
            if(a == b) {
                res.push_back(true);
                continue;
            }
            if([&]{
                for(auto&& i: rts) {
                    if(f[a].count(i[0]) && f[b].count(i[1]) || f[a].count(i[1]) && f[b].count(i[0]))
                        return true;
                }
                return false;
            }()) res.push_back(false);
            else {
                if(size(f[a]) > size(f[b])) swap(f[a], f[b]);
                p[a] = b;
                f[b].merge(move(f[a]));
                res.push_back(true);
            }
        }
        return res;
    }
};
```
### 复杂度分析
- 时间复杂度$O(NM)$
- 空间复杂度$O(N)$
----

## 个人总结
题意理解能力一直很弱，比赛时容易着急，需要增加比赛次数，以赛代练，另外不同角度思考问题的能力需要继续进步。最后，罚时太多，需要减少错误次数

----

**欢迎讨论指正**