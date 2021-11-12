---
title: pmr优化各类stl
categories:
- C++知识
tags:
- 常数优化
- stl
---

stl各类容器慢的一批，尤其是关联式or无序关联式容器，可能是由于动态申请空间的原因，因此可以预先分配并指定资源池来提高速度。

```
//资源池
static inline byte buffer [1 << 30];
static inline pmr:: monotonic_buffer_resource pool {data(buffer), size(buffer)};

//对关联式容器or无序关联式容器优化， (unordered_)set/map 以及 mutiset/multimap等,作用极其明显
pmr:: unordered_set<T> st {&pool};
pmr:: unordered_map<T1, T2> mp {&pool}

//优化序列式容器以及相应的容器适配器主要是vector/deque/queue/stack/priority_queue,效果比较一般
pmr::vector<int> dp {&pool};
//声明长度的,实际上之前的{}写成()也可以
pmr::vector<int> dp (n, &pool);
//二维vector
pmr::vector<pmr::vector<int>> dp {&pool}
//二维m x n：
pmr::vector<pmr::vector<int>> dp (m, pmr::vector<int> (n, 0), &pool);

//deque
pmr:: deque<int> dq {&pool}

//三个适配器的写法
//queue
auto q = queue {pmr::deque<int> {&pool}};

//stack
stack<int, vector<int>> st; //使用vector实现stack
auto st = stack {pmr:: vector<int> {&pool}};

//priority_queue
auto q = priority_queue{greater{},vector<T>{}}; //优先队列一种定义方式
auto pq = priority_queue{greater{},pmr:: vector<T>{&pool}};
auto pq = priority_queue <T, pmr:: vector<T>, greater<>> {&pool};
```

声明的时候都需要搞个{&pool}初始化，实际上可以通过一行代码简化写法，如下：

```
#include <bits/stdc++.h>
using namespace std;

inline byte buffer [1 << 30];
inline pmr:: monotonic_buffer_resource pool {data(buffer), size(buffer)};

int main () {
	set_default_resource(&pool);
	pmr:: set<int> st;
	//具体操作
	return 0;
}
```

最后，以set为例，注意pmr::set和普通set不是一个东西，没法直接转化，举例说明，是leetcode如果要求返回一个vector，如果返回一个pmr:: vector就不行