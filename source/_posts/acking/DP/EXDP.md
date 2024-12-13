---
title: 特殊DP问题
tags: [特殊DP问题]
categories: 'acking'
description: ''
cover: 'https://raw.githubusercontent.com/EverForests/PicGo/master/Avg_50_i36.png'
date: 2024-12-13 10:10:18
keywords:
---

## 区间DP——石子合并 [Go](https://www.acwing.com/problem/content/284/)

给定一堆石子，规定代价计算方式为待合并两堆石子的质量和，找代价最小的方案。

先建模，定义`f[i][j]`为第i至第j堆石子合并的最小代价。

讨论转移方程，`f[i][j] = f[i][k] + f[k][j] + w[i][j]`，`w[i][j]`表示第i至第j堆石子的质量和，从小到大对多堆石子的代价进行迭代即可，注意遍历顺序。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 310;
int f[N][N];
int s[N];
int n;

int main(){
    cin >> n;
    for (int i = 1; i <= n; i++) cin >> s[i];
    for (int i = 1; i <= n; i++) s[i] += s[i-1];  // 前缀和处理石子质量和
    
    for (int len = 2; len <= n; len++)
        for (int i = 1; i + len - 1 <= n; i++) {
            int l = i, r = i + len - 1;
            f[l][r] = 1e8;
            for (int k = l; k < r; k++)
                f[l][r] = min(f[l][r], f[l][k] + f[k+1][r] + s[r] - s[l-1]);
        }
        
    cout << f[1][n] << endl;
    
    return 0;
}
```

## 计数DP——整数划分 [Go](https://www.acwing.com/problem/content/902/)

将一个整数划分为一个单调序列，求合理的划分方法数。

这个问题可以划归为：从价值为1～n的n种不限量物品中取，考虑价值总和为n的取物方案数，于是便成了完全背包问题。

先建模，在新的立意下，定义`f[i][j]`为在前i个物品中取，总价值为j的方案数。

讨论转移方程，`f[i][j] = f[i-1][j] + f[i][j-i]`，`f[i][j-i]`可以参考之前的完全背包问题，是通过递推方程得出的，意为考虑取第i个物品时的取物方案数。由于等号右边先做计算，所以在遍历过程中i可以略去，因此有`f[j] = f[j] + f[j-i]`

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1010;
int f[N];
int n;

// 化归，数n的划分方案可以转化为，从价值为1～n的n种物品中取，考虑价值总和为n的不同方案。

int main()
{
    cin >> n;
    
    int mod = 1e9 + 7;
    
    f[0] = 1;
    
    for (int i = 1; i <= n; i++)
        for (int j = i; j <= n; j++)
            f[j] = (f[j] + f[j-i]) % mod;  // 这里简化了i维度，原模型为f[i][j]，意为考虑从1～i种物品中取，总和为j的所有方案
    
    cout << f[n] << endl;
    
    return 0;
}
```

## 数位统计DP——计数问题 [Go](https://www.acwing.com/problem/content/340/)

给定两个数，求范围内0～9各出现次数（这其实并不算一个正统的DP问题，更像小学数奥。。

如果采用暴力破解，包超时的，所以需要寻找规律，比如哪几位数出现的范围比较固定。

这里我们这样考虑，定义`count(n, k)`为1～n中数k出现的次数，k属于0～9。那么如果要计算任一数在范围内出现的次数，比如数k在m~n中出现的次数，可以通过`count(n, k) - count(m, k)`来计算，这里借用了前缀和的思想。现在问题就变成count函数该如何设计了，由于范围给定，数位相对固定，所以可以将k在每一位出现的次数叠加起来，得到范围内出现的次数。

从一般情况出发，对xxx1yyy，假设有`1 <= xxx1yyy <= abcdefg`，那么有以下规律：
若`xxx < abc`，xxx可取000～abc-1，yyy可取000~999，共`abc*1000`种。
若`xxx = abc`，需要对d进行分类：
`d < 1`，不合法；
`d = 1`，yyy可取000~efg，共`efg+1`种;
`d > 1`，yyy可取000~999，共1000种。

在实际计算中，将这三种情况计算并相加即可得到1在1～abcdefg中d位的出现次数。其它位类似，将各位的结果累加就能得到`count(abcdefg, 1)`。

注意如果考虑的数是0，那么要跳过首位。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;

// 获取位数
int get(vector<int> num, int l, int r) {
    int res = 0;
    for (int i = l; i >= r; i--) res = res * 10 + num[i];
    return res;
}

// 计算后续有多少0
int power10(int x) {
    int res = 1;
    while (x--) res *= 10;
    return res;
}

// 求1～n中x出现的次数
int count(int n, int x) {
    vector<int> num;
    
    while(n) {
        num.push_back(n%10);
        n /= 10;
    }
    
    n = num.size();
    
    int res = 0;
    for (int i = n-1-!x; i >= 0; i--) {
        if (i < n - 1) res += (get(num, n-1, i+1)-!x) * power10(i);
        
        if (num[i] == x) res += get(num, i-1, 0) + 1;
        else if (num[i] > x) res += power10(i);
    }
    
    return res;
}

int main()
{
    int a, b;
    
    while (cin >> a >> b, a || b) {
        if (a > b) swap(a, b);
        
        for (int i = 0; i <= 9; i++) 
            cout << count(b, i) - count(a-1, i) << " ";
        cout << endl;
    }

    return 0;
}
```

## 状态压缩DP——蒙德里安的梦想 [Go](https://www.acwing.com/problem/content/293/)

一道经典DP问题，将`N*M`的棋盘分割成若干个`1*2`的长方形，有多少种方案。

首先，1*2的长方形是划分的基本单位，它只有两种形态，横置和竖置。通过样例，我们可以发现，当我们将所有横置长方形确定后，竖置长方形的放法是唯一的，所以，只需考虑横置长方形的所有摆放方案。

建模是本题的难点，通常我们对问题的雏形进行思考，这里我们定义`f[i][j]`为填满前i列横置长方形时，第i列的状态为j的所有方案数。（为什么要考虑j，因为在填满前i列的同时大概率会存在第i-1列横置方块向第i列突出的情况）。j是状态压缩的标志，它是一个二进制数，表征第i列每一行1*1方格的占用情况。

这道题棋盘规模数值并不大，`1 <= N, M <= 11`，所以状态j的种类数最大也才$2^11$，因此我们可以对j的所有可能性进行遍历，如果空方格的数量为偶数，那就意味着第i列可以用竖置长方形填满，分割方案就是合理的，我们将每种状态的合理性记录在数组。

此外，还得找出所有可转移的状态，也即第i-2列向第i-1列突出的状态k。可转移有两个条件，一个是不冲突，即`k&j`为0；一个是可分割，即`k|j`应是一个合理状态。如果这两个条件都满足，就将k记录在j的可转移数组里。

最后对棋盘规模进行迭代分析（规模遍历）即可。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;

const int N = 12, M = 1 << N;

long long f[N][M];  // f[i][j]意为前i列已摆放完毕，且第i-1列向第i列突出的情况为j
bool st[M];
vector<vector<int>> state(M);
int n, m;

int main()
{
    while (cin >> n >> m, n || m) {
        for (int i = 0; i < (1 << n); i++) {
            int cnt = 0;
            bool isValid = true;
            
            for (int j = 0; j < n; j++) {
                if ((i >> j) & 1) {
                    if (cnt & 1) {isValid = false; break;}
                    cnt = 0;  // 注意清零
                }
                
                else cnt++;
            }
            
            if (cnt & 1) isValid = false;
            
            st[i] = isValid;
        }
        
        for (int i = 0; i < (1 << n); i++) {
            state[i].clear();
            for (int j = 0; j < (1 << n); j++) {
                if ((i & j) == 0 && st[i | j]) state[i].push_back(j);
            }
        }
        
        memset(f, 0, sizeof f);
        f[0][0] = 1;
        
        for (int i = 1; i <= m; i++) {
            for (int j = 0; j < (1 << n); j++){
                for (auto k: state[j]) {
                    f[i][j] += f[i-1][k];
                }
            }
        }
        
        cout << f[m][0] << endl;
    }
    
    return 0;
}
```

## 状态压缩DP——最短Hamilton路径 [Go](https://www.acwing.com/problem/content/93/)

给定一张 n 个点的带权无向图，点从 0∼n−1 标号，求起点 0 到终点 n−1 的最短 Hamilton 路径。 Hamilton 路径的定义是从 0 到 n−1 不重不漏地经过每个点恰好一次。

这里我们考虑压缩的状态是n个点的遍历情况，定义`f[i][j]`为遍历状态为i且此时到达点j的最短路径。

对于状态转移，应有`f[i][j] = f[state_k][k] + w[k][j]`。

初始化情况，由于是从点0出发，所以`f[1][0] = 0`。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 20, M = 1 << 20;  // 此处不能为30，空间爆了
int f[M][N], w[N][N];
int n;

int main()
{
    cin >> n;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cin >> w[i][j];
            
    memset(f, 0x3f, sizeof f);
    f[1][0] = 0;
            
    for (int i = 0; i < (1 << n); i++) 
        for (int j = 0; j < n; j++) {
            if (i >> j & 1) {  // 对任意状态下当前节点为j时，都可解，忽略过程
                for (int k = 0; k < n; k++) {
                    if ((i - (1 << j)) >> k & 1) f[i][j] = min(f[i][j], f[i - (1 << j)][k] + w[k][j]);
                }
            }
        }
        
    cout << f[(1 << n) - 1][n-1] << endl;
    
    return 0;
}
```

## 树形DP——没有上司的舞会 [Go](https://www.acwing.com/problem/content/287/)

Ural 大学有 N 名职员，编号为 1∼N。
他们的关系就像一棵以校长为根的树，父节点就是子节点的直接上司。
每个职员有一个快乐指数，用整数 Hi 给出，其中 1≤i≤N。
现在要召开一场周年庆宴会，不过，没有职员愿意和直接上司一起参会。
在满足这个条件的前提下，主办方希望邀请一部分职员参会，使得所有参会职员的快乐指数总和最大，求这个最大值。

由于本题已指明为树，所以相比之前的DP问题，本题形象得多。基于树的存储，定义`f[i][2]`为树中i号职员的活动参与情况，`f[i][0]`表示i号职员参与，`f[i][1]`表示i号职员不参与。然后从根节点出发，对树做一次dfs，更新每个点的f`[i][2]`即可。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 6010;
int f[N][2];
int happy[N];
int n, e[N], h[N], ne[N], idx;
bool has_root[N];

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

void dfs(int u) {
    f[u][1] = happy[u];
    
    for (int i = h[u]; ~i; i = ne[i]) {
        int j = e[i];
        dfs(j);
        f[u][1] += f[j][0];
        f[u][0] += max(f[j][0], f[j][1]);
    }
}

int main() {
    cin >> n;
    for (int i = 1; i <= n; i++) cin >> happy[i];
    
    memset(h, -1, sizeof h);
    
    for (int i = 0; i < n-1; i++) {
        int a, b;
        cin >> a >> b;
        add(b, a);
        has_root[a] = true;
    }
    
    int root = 1;
    while (has_root[root]) root++;
    
    dfs(root);
    
    cout << max(f[root][0], f[root][1]) << endl;
    
    return 0;
}
```

## 记忆化搜索——滑雪 [Go](https://www.acwing.com/problem/content/903/)

一个人在矩形雪域滑雪，有多个不同高度的区域（点），他只能从高海拔区域向低海拔区域滑，可以从任一区域开始滑，求最长的滑行轨迹（经过区域最多）。

在某种意义上，这应该是一道深搜问题（

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 310;
int h[N][N], f[N][N];
int r, c;

int dx[4] = {0, 1, 0, -1}, dy[4] = {1, 0, -1, 0};

int dp(int x, int y) {
    int &v = f[x][y];
    
    if (v != -1) return v;
    
    v = 1;
    for (int i = 0; i < 4; i++) {
        int a = x + dx[i], b = y + dy[i];
        if (a >= 1 && a <= r && b >= 1 && b <= c && h[a][b] < h[x][y])
            v = max(v, dp(a, b) + 1);
    }
    
    return v;
}

int main() {
    cin >> r >> c;
    
    memset(f, -1, sizeof f);
    for (int i = 1; i <= r; i++)
        for (int j = 1; j <= c; j++)
            cin >> h[i][j];
    
    int res = 0;
    for (int i = 1; i <= r; i++)
        for (int j = 1; j <= c; j++)
            res = max(res, dp(i, j));
            
    cout << res << endl;
    
    return 0;
}
```















































