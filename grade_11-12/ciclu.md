---
layout: default
title: Ciclu
parent: Grades 11-12
tags: [Graph Theory]
---
## [`Ciclu`](https://www.infoarena.ro/problema/ciclu)

Se dă un graf orientat ponderat $G = (V, E)$ cu $n$ noduri și $m$ arce, cu funcția de cost $w : E \to \mathbb{R}$, pentru care avem $$w(e) > 0, \forall e \in E.$$

Pentru un ciclu $c = (v_0, v_1, \ldots, v_k)$, $v_0 = v_k$, definim costul acestuia prin 
$$w(c) = \sum_{i = 1}^{k} w(v_{i - 1}, v_i),$$
precum și costul său mediu ca fiind egal cu $\frac{w(c)}{k}$.

Se cere să se determine costul mediu minim al vreunui ciclu, adică valoarea:
$$\min_{c = (v_0, v_1, \ldots, v_k)} \frac{w(c)}{k} .$$

*Soluție.*

| ciclu.in    | ciclu.out  |
|-------------|------------|
| $4$ $5$     | $1.75$     |
| $1$ $2$ $1$ |            | 
| $2$ $3$ $1$ |            |
| $1$ $3$ $1$ |            |
| $3$ $4$ $2$ |            |
| $4$ $1$ $3$ |            |

*Codul sursă.*
```cpp
#include <fstream>
#include <iomanip>

using namespace std;

ifstream fin("ciclu.in");
ofstream fout("ciclu.out");

const int MAX_N = 1000;
const int MAX_M = 4000;
const int INF = 1 << 30;
const double EPS = 0.001;

struct Edge
{
    int u, v, cost;
};

Edge edges[MAX_M + 1];
double dist[MAX_N + 1];
int n, m;

void ReadGraph()
{
    fin >> n >> m;
    for(int i = 1; i <= m; i++)
        fin >> edges[i].u >> edges[i].v >> edges[i].cost;
}

void Init(int src)
{
    for(int i = 1; i <= n; i++)
        dist[i] = INF;
    dist[src] = 0;
}

void RelaxEdge(Edge edge, double x)
{
    if(dist[edge.v] > dist[edge.u] + edge.cost + x)
        dist[edge.v] = dist[edge.u] + edge.cost + x;
}

bool NegativeCycle(int src, double x)
{
    Init(src);
    for(int step = 1; step < n; step++)
        for(int i = 1; i <= m; i++)
            RelaxEdge(edges[i], x);
    for(int i = 1; i <= m; i++)
        if(dist[edges[i].v] > dist[edges[i].u] + edges[i].cost + x)
            return true;
    return false;
}

void BinarySearch()
{
    double left = 0, right = INF, res = INF;
    while(right - left > EPS)
    {
        double mid = left + (right - left) / 2;
        if(NegativeCycle(1, -mid))
        {
            res = mid;
            right = mid - EPS;
        }
        else
            left = mid + EPS;
    }
    fout << fixed << setprecision(2) << res << '\n';
}

int main()
{
    ReadGraph();
    BinarySearch();

    fin.close();
    fout.close();

    return 0;
}
```