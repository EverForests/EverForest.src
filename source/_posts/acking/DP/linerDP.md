---
title: 线性DP
tags: [动态规划]
categories: 'acking'
description: ''
cover: 'https://raw.githubusercontent.com/EverForests/PicGo/master/wallhaven-yx3w57.png'
date: 2024-11-22 14:33:03
keywords:
---

## 数字三角形 [Go](https://www.acwing.com/problem/content/900/)

基于实体逻辑的动态规划称为线性DP，采用闫氏DP分析法，自下而上（这样每点的后继是有限的，状态可解），设终点为点K的最大路径数字和为f[i][j]（因为有两个维度）。基于这样的假设，得出转移方程，f[1][1]就是目标结果。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

const int N = 510;
int f[N][N];
int n;

using namespace std;

int main () {
    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= i; j++)
            cin >> f[i][j];
  
    for (int i = n; i; i--)
        for (int j = 1; j <= i; j++)
            f[i][j] = max(f[i+1][j], f[i+1][j+1]) + f[i][j];
  
    cout << f[1][1] << endl;
  
    return 0;
}
```

## 最长上升子序列 [Go](https://www.acwing.com/problem/content/897/)

给定一个序列，求数值严格递增的最大长度子序列。

构造，以序列中第i个数结尾的子序列中最大长度为f[i]，对于第k个数，在计算f[k]时，需要考虑的是以第1～k-1个数结尾的子序列中的最大长度，遍历取最大值即可。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

const int N = 1010;
int n;
int a[N], f[N];

using namespace std;

int main()
{
    cin >> n;
    for (int i = 1; i <= n; i++) cin >> a[i];
  
    for (int i = 1; i <= n; i++) {
        f[i] = 1;
        for (int j = 1; j < i; j++){
            if (a[i] > a[j]) f[i] = max(f[i], f[j] + 1);
        }
    }
  
    int res = 0;
    for (int i = 1; i <= n; i++) res = max(res, f[i]);
  
    cout << res << endl;
}
```

## 最长上升子序列强化版 [Go](https://www.acwing.com/problem/content/898/)

在数据量较大时，基础的多重循环解法已不再适用。这里有一种特殊的考虑，找到每种长度上升子序列的最小末尾值f[i]，利用反证法可以验证f也为严格上升序列，这样只需要遍历原序列中的每一个数j，利用二分法找到f序列中小于j的最大数的位置，加入j不断更新f序列，最终f序列的长度就是原序列中最长的上升子序列。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 100010;
int f[N], a[N];
int n;

int main()
{
    cin >> n;
    f[0] = -2e9;
    for (int i = 0; i < n; i++) cin >> a[i];
  
    int len = 0;
    for (int i = 0; i < n; i++) {
        int l = 0, r = len;
        while (l < r) {
            int mid = (l + r + 1) >> 1;
            if (f[mid] < a[i]) l = mid;
            else r = mid - 1;
        }
      
        f[r + 1] = a[i];
        len = max(len, r+1);
    }
  
    cout << len << endl;
  
    return 0;
}
```

