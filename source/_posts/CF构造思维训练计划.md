---
title: CF构造思维训练计划
toc: true
tags:
  - 常用算法
  - 构造
categories:
  - - 常用算法
date: 2022-03-22 20:44:59
updated:
---
<!--more-->

## 概述
主要是在CodeForces 分数为1700左右， tag带有constructive algorithms标签的题，训练一番思维， 暂定是每日三题。

## 2022-03-22

### [1360F - Spy-string](https://codeforces.com/problemset/problem/1360/F)
### 题意简述
给几个字符串，寻找是否存在一个字符串使得它与每个字符串对应位置字符不同位置不超过1个

### 分析
由于数据范围小，字符串数量不超过10，且字符串长度不超过10，选择任意一个字符串随后暴力枚举所有与它不同位置不超过一个的字符串即可。

### Code
```
/*head*/
#include <bits/stdc++.h>
using namespace std;
typedef long long ll; typedef unsigned long long ull; typedef pair<int, int> pii;
#define fi first
#define se second
#define pb push_back
#define all(v) begin(v),end(v)
#define mst(a,v) memset(a,v,sizeof(a))
#define mcp(a,v) memcpy(a,v,sizeof(a));
#define bitcnt(a) __builtin_popcount(a)
#define rep(i,j,k) for(int i=int(j);i<=int(k);i++)
#define per(i,j,k) for(int i=int(j);i>=int(k);i--)
#define subset(i,a) for(int i=a;i;i=a&i-1) 
#define cnr(i,n,r) for(int i=(1<<r)-1,_,a=1<<n;i<a;_=i+(i&-i),i=(i&~_)>>__builtin_ffs(i)|_)
#define debug(x...) cout<<#x<<" -> ",Err(x)
const int MOD = 1e9 + 7, INF = 0x3f3f3f3f;
void Err() {cout<<'\n';}
template<class T, class... Ts>
void Err(const T& A,const Ts&... As){cout<<A<<" ";Err(As...);}
const int dir[][2] {{0,1},{1,0},{0,-1},{-1,0},{1,1},{-1,1},{1,-1},{-1,-1}};
template <class T>inline bool chmax(T& a, const T& b){return a<b?a=b,1:0;}
template <class T>inline bool chmin(T& a, const T& b){return a>b?a=b,1:0;}

const int N = 12;
string a[N];
int n, m;

bool check(string& s) {
    rep(i, 1, n - 1) {
        int u = 0;
        rep(j, 0, m - 1) {
            if(a[i][j] != s[j]) {
                if(u) return false;
                u ++;
            }
        }
    }
    return true;
}
void solve() {
    cin >> n >> m;
    rep(i, 0, n - 1) cin >> a[i];
    string res = a[0];
    rep(i, 0, m - 1) {
        rep(j, 0, 25) {
            res[i] = 'a' + j;
            if(check(res)) {
                cout << res << endl;
                return;
            }
        }
        res[i] = a[0][i];
    }
    cout << -1 << endl;
}

int main()
{
    int t = 1;
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    cin >> t;
    while(t --) solve();
    return 0;
}
```

### 复杂度分析

- 时间复杂度$O(26MN^2)$
- 空间复杂度$O(N)$

### [1325D - Ehab the Xorcist](https://codeforces.com/contest/1325/problem/D)

### 题意简述
给2个整数，u，v找到一个最短的数组使得数组元素的异或和等于u，数组元素之和等于v，如果不存在返回-1

### 分析

先考虑无解情况，若u > v，由于异或和（不进位加法）肯定小于等于加法和，因此此种情况无解。
若u和v奇偶性不同，假设有解，考虑数组中元素的二进制最后一位为1的数量

若数量为奇数，则u必然为奇数，v也必然为奇数
若数量为偶数，则u必然为偶数，v也必然为偶数

因此无解情况就是u > v 或者是u v奇偶性不同时无解。

当u == v时，如果都为0， 空数组即可，输出长度为0， 若不为零，则包含一个元素为u的数组即可。

考虑u < v且奇偶性相同的情况
令x = (v - u) / 2， 那么显然我们可以找到一组 u x x 满足条件，特别地当u 为 0时， x x即满足条件。

数组长度至多为3，考虑何时能找到长度为2的数组满足条件，等价于问题：是否存在a，b使得a ^ b = u, a + b = v成立

由于等式：
**a + b = (a ^ b) + 2 (a & b)**
那么有x = a & b

若x二进制位数中某位是1，那么a和b对应的位必然是1，则u对应的位必然是0
若x二进制中某位是0，则u对应的位可能为0或者1
因此 若 x & u不为0，则必然存在x和u同为1的位，因此是不可能存在这样的a和b
若x & u等于0，那么根据前面的等式，有x + u == x ^ u，因此数组[x + u, x]就是满足条件的长度为2的解。


### Code
```
/*head*/
#include <bits/stdc++.h>
using namespace std;
typedef long long ll; typedef unsigned long long ull; typedef pair<int, int> pii;
#define fi first
#define se second
#define pb push_back
#define all(v) begin(v),end(v)
#define mst(a,v) memset(a,v,sizeof(a))
#define mcp(a,v) memcpy(a,v,sizeof(a));
#define bitcnt(a) __builtin_popcount(a)
#define rep(i,j,k) for(int i=int(j);i<=int(k);i++)
#define per(i,j,k) for(int i=int(j);i>=int(k);i--)
#define subset(i,a) for(int i=a;i;i=a&i-1) 
#define cnr(i,n,r) for(int i=(1<<r)-1,_,a=1<<n;i<a;_=i+(i&-i),i=(i&~_)>>__builtin_ffs(i)|_)
#define debug(x...) cout<<#x<<" -> ",Err(x)
const int MOD = 1e9 + 7, INF = 0x3f3f3f3f;
void Err() {cout<<'\n';}
template<class T, class... Ts>
void Err(const T& A,const Ts&... As){cout<<A<<" ";Err(As...);}
const int dir[][2] {{0,1},{1,0},{0,-1},{-1,0},{1,1},{-1,1},{1,-1},{-1,-1}};
template <class T>inline bool chmax(T& a, const T& b){return a<b?a=b,1:0;}
template <class T>inline bool chmin(T& a, const T& b){return a>b?a=b,1:0;}

ll u, v;

void solve() {
    cin >> u >> v;
    if(u > v || (v - u) % 2) {
        puts("-1");
        return;
    }
    if(u == v) {
        if(u) {
            printf("%d\n%lld\n", 1, u);
        } else {
            puts("0");
        }
        return;
    }
    ll x = (v - u) / 2;
    if((x & u) == 0) {
        printf("%d\n", 2);
        printf("%lld %lld\n", u + x, x);
    } else {
        if(u) {
        printf("%d\n", 3);
        printf("%lld %lld %lld\n", u, x, x);
        } else {
            printf("%d\n", 2);
            printf("%lld %lld\n", x, x);
        }
    }
}

int main()
{
    int t = 1;
    while(t --) solve();
    return 0;
}
```
### 复杂度分析

- 时间复杂度$O(1)$
- 空间复杂度$O(1)$
----

### [546D - Soldier and Number Game](https://codeforces.com/problemset/problem/546/D)

### 题意简述
简化后既是要求给两个数a b 求 (a!) / (b!)的所有大于1的因子个数

### 分析
可以先打表求出每个数的大于1因子个数，用前缀和思想求b 到 a区间的总的大于1的因子个数即可。
打表时利用DP的方法，当前数的大于1的因子个数等于其除以其最小质因数得到的数的大于1的因子个数 加 1

求数的最小质因子可以用欧拉筛O(1)的求出

### Code
```
/*head*/
#include <bits/stdc++.h>
using namespace std;
typedef long long ll; typedef unsigned long long ull; typedef pair<int, int> pii;
#define fi first
#define se second
#define pb push_back
#define all(v) begin(v),end(v)
#define mst(a,v) memset(a,v,sizeof(a))
#define mcp(a,v) memcpy(a,v,sizeof(a));
#define bitcnt(a) __builtin_popcount(a)
#define rep(i,j,k) for(int i=int(j);i<=int(k);i++)
#define per(i,j,k) for(int i=int(j);i>=int(k);i--)
#define subset(i,a) for(int i=a;i;i=a&i-1) 
#define cnr(i,n,r) for(int i=(1<<r)-1,_,a=1<<n;i<a;_=i+(i&-i),i=(i&~_)>>__builtin_ffs(i)|_)
#define debug(x...) cout<<#x<<" -> ",Err(x)
const int MOD = 1e9 + 7, INF = 0x3f3f3f3f;
void Err() {cout<<'\n';}
template<class T, class... Ts>
void Err(const T& A,const Ts&... As){cout<<A<<" ";Err(As...);}
const int dir[][2] {{0,1},{1,0},{0,-1},{-1,0},{1,1},{-1,1},{1,-1},{-1,-1}};
template <class T>inline bool chmax(T& a, const T& b){return a<b?a=b,1:0;}
template <class T>inline bool chmin(T& a, const T& b){return a>b?a=b,1:0;}

const int N = 5000000 + 5;
vector<int> primes;
int min_primes[N];
int factors[N];
ll pre[N];

void init() {
    for(int i = 2; i < N; i ++) {
        if(min_primes[i] == 0) {
            primes.pb(i);
            min_primes[i] = i;
        }
        for(int j = 0; j < size(primes) && i * primes[j] < N; j ++) {
            min_primes[i * primes[j]] = primes[j];
            if(i % primes[j] == 0) break;
        }
    } 
    for(int i = 2; i < N; i ++) {
        factors[i] = factors[i / min_primes[i]] + 1;
        pre[i] = pre[i - 1] + factors[i];
    }
}



void solve() {
    int a, b;
    cin >> a >> b;
    cout << pre[a] - pre[b] << '\n';
}

int main()
{
    int t = 1;
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    cin >> t;
    init();
    while(t --) solve();
    return 0;
}
```

### 复杂度分析

- 时间复杂度$O(C)$
- 空间复杂度$O(C)$
----


## 2022-03-23

### [1393C - Pinkie Pie Eats Patty-cakes](https://codeforces.com/problemset/problem/1393/C)

### 题意简述
给一个数组，至少存在两个相同值，安排一种顺序使得相同值之间的距离尽可能大，输出该距离即可

### 分析
显然答案具有二段性，可以二分，根据该距离将数组分为m个桶，其中最后一个桶元素可能不满，对于每一个元素若重复了x次，则贪心的选择x个空位最多的桶放进去即可。

### Code
```
/*head*/
#include <bits/stdc++.h>
using namespace std;
typedef long long ll; typedef unsigned long long ull; typedef pair<int, int> pii;
#define fi first
#define se second
#define pb push_back
#define all(v) begin(v),end(v)
#define mst(a,v) memset(a,v,sizeof(a))
#define mcp(a,v) memcpy(a,v,sizeof(a));
#define bitcnt(a) __builtin_popcount(a)
#define rep(i,j,k) for(int i=int(j);i<=int(k);i++)
#define per(i,j,k) for(int i=int(j);i>=int(k);i--)
#define subset(i,a) for(int i=a;i;i=a&i-1) 
#define cnr(i,n,r) for(int i=(1<<r)-1,_,a=1<<n;i<a;_=i+(i&-i),i=(i&~_)>>__builtin_ffs(i)|_)
#define debug(x...) cout<<#x<<" -> ",Err(x)
const int MOD = 1e9 + 7, INF = 0x3f3f3f3f;
void Err() {cout<<'\n';}
template<class T, class... Ts>
void Err(const T& A,const Ts&... As){cout<<A<<" ";Err(As...);}
const int dir[][2] {{0,1},{1,0},{0,-1},{-1,0},{1,1},{-1,1},{1,-1},{-1,-1}};
template <class T>inline bool chmax(T& a, const T& b){return a<b?a=b,1:0;}
template <class T>inline bool chmin(T& a, const T& b){return a>b?a=b,1:0;}

const int N = 1e5 + 5;
int a[N], b[N], n;
unordered_map<int, int> cnt;

/*
1 7 1 6 4 4 6
1 4 6 7 1 4 6

分为x个桶（最后一个桶可能少）

上取整

*/
bool check(int mid) {
    int len = mid + 1;
    int m = (n + len - 1) / len;
    int last = n % len;
    if(last == 0) last = len;
    priority_queue<int> q;
    rep(i, 1, m - 1) q.emplace(len);
    q.emplace(last);
    for(auto&& [k, v]: cnt) {
        if(v > size(q)) return false;
        int idx = 0;
        rep(i, 1, v) {
            b[idx ++] = q.top() - 1;
            q.pop();
        }
        rep(i, 0, idx - 1) {
            if(b[i]) q.emplace(b[i]);
        }
    }
    return true;
}

void solve() {
    cin >> n;
    cnt.clear();
    rep(i, 1, n) cin >> a[i], cnt[a[i]] ++;
    sort(a + 1, a + 1 + n);
    int l = 0, r = n - 2;
    while(l < r) {
        int mid = l + r + 1 >> 1;
        if(check(mid)) l = mid;
        else r = mid - 1;
    }
    cout << l << endl;
}

int main()
{
    int t = 1;
    ios::sync_with_stdio(0), cin.tie(0);
    cin >> t;
    while(t --) solve();
    return 0;
}
```

### 复杂度分析

- 时间复杂度$O(NlogN^2)$
- 空间复杂度$O(N)$
----

### [1516C - Baby Ehab Partitions Again](https://codeforces.com/problemset/problem/1516/C)

### 题意简述
给定一个数组，最少删除几个元素可以使得该数组无法被分割为两个和相等的子数组

### 分析
首先考虑无解情况，若和为奇数，显然直接输出0，随后采用01背包的方法判断是否存在和为一半的解。
若无解则输出0

若有解
先考虑简单情况，若数组中存在一个奇数元素，则删除该元素即可

接下来考虑全是偶数的情况，全是偶数则可以将所有的数减半，不影响结果，一直整体减半到存在一个奇数，随后删除该奇数元素即可。

### Code
```
/*head*/
#include <bits/stdc++.h>
using namespace std;
typedef long long ll; typedef unsigned long long ull; typedef pair<int, int> pii;
#define fi first
#define se second
#define pb push_back
#define all(v) begin(v),end(v)
#define mst(a,v) memset(a,v,sizeof(a))
#define mcp(a,v) memcpy(a,v,sizeof(a));
#define bitcnt(a) __builtin_popcount(a)
#define rep(i,j,k) for(int i=int(j);i<=int(k);i++)
#define per(i,j,k) for(int i=int(j);i>=int(k);i--)
#define subset(i,a) for(int i=a;i;i=a&i-1) 
#define cnr(i,n,r) for(int i=(1<<r)-1,_,a=1<<n;i<a;_=i+(i&-i),i=(i&~_)>>__builtin_ffs(i)|_)
#define debug(x...) cout<<#x<<" -> ",Err(x)
const int MOD = 1e9 + 7, INF = 0x3f3f3f3f;
void Err() {cout<<'\n';}
template<class T, class... Ts>
void Err(const T& A,const Ts&... As){cout<<A<<" ";Err(As...);}
const int dir[][2] {{0,1},{1,0},{0,-1},{-1,0},{1,1},{-1,1},{1,-1},{-1,-1}};
template <class T>inline bool chmax(T& a, const T& b){return a<b?a=b,1:0;}
template <class T>inline bool chmin(T& a, const T& b){return a>b?a=b,1:0;}

const int N = 1e5 + 5;
int a[1010], n;
bool dp[N] {true};

void solve() {
    cin >> n;
    int sum = 0;
    rep(i, 1, n) cin >> a[i], sum += a[i];
    
    //和为奇数
    if(sum & 1) {
        puts("0");
        return;
    }
    
    int m = sum / 2;
    rep(i, 1, n) {
        per(j, m, a[i]) {
            if(dp[j - a[i]]) dp[j] = true;
        }
    }
    
    if(!dp[m]) {
        puts("0");
        return;
    }
    
    while([&]{
        rep(i, 1, n) if(a[i] & 1) return false;
        return true;
    }()) {
        rep(i, 1, n) a[i] /= 2;
    }
    
    //存在一个奇数元素
    rep(i, 1, n) {
        if(a[i] & 1) {
            printf("%d\n%d\n", 1, i);
            return;
        }
    }
    
    
    
    
    
    
}

int main()
{
    int t = 1;
    ios::sync_with_stdio(0), cin.tie(0);
    //cin >> t;
    while(t --) solve();
    return 0;
}
```

### 复杂度分析
整个算法复杂度最高的部分在01背包部分，最坏1e7次计算。

- 时间复杂度$O(NC)$
- 空间复杂度$O(C)$
----

### [484A - Bits](https://codeforces.com/problemset/problem/484/A)

### 题意简述
给两个数l， r 找到[l, r]中二进制数位中1最多的输出

### 分析

首先显然是l == r时没得选，输出l

不相等时，可以找到一个最大的b 使得$2^b - 1 \leq r$ 

若$2^b - 1 \geq l$ 那么它一定是满足条件的解，因为下一个比它1多的是$2^{b + 1} - 1$,根据我们b的定义，这个数一定大于r

若$2^b - 1 \lt l$则等价于说$2^b \leq l$ 那么也有$2^b \lt r$只要将l,r同时减去$2^b$找到同时缩小区间后的f(l - $2^b$, r - $2^b$)的解，然后将其高位置1，即加上$2^b$一定就是我们要的解。

### Code
```
/*head*/
#include <bits/stdc++.h>
using namespace std;
typedef long long ll; typedef unsigned long long ull; typedef pair<int, int> pii;
#define fi first
#define se second
#define pb push_back
#define all(v) begin(v),end(v)
#define mst(a,v) memset(a,v,sizeof(a))
#define mcp(a,v) memcpy(a,v,sizeof(a));
#define bitcnt(a) __builtin_popcount(a)
#define rep(i,j,k) for(int i=int(j);i<=int(k);i++)
#define per(i,j,k) for(int i=int(j);i>=int(k);i--)
#define subset(i,a) for(int i=a;i;i=a&i-1) 
#define cnr(i,n,r) for(int i=(1<<r)-1,_,a=1<<n;i<a;_=i+(i&-i),i=(i&~_)>>__builtin_ffs(i)|_)
#define debug(x...) cout<<#x<<" -> ",Err(x)
const int MOD = 1e9 + 7, INF = 0x3f3f3f3f;
void Err() {cout<<'\n';}
template<class T, class... Ts>
void Err(const T& A,const Ts&... As){cout<<A<<" ";Err(As...);}
const int dir[][2] {{0,1},{1,0},{0,-1},{-1,0},{1,1},{-1,1},{1,-1},{-1,-1}};
template <class T>inline bool chmax(T& a, const T& b){return a<b?a=b,1:0;}
template <class T>inline bool chmin(T& a, const T& b){return a>b?a=b,1:0;}

ll f(ll l, ll r) {
    if(l == r) return l;
    int lo = 0, hi = 63;
    while(lo < hi) {
        int mid = lo + hi + 1>> 1;
        if((1ll << mid) - 1 <= r) lo = mid;
        else hi = mid - 1; 
    }
    ll x = (1ll << lo) - 1;
    if(x >= l) return x;
    return f(l - x - 1, r - x - 1) + x + 1;
}

void solve() {
    ll l, r;
    cin >> l >> r;
    cout << f(l, r) << endl;
}
int main()
{
    int t = 1;
    ios::sync_with_stdio(0), cin.tie(0);
    cin >> t;
    while(t --) solve();
    return 0;
}
```

### 复杂度分析
函数f里头套了一个递归，每次减少一个2 ^ b最坏复杂度logM

- 时间复杂度$O(logM)$
- 空间复杂度$O(1)$
---
**欢迎讨论指正**
