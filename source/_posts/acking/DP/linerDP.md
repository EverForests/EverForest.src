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

## 最长公共子序列 [Go](https://www.acwing.com/problem/content/899/)

考虑序列A的前i项，序列B的前j项，讨论每组情况下的最长公共子序列。

转移方程是难点所在，需要对三种情况进行分析。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

const int N = 1010;
int f[N][N];
int n, m;
char a[N], b[N];

using namespace std;

int main()
{
    scanf("%d%d", &n, &m);
    scanf("%s%s", a+1, b+1);
  
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++) {
            f[i][j] = max(f[i-1][j], f[i][j-1]);
            if (a[i] == b[j]) f[i][j] = max(f[i][j], f[i-1][j-1] + 1);
        }
  
    cout << f[n][m] << endl;
    return 0;
}
```

## 最短编辑距离 [Go](https://www.acwing.com/problem/content/904/)

改变f[i][j]的内涵即可，其余同最长公共子序列，每一步遍历增删换三个方法。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1010;
int n, m;
int f[N][N];
char a[N], b[N];

int main()
{
    scanf("%d%s", &n, a + 1);
    scanf("%d%s", &m, b + 1);
  
    for (int i = 1; i <= n; i++) f[i][0] = i;
    for (int j = 1; j <= m; j++) f[0][j] = j;
  
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++) {
            f[i][j] = min(f[i-1][j] + 1, f[i][j-1] + 1);  // 考虑增删
            if (a[i] == b[j]) f[i][j] = min(f[i][j], f[i-1][j-1] );  // 考虑无需操作
            else f[i][j] = min(f[i][j], f[i-1][j-1] + 1);  // 考虑换
        }
      
    cout << f[n][m] << endl;
  
    return 0;
}
```

## 编辑距离 [Go](https://www.acwing.com/problem/content/901/)

难点主要在于写法，关键算法同上面的最短编辑距离。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1010;
char a[N][N];
int n, m;

int edit_distance(char sa[], char sb[]) {
    int la = strlen(sa + 1);
    int lb = strlen(sb + 1);
  
    int f[N][N];
  
    for (int i = 1; i <= la; i++) f[i][0] = i;
    for (int j = 1; j <= lb; j++) f[0][j] = j;
  
    for (int i = 1; i <= la; i++)
        for (int j = 1; j <= lb; j++) {
            f[i][j] = min(f[i-1][j] + 1, f[i][j-1] + 1);
            f[i][j] = min(f[i][j], f[i-1][j-1] + (sa[i] != sb[j]));
        }
      
    return f[la][lb];
}

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; i++) scanf("%s", a[i]+1);
  
    while (m--) {
        char b[N];
        int res = 0;
        int limit;
      
        scanf("%s%d", b + 1, &limit);
      
        for (int i = 1; i <= n; i++) {
            if (edit_distance(a[i], b) <= limit) res++;
        }
      
        cout << res << endl;
    }
  
    return 0;
}
```
