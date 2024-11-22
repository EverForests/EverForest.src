---
title: 数据遍历
tags: [Search]
categories: 'acking'
description: ''
cover: 'https://raw.githubusercontent.com/EverForests/PicGo/master/wallhaven-6dg7ll.png'
date: 2024-11-15 16:46:26
keywords:
---

## 1 DFS（广义深搜）

### 1.1 排列数据

构建递归，先确定终止条件再设计每一层需要做的事。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 10;
bool st[N];
int a[N];


int n;
void dfs(int k)
{
    if (k > n)
    {
        for (int i = 1; i <= n; i++) cout << a[i] << " ";
        cout << endl;
    }

    for (int i = 1; i <= n; i++)
    {
        if (!st[i])  // 注意以下代码的执行条件
        {
            a[k] = i;
            st[i] = true;
            dfs(k + 1);
            st[i] = false;
        }
    }
}

int main()
{

    cin >> n;
    dfs(1);
    return 0;
}
```

### 1.2 n-皇后问题

#### 1.2.1 不带入结论直接对每一个位置的可能性进行枚举

注意这里以竖直向下为x方向，以水平向右为y方向

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 20;
int n;
char g[N][N];
bool row[N], col[N], dg[N], udg[N];

void dfs(int x, int y, int s)
{
    if (s > n) return;
    if (y == n) y = 0, x++;
    if (x == n) {
        if (s == n) {  // 注意判断是否放置了n个皇后，因为有一些情况并没有放满n个
            for (int i = 0; i < n; i ++ ) puts(g[i]);  // 可以采用此种特殊的输出方式是因为0为串集结束符，在字符区域以外都是串集结束符，而下面的方法则行不通
            puts("");
        }
        return;
    }
  
    // 算法对每一层都应该有普适性，所以选择了这种写法
    g[x][y] = '.';
    dfs(x, y + 1, s);
  
    if (!row[x] && !col[y] && !dg[x+y] && !udg[n + x - y])
        {
            g[x][y] = 'Q';
            row[x] = col[y] = dg[x+y] = udg[n + x - y] = true;
            dfs(x, y+1, s+1);
            row[x] = col[y] = dg[x+y] = udg[n + x - y] = false;
            g[x][y] = '.';
        }
}

int main()
{
    cin >> n;
    dfs(0, 0, 0);
    return 0;
}
```

#### 1.2.2 带入结论（每行只有一个皇后）的方法

注意这里以竖直向下为x方向，以水平向右为y方向, 在联系对角线位置与坐标位置有重要作用

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 20;
int n;
char g[N][N];
bool col[N], dg[N], udg[N];

void dfs(int u)
{
    if (u == n) {
        for (int i = 0; i < n; i ++ )  // 注意输出格式
        {
            for (int j = 0; j < n; j ++ ) cout << g[i][j];
            cout << endl;
        }
        cout << endl;
    }
  
    for (int i = 0; i < n; i++)
    {
        if (!col[i] && !dg[u+i] && !udg[n - u + i])
        {
            g[u][i] = 'Q';
            col[i] = dg[u+i] = udg[n - u + i] = true;
            dfs(u+1);
            col[i] = dg[u+i] = udg[n - u + i] = false;
            g[u][i] = '.';
        }
    }
}

int main()
{
    cin >> n;
    for (int i = 0; i < n; i ++ )
        for (int j = 0; j < n; j ++ )
            g[i][j] = '.';
    
    dfs(0);
    return 0;
}
```

## 2 BFS（广义宽搜）

### 2.1 走迷宫

这道题是非常经典的一道bfs模板题，在周赛里出现了很多次了哇

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <queue>
#define x first
#define y second

using namespace std;

typedef pair<int, int> PII;

const int N = 110;

int n, m;
int g[N][N], d[N][N];

int bfs()
{
    queue<PII> q;
  
    memset(d, -1, sizeof d);
    d[0][0] = 0;
    q.push({0, 0});
  
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    while (q.size())
    {
        auto t = q.front();
        q.pop();
  
        for (int i = 0; i < 4; i++)
        {
            int x = t.x + dx[i], y = t.y + dy[i];
            if (x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1)  // 注意x和y的边界限制
            {
                d[x][y] = d[t.x][t.y] + 1;
                q.push({x, y});
            }
        }
    }
  
    return d[n-1][m-1];
}

int main()
{
    cin >> n >> m;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            cin >> g[i][j];
  
    cout << bfs() << endl;
    return 0;
} 
```

## 3 树与图的深搜

思路：
 在深搜基础上添加一些函数来对以每个节点为根的子树进行分析。

重点：

1. 邻接表构建图（数组+单链表）
2. 基于邻接表的深搜实现
3. 将重心的寻找与深搜结合

第二遍做的时候对点数不是特别清楚，它不是权值，这里的点数指的是节点数。在有了以上的认知后，我们在设计深度优先搜索时就可以做一个假设，然后根据这个假设来设计递归函数的返回值。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <cstdio>

using namespace std;
const int N = 100010, M = N*2;  // 握手定理

int n;
int h[N], e[M], ne[M], idx;
bool st[N];
int ans = N;

void add(int a, int b)  // 图的标准初始化工具
{
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

int dfs(int u)
{
    st[u] = true;
  
    int size = 0, sum = 0;
    for (int i = h[u]; i != -1; i = ne[i])  // 深搜所有点，探究拿掉点后的情况，本题的关键也在于此。
    {
        int j = e[i];
        if (!st[j])
        {
            int s = dfs(j);  // 获得子树的不包含根的节点数
            size = max(size, s);  // 求阶数最大的子树
            sum += s;  // 除去节点u所有子树之外的部分节点树，用图示理解可能更清晰
        }
    }
    size = max(size, n - sum - 1);
    ans = min(ans, size); 
  
    return sum + 1;  // 注意返回值的选择，它与每一层的回溯相关
}

int main()
{
    cin >> n;
  
    int a, b;
    memset(h, -1, sizeof h);  // 初始化节点链表，一定在初始化边之前
  
    for (int i = 0; i < n - 1; i ++ )
    {
        int a, b;
        scanf("%d%d", &a, &b);
        add(a, b), add(b, a);
    }
  
    dfs(1);  // 别忘记遍历
    cout << ans << endl;
    return 0;
}
```

## 4 树与图的广搜

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 100010, M = N*2;
int n, m;
int h[N], e[M], ne[M], idx;  // 注意h、ne、idx中和e的下标中存的都是位置值，而h的下标、e中存的是实值
int d[N], q[M];

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

int bfs()
{
    int hh = 0, tt = -1;
    memset(d, -1, sizeof d);
  
    d[1] = 0;
    q[++tt] = 1;
  
    while (hh <= tt)
    {
        int t = q[hh++];
        for (int i = h[t]; i != -1; i = ne[i])  // 由于是层序遍历，所以找到的一定是最短路径，越早遍历到的路径越短
        {
            int j = e[i];
            if (d[j] == -1) {
                d[j] = d[t] + 1;  // 注意位置与实值间的差异, 同时关注运算符的优先级
                q[++tt] = j;
            }
        }
    }
  
    return d[n];
}

int main()
{
    cin >> n >> m;
    memset(h, -1, sizeof h);
  
    int a, b;
    while (m -- ) {
        cin >> a >> b;
        add(a, b);
    }
  
    cout << bfs() << endl;
    return 0;
}
```

## 5 拓扑序列

思路：考虑各点的入度，一个合理拓扑序列一定是从0度点出发的；确定好起点后可以选择一种合适的遍历方式，通常来讲应该选择广度优先搜索，度先降为0的点应该在前面。

重点：会实现静态队列，这里需要将队列信息展示出来，所以没有选择用头文件实现。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 100010, M = N*2;

int n, m;
int h[N], e[M], ne[M], idx;
int d[N], q[N];  // d[N] 具有判重数组的作用

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

bool torbsort()
{
    int hh = 0, tt = -1;
    for (int i = 1; i <= n; i++)
    {
        if (!d[i]) q[++tt] = i;  // 从0入度点切入
    }
  
    while (hh <= tt)
    {
        int t = q[hh++];
        for (int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            if (--d[j] == 0) q[++tt] = j;  // 如果指向的点入读为1，那么直接入队，如果不为1，说明还有其它点指向点j，且这一点（记为k）一定不指向点t，由于是按序录入切入点的，所以点k一定在t点之后,点j先不入队
        }
    }
  
    return tt == n - 1;  // 若满足拓扑序列，那么队列里应该包含了所有元素
}

int main()
{
    cin >> n >> m;
    memset(h, -1, sizeof h);  // 特别注意h数组的初始化，以及d数组的多含义，要不要初始化
  
    int a, b;
    while (m--)
    {
        cin >> a >> b;
        add(a, b);
        d[b]++;
    }
  
    if (torbsort()) {
        for (int i = 0; i < n; i++)
            cout << q[i] << " ";
    }
  
    else cout << -1 << endl;
  
    return 0;
}
```
