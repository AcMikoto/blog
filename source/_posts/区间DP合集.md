---
title: 区间DP合集
toc: true
tags:
  - LeetCode
  - 提高计划
  - 区间DP
  - 记忆化搜索
categories:
  - - LeetCode
    - 提高计划
date: 2021-11-21 19:27:50
updated:
---
<!--more-->
# LeetCode区间DP合集

## 概述
区间DP是指在一段区间上进行动态规划，一般做法是由长度较小的区间向长度较大的区间进行递推，最终得到整个区间的答案。自底向上的动态规划时，通常做法比较固定，先枚举区间长度，再枚举区间左端点，随后枚举区间分割点进行状态转移，时间复杂度多为$O(N^3)$。一般来说自顶向下的记忆化搜索写起来更加方便，思路更为自然，更推荐使用记忆化搜索的方式解此类问题。   

结合LeetCode上几道区间DP的题练习。

## [486. 预测赢家](https://leetcode-cn.com/problems/predict-the-winner/)  

### 思路
博弈类游戏，这类问题有一个特点就是到达某个确定的区间时此时轮到谁选是确定的，一个比较通用的做法是，定义方案f[i][j]为到达区间[i, j]时，当前先手者与后手者的得分最大差值。因此，可以推出状态转移方程为$f[i][j] = max(nums[i] - f[i + 1][j], nums[j] - f[i][j - 1])$，采用记忆化搜索的方式解决。  
### Code
```
class Solution {
public:
    int f[20][20];
    bool PredictTheWinner(vector<int>& nums) {
        int n = size(nums);
        auto dfs = [&](auto&& dfs, int l, int r) {
            if(l > r) return 0;
            if(f[l][r]) return f[l][r];
            auto&& res = f[l][r];
            res = max(nums[l] - dfs(dfs, l + 1, r), nums[r] - dfs(dfs, l, r - 1));
            return res;
        };
        return dfs(dfs, 0, n - 1) >= 0;
    }
};
```
### 复杂度分析
二维状态，每次转移复杂度$O(1)$
- 时间复杂度$O(N^2)$
- 空间复杂度$O(N^2)$
----

## [312. 戳气球](https://leetcode-cn.com/problems/burst-balloons/)  

### 思路
正常处理有一些麻烦，因为戳掉一个气球之后数组长度以及下标会发生变化，难以处理。可以保持数组下标不动，枚举(l,r)区间内最后一次戳掉气球的位置，状态转移方程$f[i][j] = max(f[i][k - 1] + f[k + 1][j] + nums[i] * nums[k] * nums[j]), 其中k \in (i, j);$，为了方便处理，可以在两头分别添加一个哨兵，采用记忆化搜索的方式解决。  
### Code
```
class Solution {
public:
    int f[510][510];
    int maxCoins(vector<int>& nums) {
        vector<int> a {1};
        for(auto&& i: nums) a.push_back(i);
        a.push_back(1);
        auto dfs = [&] (auto&& dfs, int l, int r) -> int {
            if(r - l <= 1) return 0;
            if(f[l][r]) return f[l][r];
            int& res = f[l][r];
            for(int i = l + 1; i < r; i ++) {
                res = max(res, a[l] * a[i] * a[r] + dfs(dfs, l, i) + dfs(dfs, i, r));
            }
            return res;
        };
        return dfs(dfs, 0, nums.size() + 1);
    }
};
```
### 复杂度分析
二维状态，每次转移复杂度$O(N)$
- 时间复杂度$O(N^3)$
- 空间复杂度$O(N^2)$
----

## [664. 奇怪的打印机](https://leetcode-cn.com/problems/strange-printer/)  

### 思路
对于字符串????????，可以寻找分界点将串分为左右两段非空字符串，整串的打印次数为两段之和，但我们漏掉了一种情况，即当当前串首尾两端相等时，可以在打印时通过先打印一整串相同字符来将首尾两个字符同时打印出来，整体打印次数减1。（这里注意不是当分开的两段左字符串尾和右字符串首相等时，这种情况通过分界点的枚举一定可以包含，分成左右非空两段字符串，漏掉的情况就是原字符串首尾相等时的方案），参考代码如下：
### Code
```
class Solution {
public:
    int f[105][105];
    int strangePrinter(string s) {
        int n = size(s);
        memset(f, 0x3f, sizeof f);
        auto dfs = [&](auto&& dfs, int l, int r) {
            if(l >= r) return 1;
            if(f[l][r] != 0x3f3f3f3f) return f[l][r];
            auto&& res = f[l][r];
            for(int k = l; k < r; k ++) {
                res = min(res, dfs(dfs, l, k) + dfs(dfs, k + 1, r) - int(s[l] == s[r]));
            }
            return res;
        };
        return dfs(dfs, 0, n - 1);
    }
};
```
### 复杂度分析

- 时间复杂度$O(N^3)$
- 空间复杂度$O(N^2)$
----

## [546. 移除盒子](https://leetcode-cn.com/problems/remove-boxes/)  

### 思路
本题思维难度较大，并未自主解决，参考了状态定义。  
初看此题，容易陷入误区，即定义f[i][j]为[i，j]区间内能够获得的最大积分和，但是[i,j]区间内的值能获得的最大区间和依赖于其它区间的情况，比如当前区间的某个值可以和其它区间的相同值连起来获得更高的分数。因此这个状态是无法计算的，它依赖于后续状态。   
对于移除盒子这个问题，不难想到，我们需要凑一些尽可能长的同色盒子消除以获得更高的分数，我们不妨每次都选择消区间最后一个盒子（通过区间划分，每次消最后一个盒子也能不漏的包含所有情况），记录一下最后一个盒子以及其后面组成的同色盒数量。状态定义为f[l,r,k]，其中k为经过一顿消除，攒下来的和boxs[r]同色的盒子数量（这里选择包括boxs[r]，其实都行），那么根据我们的状态定义，下一步操作可以分为几种情况：
- 选择直接消掉最后一个盒子及攒下的同色盒子，f[l][r][k] = f[l][r - 1][1] + k * k;
- 在区间中寻找与最后一个盒子同色的盒子boxs[mid]，然后消除掉其与最后一个盒子的中间段，继续攒同色盒子f[l][r][k] = f[l][mid][k + 1] + f[mid + 1][r - 1][1]

最后我们取一个最大值即可，采用记忆化搜索的方式解决，参考代码如下：
### Code
```
class Solution {
public:
    static const int N  = 105;
    int f[N][N][N];
    int removeBoxes(vector<int>& nums) {
        int n = nums.size();
        auto dfs = [&] (auto&& dfs, int l, int r, int k) {
            if(l > r) return 0;
            if(f[l][r][k]) return f[l][r][k];
            auto&& res = f[l][r][k];
            //直接把攒的都消掉
            res = dfs(dfs, l, r - 1, 1) + k * k;
            for(int i = l; i < r; i ++) {
                if(nums[i] == nums[r]) {
                    //继续攒
                    res = max(res, dfs(dfs, l, i, k + 1) + dfs(dfs, i + 1, r - 1, 1));
                }
            }
            return res;
        };
        return dfs(dfs, 0, n - 1, 1);
    }
};
```
### 复杂度分析
三维状态，单次转移复杂度$O(N)$  
- 时间复杂度$O(N^4)$
- 空间复杂度$O(N^3)$
----

---
## [730. 统计不同回文子序列](https://leetcode-cn.com/problems/count-different-palindromic-subsequences/)

### 思路
显然是区间dp的问题，相比于一般的统计回文子序列问题，主要难点在于判重。在传统区间dp的状态之外，额外增加一维，将区间状态定义为f[i][j][k],代表区间[i, j]能够组成的两端为字母为k的不同的回文子序列个数。显然当k不同时，[i, j]区间组成的回文子序列互不相同，当s[i]-'a'不等于k时，当前左端点对子序列无贡献，则f[i][j][k] = f[i + 1][j][k]同理可以对右端点作相同考虑。当左右端点都等于'a' + k时，与三数之和去重的思路类似，优先考虑采用左右端点的字母，当左右端点字母都用过才去考虑用更里面一层的相同字母。具体见代码注释。  

### code
```
const int mod = 1e9 + 7, N = 1010;
int f[N][N][4];
class Solution {
public:
    int countPalindromicSubsequences(string s) {
        int n = s.size();
        for(int len = 1; len <= n; len ++) {
            for(int st = 0; st + len - 1 < n; st ++) {
                int ed = st + len - 1;
                for(int k = 0; k < 4; k ++) {
                    auto&& u = f[st][ed][k];
                    //实测在这里初始化可以避免每次都初始化一个巨大数组带来的开销，显著减少运行时间
                    if(len == 1) u = int(s[st] == 'a' + k);
                    else {
                        //如果左端点不是当前元素，则两端元素为k的回文子序列不可能以它开头
                        if(s[st] != 'a' + k) u = f[st + 1][ed][k];
                        else if(s[ed] != 'a' + k) u = f[st][ed - 1][k];
                        else {
                            // u = 2是两个端点可以形成的长度为1和2的回文子序列
                            u = 2;
                            // 两个端点 + 中间的回文子序列组成的新的回文子序列
                            for(int t = 0; t < 4; t ++) {
                                u += f[st + 1][ed - 1][t];
                                u %= mod;
                            }
                        }
                    }
                }
            }
        }
        int res = 0;
        for(int i = 0; i < 4; i ++) {
            res += f[0][n - 1][i];
            res %= mod;
        }
        return res;
    }
};
```
### 复杂度分析
忽略常数的话两重循环$O(N^2)$，考虑常数时的最坏复杂度$O(N^2K^2)$,其中K为字符种类个数
- 时间复杂度$O(N^2)$
- 空间复杂度$O(N^2)$

---

## [1000. 合并石头的最低成本](https://leetcode-cn.com/problems/minimum-cost-to-merge-stones/)

### 思路

首先，每次合并K个石头，显然无解情况是可以特判的，合并一次相当于石头减少$K-1$个，若石头总量模$K-1$余1，此时必然能合并，否则不能合并成一堆，但我们发现一个特例，就是每次合并2个石头，$K-1=1$，一个常用的技巧是先将总数$n$减1，然后判断$(n - 1)\%(k - 1)$是否为0即可，至此，完成了一定数量的石头是否能合并成一堆的判断。

另外，进一步地，如果我们石头合并到尽可能堆数少，那么对于一个区间$[i, j]$其合并后的最少堆数一定是确定的,因此从可以定义状态f(i, j)定义为将$[i,j]$区间合并成最少堆数时的花费。状态定义完后思考状态之间的转移：  

先不考虑合并，假设[i, j]区间最终能加成x堆，不妨设它总是由合并成1堆的左边f[i, mid](mid = 1 + (k - 1) * N)和右边堆累加而来。因此有$x \in [2, k]$, （同样的通过区间划分，也能包含所有的情况）每当累计到K堆时我们最后合并一次。那么有：
$f[i][j] = min(f[i][j], f[i][mid] + f[mid + 1][j])$

具体见代码及注释   

### code

```
class Solution {
public:
    int mergeStones(vector<int>& stones, int k) {
        int n = stones.size();
        //特判无解
        if((n - 1) % (k - 1)) return -1;
        const int N = 35;
        int f[N][N] {}, sum[N] {};
        //这个前缀和属于石子合并常用技巧了
        for(int i = 0; i < n; i ++) {
            sum[i + 1] = sum[i] + stones[i];
        }
        auto dfs = [&](auto&& dfs, int l, int r) {
            //区间长度为1时初始化
            if(l == r) return 0;
            if(f[l][r]) return f[l][r];
            auto&& res = f[l][r] = 0x3f3f3f3f;
            //划分为左边一堆和右边剩余堆，当然划分为右边一堆和左边剩余堆也正确
            for(int mid = l; mid < r; mid += k - 1) {
                res = min(res, dfs(dfs, l, mid) + dfs(dfs, mid + 1, r));
            }
            //当可以合并时，合并一下
            if((r - l) % (k - 1) == 0) res += sum[r] - sum[l - 1];
            return res;
        };
        return dfs(dfs, 1, n);
    }
};
```
### 复杂度分析
二维状态，每次转移复杂度最坏O(N)
- 时间复杂度$O(N^3)$
- 空间复杂度$O(N^2)$

---

----
**欢迎讨论指正**