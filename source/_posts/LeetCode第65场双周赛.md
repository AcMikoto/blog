---
title: LeetCode第65场双周赛复盘
categories:
- LeetCode
- 周赛复盘
tags:
- 二分套二分
- 模拟
---
solved：3/4  
rank：518/2676   
实况概括：日常模拟题写炸了，wa十发，心态爆炸，神志不清。

T1：[5910. 检查两个字符串是否几乎相等](https://leetcode-cn.com/contest/biweekly-contest-65/problems/check-whether-two-strings-are-almost-equivalent/)  
如果两个字符串 word1 和 word2 中从 'a' 到 'z' 每一个字母出现频率之差都 不超过 3 ，那么我们称这两个字符串 word1 和 word2 几乎相等。  
给你两个长度都为 n 的字符串 word1 和 word2 ，如果 word1 和 word2 几乎相等 ，请你返回 true ，否则返回 false 。  
一个字母 x 的出现 频率 指的是它在字符串中出现的次数。  

分析：简单题，略  

参考代码：  
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
T2：[5911. 模拟行走机器人 II](https://leetcode-cn.com/problems/walking-robot-simulation-ii/)  
给你一个在 XY 平面上的 width x height 的网格图，左下角 的格子为 (0, 0) ，右上角 的格子为 (width - 1, height - 1) 。网格图中相邻格子为四个基本方向之一（"North"，"East"，"South" 和 "West"）。一个机器人 初始 在格子 (0, 0) ，方向为 "East" 。

机器人可以根据指令移动指定的 步数 。每一步，它可以执行以下操作。

沿着当前方向尝试 往前一步 。
如果机器人下一步将到达的格子 超出了边界 ，机器人会 逆时针 转 90 度，然后再尝试往前一步。
如果机器人完成了指令要求的移动步数，它将停止移动并等待下一个指令。

请你实现 Robot 类：


Robot(int width, int height) 初始化一个 width x height 的网格图，机器人初始在 (0, 0)，方向朝 "East"。  
void move(int num) 给机器人下达前进 num 步的指令。  
vector\<int> getPos() 返回机器人当前所处的格子位置，用一个长度为 2 的数组 [x, y] 表示。  
string getDir() 返回当前机器人的朝向，为 "North" ，"East" ，"South" 或者 "West"。  
- 2 <= width, height <= 100  
- 1 <= num <= $10^5$  
- move ，getPos 和 getDir 总共调用次数不超过 $10^4$ 次。  

分析：按题意模拟即可，注意数据范围比较大，暴力会超时，必须取模，且不能单步模拟。  
比赛时存在的问题：
1. 没有注意这个题的坐标为数学坐标系，并非常见编程坐标系  
2. 没有考虑数据范围，tle
3. 取模时没有考虑大于才能取模，为周长倍数时候取模后位置虽然正确但方向错误  

参考代码：  

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

T3: [5912. 每一个查询的最大美丽值](https://leetcode-cn.com/problems/most-beautiful-item-for-each-query/)  
给你一个二维整数数组 items ，其中 items[i] = [$price_i$ , $beauty_i$] 分别表示每一个物品的 价格 和 美丽值 。

同时给你一个下标从 0 开始的整数数组 queries 。对于每个查询 queries[j] ，你想求出价格小于等于 queries[j] 的物品中，最大的美丽值 是多少。如果不存在符合条件的物品，那么查询的结果为 0 。

请你返回一个长度与 queries 相同的数组 answer，其中 answer[j]是第 j 个查询的答案。

- 1 <= items.length, queries.length <= 105
- items[i].length == 2
- 1 <= $price_i$ , $beauty_i$, queries[j] <= 109

分析：比T2简单，离散化 + 树状数组直接莽，神志不清，N开小了wa一发     
$ps:$有更简单的解法，后续再讨论。

参考代码：  
```
using TT = long long;
static const int N = 2e5 + 10;
TT tr[N] {};
inline int lowbit(int x) {
    return x & -x;
}
inline void add(int x, TT c) {
    for(int i = x; i < N; i += lowbit(i)) tr[i] = max(tr[i], c);
}
inline TT query(int x) {
    TT res = 0;
    for(int i = x; i; i -= lowbit(i)) res = max(tr[i], res);
    return res;
}
inline TT query(int l, int r) {return query(r) - query(l - 1);}
void init() {memset(tr, 0, sizeof tr);}

class Solution {
public:
    vector<int> maximumBeauty(vector<vector<int>>& items, vector<int>& queries) {
        init();
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

T4：[5913. 你可以安排的最多任务数目](https://leetcode-cn.com/problems/maximum-number-of-tasks-you-can-assign/)  
给你 n 个任务和 m 个工人。每个任务需要一定的力量值才能完成，需要的力量值保存在下标从 0 开始的整数数组 tasks 中，第 i 个任务需要 tasks[i] 的力量才能完成。每个工人的力量值保存在下标从 0 开始的整数数组 workers 中，第 j 个工人的力量值为 workers[j] 。每个工人只能完成 一个 任务，且力量值需要 大于等于 该任务的力量要求值（即 workers[j] >= tasks[i] ）。

除此以外，你还有 pills 个神奇药丸，可以给 一个工人的力量值 增加 strength 。你可以决定给哪些工人使用药丸，但每个工人 最多 只能使用 一片 药丸。

给你下标从 0 开始的整数数组tasks 和 workers 以及两个整数 pills 和 strength ，请你返回 最多有多少个任务可以被完成。

- n == tasks.length
- m == workers.length
- 1 <= n, m <= 5 * 104
- 0 <= pills <= m
- 0 <= tasks[i], workers[j], strength <= 109

分析：前面写炸了因此比赛时没来得及看，首先，答案显然具有**单调性**，进一步地，如果可以完成x个任务，那么**x个最强壮的工人嗑药一定可以完成x个最弱的任务**，反之如果x个最强壮的工人嗑药无法完成x个最弱的任务，则其它方案必然也无法满足条件。
因此确定主体框架：可以先分别对工人和任务排序，然后二分答案。  
二分最关键的是check函数的实现，可以**从大到小**考虑每一个任务，对于每一个任务首先找不嗑药满足要求的最弱工人，如果没有，则寻找一个嗑药能刚好满足要求的最弱工人
复杂度：check函数复杂度为$nlogn$, 二分答案复杂度为$logn$,整体复杂度为$nlogn^2$。  

参考代码：  
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

赛后总结：需要增加模拟题的训练，模拟题写炸不是一次两次，另外手速需要继续训练，按键键位总错，尤其右手小指，最后一题难度目前来说有些高，有时间或许能想出来。