---
title: 最短路
tags: [Search]
categories: 'acking'
description: ''
cover: 'https://raw.githubusercontent.com/EverForests/PicGo/master/wallhaven-zy7kyo.png'
date: 2024-11-15 16:46:07
keywords:
---

## 1 dijkstra

> 适于解决单源汇类型的最短路问题

### 1.1 基于邻接矩阵实现

思路：从起点出发，不断寻找起点距离最近的点，每找到一个最短路径就加入确定集，扩散该点的影响。

ps: 步步唯一才能实现最终程序，因此找到迭代方法是关键。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
int n, m;
const int N = 510;

int g[N][N];  // 采用邻接矩阵进行图的存储
int dist[N];  // 某点到出发点最短路
bool st[N];  // 判断是否找到了最短路

int dijkstra()
{
    memset(dist, 0x3f, sizeof dist);  // 注意memset的写法，平常只能填充0x3f、0和-1，填充其它会有异常
    dist[1] = 0;
  
    for (int i = 0; i < n - 1; i++)  // 注意这里是n-1次遍历，因为复杂图已经变成了简单图,然后排掉了1号点，最多可以确定n-1个点的最短路； 
    {
        int t = -1;
        for (int j = 1; j <= n; j++)  // 找到未确定最短路的第一个点，并确定它
        {
            if (!st[j] && (t == -1 || dist[t] > dist[j])) t = j;  // 寻找与当前确定集距离最近的点
        }
  
        st[t] = true;
   
        for (int j = 1; j <= n; j++)  // 根据上述遍历确定的最短路对之后的所有点做调整。
        {
            dist[j] = min(dist[j], dist[t] + g[t][j]);
        }
    }
  
    if (dist[n] == 0x3f3f3f3f) return -1;
    else return dist[n];
}

int main()
{
    cin >> n >> m;
    memset(g, 0x3f, sizeof g);  // 为之后做重边筛查打下基础
  
    int a, b, c;
    while (m--)
    {
        cin >> a >> b >> c;
        g[a][b] = min(g[a][b], c);   // 注意这里需要对重边做处理，挑出两点间最短的那条边就行；同时，在本题中环已泛化，因此无需特判
    }
  
    int t = dijkstra();
    cout << t << endl;
    return 0;
}
```

### 1.2 基于邻接表实现

思路：与1存在差别，这里是稀疏图，所以常用邻接表（当然邻接矩阵也可以做doge），用邻接表存储时，一个比较大的问题就是预处理重边和自环的算法会非常复杂，所以这里直接对复杂图进行记录并处理。但是，算法的思路还是不变的，从起点出发，不断寻找到起点的最短边（这里采用了最小堆简化过程），由于边都是非负的，因此就算需要考虑也不会对结果产生影响，这个最短边的另一端就是确定了最短路（距离已经最新最小）的端点。每确定一次，都对图中各点到起点的距离都做一次更新，每一个最新的点与其距离构成的点对都加入到最小堆。

重点：

1. 最小堆的构建
2. 每次加入最小堆的时机

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <queue>
#include <vector>

#define x first
#define y second

using namespace std;
typedef pair<int, int> PII;

const int N = 200010;
int n, m;
int h[N], e[N], ne[N], idx, w[N];
int dist[N];  // 到1位置最短距离
bool st[N];

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;  
}

/*
 对邻接表的理解越来越深了，idx表示的是每条边的位置（线性），e数组存储每条边的终点实值
 w数组存储每条边的权值，h数组存储以a为始点的第一条边的位置
*/

int dijkstra()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;
  
    priority_queue<PII, vector<PII>, greater<PII>> q;  // 优先队列的特殊格式
    q.push({0, 1});  // 距离优先，数对比较先比x再比y
  
    while (q.size())  // 与BFS类似的过程
    {
        auto t = q.top();
        q.pop();
        /*
        在将点推入队列时优先队列会对所有点的最短路值进行排序，所以每次从队列中提取的点都是确定找到最短路且
        最短路中距离最小的点
        */
        int distance = t.x, ver = t.y;
        if (st[ver]) continue;  // 注意这里要进行判重，否则会因为重边导致重复更新
        st[ver] = true;
  
        for (int i = h[ver]; i != -1; i = ne[i])  // 与点1只有1条边相连的所有点的最大距离必定比其它点到点1的距离要小
        {
            int j = e[i];
            if (dist[j] > dist[ver] + w[i])
            {
                dist[j] = dist[ver] + w[i];
                q.push({dist[j], j});
            }
      
        }
    }
  
    if (dist[n] == 0x3f3f3f3f) return -1;
    else return dist[n];
}

int main()
{
    cin >> n >> m;
  
    memset(h, -1, sizeof h);
  
    int a, b, c;
    while (m -- )
    {
        cin >> a >> b >> c;
        add(a, b, c);
    }
  
    int t = dijkstra();
    cout << t << endl;
    return 0;
}
```

## 2 bellman-ford

> 适于解决有边限制的最短路问题，常常体现在有负边权、有路径长度上限。

思路：基于稠密图，利用给定的边反复对各点到初点的路径长度进行更新，相对简单但时间复杂度高。

重点：

1. 构造边结构体，对各边进行遍历
2. 每次更新都应基于已知边，所以要用到memcpy数组进行维护。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 10010, M = 510;

int n, m, k;
int dist[M];
int backup[M];

struct e  // bellman_ford算法仅仅针对所有边，对图的连接性没有要求，所以直接记录边的信息即可
{
    int a, b, w;
}edge[N];

void bellman_ford()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;  // 注意将起点进行初始化
  
    for (int i = 0; i < k; i++)  // 进行k次更新
    {
        memcpy(backup, dist, sizeof dist);  // 注意拷贝原数组，防止后续在遍历所有边时后面的边会受前面的边影响，这里需要用例子来模拟理解
        for (int j = 0; j < m; j++)  // 明确遍历对象,注意边界
        {
            int a = edge[j].a, b = edge[j].b, w = edge[j].w;
            if (dist[b] > backup[a] + w)
                dist[b] = backup[a] + w;
        }
    }
}

int main()
{
    cin >> n >> m >> k;
    int a, b, w;
    for (int i = 0; i < m; i++)
    {
        cin >> a >> b >> w;
        edge[i] = {a, b, w};
    }
  
    bellman_ford();
  
    if (dist[n] > 0x3f3f3f3f/2) cout << "impossible" << endl;
    else cout << dist[n] << endl;
  
    return 0;
}
```

## 3 spfa

### 3.1 求最短路

ps：在负权自环这里困惑了很久，因为没有负权回路所以没有负权自环，从而spfa算法过程中并不会出现死循环。。。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 100010;

int n, m;
int h[N], e[N], ne[N], w[N], idx;
int dist[N];
bool st[N];  // 关键在于st数组的使用，dijkstra在确定某点最短距离后就更新，而本题由于有负边，所以st仅仅存放可以更新后排的点，在更新完后就要拿出，因为不确定该点到始点的距离是否已经为最短距离
int q[N];

void add(int a, int b, int v)
{
    e[idx] = b, ne[idx] = h[a], w[idx] = v, h[a] = idx++;
}

int spfa()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;
  
    int hh = 0, tt = -1;
    q[++tt] = 1;
    st[1] = true;
  
    while (hh <= tt)
    {
        int t = q[hh++];
        st[t] = false;
        for (int i = h[t]; i != -1; i = ne[i])  // 在bellman_ford算法的基础上考虑每一次所选的边都能够得到更新，所以采用宽搜
        {
            int j = e[i];
            if (dist[j] > dist[t] + w[i])  // 仅将可更新的点进行更新，并入队更新的之后的所有点，这也是循环终止的重要条件，否则会出现死循环。
            {
                dist[j] = dist[t] + w[i];
                if (!st[j]) {
                    q[++tt] = j;
                    st[j] = true;
                }
            }
        }
    }
  
    return dist[n];
}

int main()
{
    cin >> n >> m;
    memset(h, -1, sizeof h);
    int a, b, v;
    while (m--)
    {
        cin >> a >> b >> v;
        add(a, b, v);
    }
  
    int t = spfa();
    if (t == 0x3f3f3f3f) cout << "impossible" << endl;
    else cout << t << endl;
    return 0;
}
```

### 3.2 判断负环

思路：基于spfa模板，每个点都可以是起点，由于更新的不直接性，需要用队列来存放可以用来更新的节点。

重点：如何判断是否存在负环，若某个结点的更新次数超过结点数，那么就存在负环了。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <queue>

using namespace std;
const int N = 10010;

int n, m;
int h[N], e[N], ne[N], w[N], idx;
int dist[N], cnt[N], st[N];  // 注意这里dist初始化为0，其内涵与求最短路不同。

void add(int a, int b, int v)
{
    e[idx] = b, ne[idx] = h[a], w[idx] = v, h[a] = idx++;
}

bool spfa()
{
    queue<int> q;
    for (int i = 1; i <= n; i ++ )  // 从1出发不一定会经历到负权边，所以将所有节点放入队列，这样就可以遍历所有点附近的边，也就是图的所有边
    {
        st[i] = true;
        q.push(i);
    }
  
  
    while (q.size()) {
        int t = q.front();
        q.pop();
        st[t] = false;
  
        for (int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            if (dist[j] > dist[t] + w[i])  // 当到子节点的边权为负时才有更新，这个时候也有剪枝的效果
            {  
                dist[j] = dist[t] + w[i];
                cnt[j] = cnt[t] + 1;  // 统计每个点的更新次数，如果到了某种程度，则说明该点被过度优化了，那么它一定在一个负环上。
                if (cnt[j] >= n) return true;
          
                if (!st[j]) {
                    q.push(j);
                    st[j] = true;
                }
            }
      
        }
    }
  
    return false;
}

int main()
{
    cin >> n >> m;
    memset(h, -1, sizeof h);
  
    int a, b, v;
    while (m -- ) {
        cin >> a >> b >> v;
        add(a, b, v);
    }
  
    if (spfa()) cout << "Yes" << endl;
    else cout << "No" << endl;
    return 0;
}
```

## 4 Floyd

> 更暴力的解法

思路：基于邻接矩阵，用其中的元素表示两点间的初始距离，当然初始状态这个距离就是两点之间的边长，后面选择枚举两点间所有可能的路径组合，对任意两点间最短路进行判断。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N = 210, INF = 1e9;

int n, m, k;
int g[N][N];

void Floyd()  // 枚举两点间所有可能的路径组合
{
    for (int k = 1; k <= n; k++)
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                g[i][j] = min(g[i][j], g[i][k] + g[k][j]);  // 注意三重循环的顺序不能改变，会有小错误。
}

int main()
{
    cin >> n >> m >> k;
  
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
        {
            if (i == j) g[i][j] = 0;
            else g[i][j] = INF;
        }
  
    int a, b, w;
    while (m -- ){
        cin >> a >> b >> w;
        g[a][b] = min(g[a][b], w);
    }
  
    Floyd();
  
    int x, y;
    while (k--)
    {
        cin >> x >> y;
        if (g[x][y] > INF/2) cout << "impossible" << endl;
        else cout << g[x][y] << endl;
    }
    return 0;
}
```
