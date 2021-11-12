---
title: SPFA算法之SLF优化
categories:
- 算法拾碎
tags:
- 常数优化
- SPFA算法
- SLF优化
---

由于众所周知的原因，SPFA作为一个理论很快的算法实际上被卡到生活不能自理，但以目前的所学的知识而言，负权图情况下的最优解还是写SPFA，但是最近发现部分负权图，它也卡SPFA！！！！
这里就记录一个很简单的优化方法，SLF优化（酸辣粉优化），可以一定程度上（约20%）改善spfa的运行速度，优点是相较于其它优化方法，SLF优化非常好写，和朴素SPFA差异很小，具体结合例题说明：

[Acwing342.道路与航线](https://www.acwing.com/activity/content/problem/content/1501/)
![](/img/imgPost/负权图卡spfa的例题.png)

题目中提到不含负环，因此最直观的想法是跑一遍spfa，但是问题就在于数据卡掉了朴素版spfa算法，因此我们可以考虑加上slf优化。

朴素spfa算法是对bellman_ford算法的队列优化，松弛的时候采用一个普通队列，从而改善了bellman_ford算法不管某个边是否被松弛过，无脑判断一遍的暴力做法。

而slf优化，在某个点加入队列时判断一下当前待加入元素与起点的距离与队头元素与起点的距离大小关系，如果队列不为空，且当前元素距离比队头距离要小，那么将当前元素加入队头，否则加入队尾。这一点其实和dijkstra算法的思想有一点相似，dijkstra算法每次贪心地用当前最短距离去更新其他点，而slf优化则是在每一次松弛后用相对最小距离点插入队头，然后用该点的距离去更新其它点。实际算法实现只需要将普通队列替换为双端队列，写法类似spfa和双端队列广搜的结合版，参考代码如下：

```
#include <bits/stdc++.h>
using namespace std;

const int N = 2e5;
int h[N], e[N], w[N], ne[N], idx;
void add(int a, int b, int c) {
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}
int dist[N];
bool st[N];

int t, r, p, s;
int main () {
    memset(h, -1, sizeof h);
    memset(dist, 0x3f, sizeof dist);
    cin >> t >> r >> p >> s;
    while(r --) {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        add(a, b, c), add(b, a, c);
    }
    while(p --) {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        add(a, b, c);
    }
    //双端队列
    deque<int> q;
    q.push_back(s);
    dist[s] = 0;
    while(q.size()) {
    		//取出队头元素且弹出
        auto u = q.front();
        q.pop_front();
        st[u] = false;
        for(int i = h[u]; i != -1; i = ne[i]) {
            int j = e[i];
            if(dist[u] + w[i] < dist[j]) {
                dist[j] = dist[u] + w[i];
                if(!st[j]) {
                    st[j] = true;
                    //队列不为空，且当前元素距离小于队头，则加入队头，否则加入队尾
                    if(q.size() && dist[j] < dist[q.front()]) q.push_front(j);
                    else q.push_back(j);
                }
            }
        }
    }
    for(int i = 1; i <= t; i ++) {
        if(dist[i] == 0x3f3f3f3f) puts("NO PATH");
        else printf("%d\n", dist[i]);
    }
    return 0;
}
```

