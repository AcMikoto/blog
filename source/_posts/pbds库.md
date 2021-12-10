---
title: pbds库
toc: true
tags:
  - pbds
categories:
  - C++知识
date: 2021-12-10 21:30:14
updated:
---
<!--more-->

# 概述
[pb_ds](https://gcc.gnu.org/onlinedocs/libstdc++/ext/pb_ds/)库是g++编译器默认附带的一个扩展库，全称是Policy-Based Data Structures
pb_ds库中有许多比较有用的数据结构，可以代替stl，不仅功能上更为强大，且速度上也要更快一些。

## pb_ds库的引入
```
//万能pb_ds头文件
#include <bits/extc++.h>
using namespace std;
```

pb_ds库主要常用的数据结构有三种，分别是[Hash Table](https://gcc.gnu.org/onlinedocs/libstdc++/ext/pb_ds/hash_based_containers.html)、[Priority Queues](https://gcc.gnu.org/onlinedocs/libstdc++/ext/pb_ds/pq_design.html)以及[Tree](https://gcc.gnu.org/onlinedocs/libstdc++/ext/pb_ds/tree_based_containers.html),下面简单了解一下它们各自的特点和常见用法。  


## Hash Table
pb_ds库中提供两种跟unordered_map类似的哈希表，主要是处理冲突的方式不一样:   
cc_hash_table //拉链法哈希表
gp_hash_table //寻址法哈希表

两种方法的常数经个人测试不好比较（通常认为是gp快一些），有时cc快有时gp快，差距可能达到一倍，可以确定的是都比stl的快，一个超时时可以试试另外一个  

属于无序哈希表，使用上基本与unorderd_map基本一致，支持find，key值不支持pair等，特别不同的一点是不支持bitset，另外支持自定义哈希方法。  


## Priority Queue

声明：
```
template<
    typename Value_Type,
    typename Cmp_Fn = std::less<Value_Type>,
    typename Tag = pairing_heap_tag,
    typename Allocator = std::allocator<char> >
class priority_queue;
```
四个参数分别是存放元素类型、比较方法（默认为std::less）、tag（标识符？）可以理解为堆的类型以及分配器类型（这个通常不用管）
pb_ds库中提供5中tag：
- pairing_heap_tag(配对堆)
- binary_heap_tag(二叉堆)
- binomial_heap_tag(二项堆)
- rc_binomial_heap_tag(另一种二项堆)
- thin_heap_tag(某种斐波那契堆)

实测pairing_heap_tag(配对堆)的速度显著快于其它（也显著快于stl的优先队列），同时它也是默认tag参数，因此声明时通常不用改动；

pb_ds库中的优先队列常用成员方法如下：
- size() 返回堆内元素个数，同std
- empty() 返回堆是否为空，同std
- push(Value_Type v) 向堆内添加一个元素同时返回该元素所在的**迭代器位置**（point_iterator）
- top() 返回堆顶元素，同std
- pop() 弹出堆顶元素，同std
- point_iterator 对应某元素的迭代器
- erase(point_iterator it) 删除某个迭代器对应的点
- erase_if(Pred prd) 删除所有满足谓词(即对prd(v)返回true)的元素并返回被删除元素的数量
- modify(pointer_iterator it, const_reference r_new_val) 修改节点对应的值
- clear() 清空堆，同std
- join(priority_queue &other) 将另一个同类型堆合并到该堆，合并后other清空
- split(Pred prd, priority_queue &other) 将堆拆分为两个堆，堆中对prd(v)返回true的元素会全部分到other中，other需与本堆同类型

可以看到，pb_ds库中的堆支持修改，删除以及合并，拆分等，非常强大，而我们知道对于dijkstra算法，由于使用的是stl堆，堆中会储存许多的重复元素，增加了时间复杂度，因此可以考虑使用pb_ds库中的堆来实现dijkstra算法，达到其最优复杂度,以Acwing堆优化dijkstra算法模板题为例说明： 

### [Acwing 850.Dijkstra求最短路II](https://www.acwing.com/problem/content/description/852/)

一般dijkstra算法略去，直接上优化版代码：

```
#include <bits/stdc++.h>
using namespace std;
#include <bits/extc++.h>
using namespace __gnu_pbds;

const int N = 1.5e5 + 10, INF = 0x3f3f3f3f;
int n, m;
int dist[N];
vector<pair<int, int>> g[N];

int dijkstra() {
    // 命名空间不可省略，因为和std的同名。仅需要写到比较方法即可，tag默认最快的pairing_heap_tag
    __gnu_pbds:: priority_queue<pair<int, int>, greater<>> q;
    fill_n(dist, n + 1, INF);
    // 这里这么写可以省去长长的迭代器名，同时用id[1]标识是否在堆中，比较简单
    auto id = vector(n + 1, q.push({0, 1}));
    dist[1] = 0;
    while(q.size()) {
        auto [ud, u] = q.top();
        q.pop();
        for(auto&& [i, w]: g[u]) {
            if(dist[i] > dist[u] + w) {
                dist[i] = dist[u] + w;
                // 在堆中则修改，不在堆中则加入，同时记录一下节点编号和迭代器位置的对应关系
                if(id[i] != id[1]) q.modify(id[i], {dist[i], i});
                else id[i] = q.push({dist[i], i});
            }
        }
    }
    return dist[n];
}

int main () {
    cin >> n >> m;
    while(m --) {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        g[a].push_back({b, c});
    }
    int res = dijkstra();
    if(res == INF) puts("-1");
    else printf("%d\n", res);
    return 0;
}
```

对于这个题而言速度快大约300~500ms， 可能重复入堆操作不够多，总之超时的话可以试试这个方法压一压常数，同时这个维护可删除堆的方法可以推广。  

## Tree
声明：
```
template<
    typename Key,
    typename Mapped,
    typename Cmp_Fn = std::less<Key>,
    typename Tag = rb_tree_tag,
    template<
        typename Const_Node_Iterator,
        typename Node_Iterator,
        typename Cmp_Fn_,
        typename Allocator_>
    class Node_Update = null_tree_node_update,
    typename Allocator = std::allocator<char> >
class tree;
```
- Key 是键类型
- Mapped 是映射方法，通常写null_type即可
- Cmp_Fn 是比较方法，默认为less
- tag 是内部采用的树类型，有rb_tree_tag, splay_tree_tag, ov_tree_tag，默认是红黑树，后两个非常慢，不推荐，了解即可
- Node_update 是节点更新方式，默认没有，常用的是tree_order_statistics_node_update（使用order_of_key和find_by_order需要）
- Allocator 分配器类型

常用成员方法:
- insert(x) 向树中插入一个元素x，并返回一个pair<point_iterator, bool>
- erase(x) 从树中删除一个元素or迭代器，返回一个bool表明是否删除成功
- order_of_key(x) 返回x以Cmp_Fn比较的排名（从0开始）
- find_by_order(x) 返回以Cmp_Fn比较的排名所对应元素的迭代器
- lower_bound(x) 以Cmp_Fn比较做lower_bound，返回迭代器
- upper_bound(x) 以Cmp_Fn比较做upper_bound, 返回迭代器
- join(x) 将x树并入当前树，前提是两棵树类型一致，x树被删除
- split(x, b) 以Cmp_Fn比较，大于x的拆分到b树
- empty() 返回是否为空
- size() 返回大小


注意Tree中不允许有重复值，常用的方法是存一个pair，第一个元素为真实值，第二个元素为时间戳，具体用Acwing普通平衡树例题来说明：

### [Acwing 253.普通平衡树](https://www.acwing.com/problem/content/255/)

```
#include <bits/stdc++.h>
using namespace std;
#include <bits/extc++.h>
using namespace __gnu_pbds;
tree<pair<int, int>, null_type, less<>, rb_tree_tag, tree_order_statistics_node_update> tr;

int main () {
    int n;
    cin >> n;
    for(int i = 0; i < n; i ++) {
        int op, x;
        scanf("%d%d",&op, &x);
        if(op == 1) {
            tr.insert({x, i});
        }
        else if(op == 2) {
            // 题目保证操作合法，即删除时存在该元素，否则需要判断一下返回迭代器对应的第一个元素是否为x（因为可能x不在里头，删到了第一个比它大的）
            tr.erase(tr.lower_bound({x, 0}));
        }
        else if(op == 3) {
            printf("%d\n", tr.order_of_key(*tr.lower_bound({x, 0})) + 1);
        }
        else if(op == 4) {
            printf("%d\n", tr.find_by_order(x - 1) -> first);
        }
        else if(op == 5) {
            printf("%d\n", prev(tr.lower_bound({x, 0})) -> first);
        }
        else {
            printf("%d\n", tr.upper_bound({x, n}) -> first);
        }
    }
}
```

从声明上看，进阶用法可以自定义Node_Update从而高度定制这个Tree，后续有空再学习。
最大的难点可能在于记住tree的声明方式，很长，目前没啥好办法简化。

----

----
**欢迎讨论指正**