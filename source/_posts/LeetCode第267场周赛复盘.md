---
title: LeetCode第267场周赛复盘
categories:
- LeetCode
- 周赛复盘
tags:
- 链表
- 模拟
- 并查集
---
solved：4/4  
rank：374/4364  
实况概括：看错题*2，小错误不断，罚时太多。  

T1：[5926. 买票需要的时间](https://leetcode-cn.com/problems/time-needed-to-buy-tickets/)  

有 n 个人前来排队买票，其中第 0 人站在队伍 最前方 ，第 (n - 1) 人站在队伍 最后方 。

给你一个下标从 0 开始的整数数组 tickets ，数组长度为 n ，其中第 i 人想要购买的票数为 tickets[i] 。

每个人买票都需要用掉 恰好 1 秒 。一个人 一次只能买一张票 ，如果需要购买更多票，他必须走到  队尾 重新排队（瞬间 发生，不计时间）。如果一个人没有剩下需要买的票，那他将会 离开 队伍。

返回位于位置 k（下标从 0 开始）的人完成买票需要的时间（以秒为单位）。

- n == tickets.length
- 1 <= n <= 100
- 1 <= tickets[i] <= 100
- 0 <= k < n  

分析：数据范围很小，按题意模拟即可:  

参考代码：  
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

T2：[5927. 反转偶数长度组的节点](https://leetcode-cn.com/problems/reverse-nodes-in-even-length-groups/)  
给你一个链表的头节点 head 。

链表中的节点**按顺序**划分成若干**非空**组，这些非空组的长度构成一个自然数序列（1, 2, 3, 4, ...）。一个组的 长度 就是组中分配到的节点数目。换句话说：

节点 1 分配给第一组
节点 2 和 3 分配给第二组
节点 4、5 和 6 分配给第三组，以此类推
注意，最后一组的长度可能小于或者等于 1 + 倒数第二组的长度 。

反转 每**偶数**长度组中的节点，并返回修改后链表的头节点 head 。

- 链表中节点数目范围是 [1, $10^5$]
- 0 <= Node.val <= $10^5$  

分析：看错题，以为是偶数编号的就翻转，WA一发，链表题比赛时能转化为vector做就直接转，这样比较方便。  

参考代码：   
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

T3：[5928. 解码斜向换位密码](https://leetcode-cn.com/problems/decode-the-slanted-ciphertext/)  
题干颇长，略；  

分析： 按题意模拟即可，比赛时先是转二维字符数组大小开错wa一发，然后是tle一发，后来二维映射一维才过，比较奇怪   
参考代码：  
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

T4：[5929. 处理含限制条件的好友请求](https://leetcode-cn.com/problems/process-restricted-friend-requests/)    
给你一个整数 n ，表示网络上的用户数目。每个用户按从 0 到 n - 1 进行编号。

给你一个下标从 0 开始的二维整数数组 restrictions ，其中 restrictions[i] = $[x_i, y_i]$ 意味着用$x_i$和用户$y_i$不能 成为 朋友 ，不管是 直接 还是通过其他用户 间接 。

最初，用户里没有人是其他用户的朋友。给你一个下标从 0 开始的二维整数数组 requests 表示好友请求的列表，其中 requests[j] = $[u_j, v_j]$ 是用户 $u_j$ 和用户 $v_j$ 之间的一条好友请求。

如果 u_j 和 v_j 可以成为 朋友 ，那么好友请求将会 成功 。每个好友请求都会按列表中给出的顺序进行处理（即，requests[j] 会在 requests[j + 1] 前）。一旦请求成功，那么对所有未来的好友请求而言， $u_j$ 和 $v_j$ 将会 成为直接朋友 。

返回一个 布尔数组 result ，其中元素遵循此规则：如果第 j 个好友请求 成功 ，那么 result[j] 就是 true ；否则，为 false 。

注意：如果 $u_j$ 和 $v_j$ 已经是直接朋友，那么他们之间的请求将仍然 成功 。

- 2 <= n <= 1000
- 0 <= restrictions.length <= 1000
- restrictions[i].length == 2
- 0 <= $x_i$, $y_i$ <= n - 1
- $x_i$ != $y_i$
- 1 <= requests.length <= 1000
- requests[j].length == 2
- 0 <= $u_j$, $v_j$ <= n - 1
- $u_j$ != $v_j$

分析：非常显然的并查集，这一点倒是很早看出来，比赛时理解题意出了大问题，首先以为两种关系都是按下标顺序给的，于是写了带扩展域的并查集，半天debug也没看出来为啥错。然后脑抽把无法成为朋友的关系也加入并查集，实际上a和b不能成为朋友，b和c不能成为朋友并不能推出a和c不能成为朋友，最后写了一个理论复杂度$O(n^3)$的算法水过去了。  

朋友关系显然具有传递性，因此可以用并查集来维护，维护时顺便将集合内的所有元素用一个哈希表维护，处理一个新的朋友请求时，如果双方本来就在一个集合内，则true，如果不在，则可以暴力枚举所有的敌对关系，如果某一对敌对关系的两端刚好分别在两个集合内，则朋友关系无法成立。整体复杂度为$O(nm)$，而比赛时写的是两层循环两个集合，判断是否元素间是否敌对，复杂度最坏为$O(n^3)$,由于数据的原因实际效率比较高，但依旧不推荐。这也提醒了，需要根据数据范围决定我们考虑的角度，枚举元素理论上超时，枚举关系更加合理。

参考代码： 
标准解法（400ms）：   
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
                //类似按秩合并，启发式合并，效率更高一些；
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

错误解法（120ms）：
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
        unordered_set<int> st;
        auto hash = [](int a, int b) {
            return (a << 10) + b;
        };
        for(auto&& i: rts) st.insert({hash(i[0], i[1]), hash(i[1], i[0])});
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
                for(auto&& i: f[a]) {
                    for(auto&& j: f[b]) {
                        if(st.count(hash(i, j))) {
                            return true;
                        }
                    }
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

总结：题意理解能力一直很弱，比赛时容易着急，只能多打一打了，以赛代练，另外不同角度思考问题的能力需要继续进步。