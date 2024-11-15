---
title: "背包问题"
tags: [动态规划]
categories: "acking"
keywords:
description: "动态规划，路线布置"
cover: "https://raw.githubusercontent.com/EverForests/PicGo/master/wallhaven-85jq9k.jpg"
---

## 01背包 [go](https://www.acwing.com/activity/content/problem/content/997/)

问题简介：在一定体积的包里装入足够多的物品使得背包里的物品价值最大化。

解题思路：动态规划基本三步，构建模型、设置状态转移规则、做好状态初始化。先泛化问题，再得到问题的完全解。比如，我们要探讨体积V背包下N个物品的最大价值，就研究在任意体积v背包下任意n个物品的最大价值的"计算公式"，然后代入V和N就可以得到目标结果。

```c++
// 一般写法
#include <iostream>
#include <cstdio>
#include <algorithm>

using namespace std;

const int N = 1010;
int v[N], w[N], f[N][N];  // f数组记录的是j体积下前i个物品的最大价值

int main()
{
    int n, m;
    cin >> n >> m;  // 记录物品的个数和背包的容积
  
    for(int i = 1; i <= n; i++) cin >> v[i] >> w[i];  // 初始化物品体积和价值
  
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++)
        {
            if(j < v[i]) f[i][j] = f[i-1][j];
            else{
                f[i][j] = max(f[i-1][j], f[i-1][j-v[i]]+w[i]);
            }
        }
  
    cout << f[n][m] << endl;
    return 0;
}
```

> 由于我们最终所需要的结果是一个确定的值，所以可以优化掉数组的某个维度，可以理解为做更粗糙的分类。

```c++
// 将二维数组优化为一维数组后的写法
#include <iostream>
#include <cstdio>
#include <algorithm>

using namespace std;

const int N = 1010;
int v[N], w[N], f[N]; 

int main()
{
    int n, m;
    cin >> n >> m;  // 记录物品的个数和背包的容积
  
    for(int i = 1; i <= n; i++) cin >> v[i] >> w[i];  // 初始化物品体积和价值
  
    for(int i = 1; i <= n; i++)
    {
        for(int j = m; j >= v[i]; j--)  
            f[j] = max(f[j], f[j - v[i]] + w[i]);  // 这种写法相对于一般写法将一些无法更新的位置略过了
    } 
  
    cout << f[m] << endl;
    return 0;
}
```

## 完全背包问题 [go](https://www.acwing.com/activity/content/problem/content/998/)

问题简介：在01背包的基础上加入一些变数，每种物品都有无数个。

解题方向：描述不可计数问题，可以采用迭代方法，难点在于推导状态转移方程。相较于01背包，完全背包的状态情况不再是2种，而是k种。 

![image-20241114164427234](https://raw.githubusercontent.com/EverForests/PicGo/master/image-20241114164427234.png)

从k种状态出发，给出每层的一般转移形式，然后做代换找到递推规律。

![image-20241114164436619](https://raw.githubusercontent.com/EverForests/PicGo/master/image-20241114164436619.png)

```c++
// 朴素解法
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 1010;
int n, m;
int v[N], w[N];
int f[N][N];

int main()
{
    cin >> n >> m;
  
    for (int i = 1; i <= n; i++) cin >> v[i] >> w[i];
  
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++) {
            f[i][j] = f[i-1][j];
            if (j >= v[i]) f[i][j] = max(f[i][j], f[i][j-v[i]] + w[i]);  // 迭代形式
        }
      
    cout << f[n][m] << endl;
}
// 优化空间版
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 1010;
int n, m;
int v[N], w[N];
int f[N];

int main()
{
    cin >> n >> m;
  
    for (int i = 1; i <= n; i++) cin >> v[i] >> w[i];
  
    for (int i = 1; i <= n; i++)
        for (int j = v[i]; j <= m; j++) {
            f[j] = f[j]; // 按C++处理逻辑，在计算本层f[j]时，右侧一定是上层f[j]，改写无异义
            // f[i][j] = f[i-1][j];
            f[j] = max(f[j], f[j-v[i]] + w[i]);  // 迭代形式
            // f[i][j] = max(f[i][j], f[i][j-v[i]] + w[i]); 合理
        }
      
    cout << f[m] << endl;
}
```

## 多重背包问题 [go](https://www.acwing.com/activity/content/problem/content/999/)

相当于弱化版的完全背包，每种物品的数量是有限的。

对于此问题，在数据量不大的情况下，可以考虑将其化归为01背包问题来解决。对于第i种物品，需要包含将1、2、3...k个对象放入背包的思考，难点在于状态转移方程的设计。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 110;
int n, m;
int v[N], w[N], s[N];
int f[N][N];

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> v[i] >> w[i] >> s[i];

    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            for (int k = 0; k <= s[i] && k*v[i] <= j; k++) {
                if (k == 0) f[i][j] = f[i-1][j];
                else f[i][j] = max(f[i][j], f[i-1][j-k*v[i]] + k*w[i]);
            }

    cout << f[n][m] << endl;

    return 0;
}
```

## 多重背包问题 · 加强版 [go](https://www.acwing.com/activity/content/problem/content/1000/)

在数据比较强时，就不能用这样的多重循环来硬解了，可以考虑通过分组来构建一个新的情景。

这里采用二进制法，那么什么是二进制法？对于1～7，我们可以用1、2、4来组合表示，通过提高逻辑复杂度来降低表示复杂度，这便是二进制法，对于数量相关问题尤为有效。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 12010, M = 2010;
int n, m;
int f[M];
int v[N], w[N];

int main()
{
    cin >> n >> m;
    
    int cnt = 0;
    for (int i = 1; i <= n; i++) {
        int a, b, s;
        cin >> a >> b >> s;
        
        int k = 1;
        while (s >= k) {
            cnt++;
            v[cnt] = a*k;
            w[cnt] = b*k;
            s -= k;
            k *= 2;
        }
        
        if (s > 0) {
            cnt++;
            v[cnt] = a*s;
            w[cnt] = b*s;
        }
    }
    
    n = cnt;
    
    for (int i = 1; i <= n; i++)
        for (int j = m; j >= v[i]; j--)
            f[j] = max(f[j], f[j-v[i]] + w[i]);
            
    cout << f[m] << endl;
    
    return 0;
}
```

## 分组背包问题 [go](https://www.acwing.com/activity/content/problem/content/1001/)

数据量较小，分组较为简洁，所以改写一下状态转移方程就可以解决。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 110;
int n, m;
int s[N];
int v[N][N], w[N][N];
int f[N][N];

int main()
{
    cin >> n >> m;
    
    for (int i = 1; i <= n; i++) {
        cin >> s[i];
        for (int j = 1; j <= s[i]; j++) cin >> v[i][j] >> w[i][j];
    }
    
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++) {
            f[i][j] = f[i-1][j];
            for (int k = 1; k <= s[i]; k++) {
                if (j >= v[i][k]) f[i][j] = max(f[i][j], f[i-1][j - v[i][k]] + w[i][k]);
            }
        }
        
    cout << f[n][m] << endl;
    
    return 0;
}
```

