---
layout: default
title: Darb
parent: Grades 11-12
tags: [Graph Theory]
---
## [`darb`](https://www.infoarena.ro/problema/darb)
Se cere să se determine diametrul unui arbore.

*Soluție.* 

**Bibliografie:** [Competitive Programmer's Handbook, Antti Laaksonen](https://usaco.guide/CPH.pdf), pag. 146

```cpp
#include <fstream>
#include <vector>

using namespace std;

ifstream f("darb.in");
ofstream g("darb.out");

const int MAX_N = 100000;

inline void maxSelf(int& x, int y)
{
    x = (x < y) ? y : x;
}

vector<int> adj[MAX_N + 1];
int n;

int toLeaf[MAX_N + 1],
    maxLength[MAX_N + 1];

void Read()
{
    f >> n;
    for(int i = 1; i < n; i++)
    {
        int a, b;
        f >> a >> b;
        adj[a].push_back(b);
        adj[b].push_back(a);
    }
}

void DFS(int node, int parent)
{
    for(int next : adj[node])
        if(parent != next)
        {
            DFS(next, node);
            maxSelf(toLeaf[node], toLeaf[next] + 1);
        }
    if(adj[node].empty())
        return;
    if((int)adj[node].size() == 1)
    {
        maxSelf(maxLength[node], toLeaf[adj[node][0]] + 1);
        return;
    }
    int node1 = 0, node2 = 0;
    for(int next : adj[node])
        if(toLeaf[next] > toLeaf[node1])
        {
            node2 = node1;
            node1 = next;
        }
        else
        if(toLeaf[next] > toLeaf[node2])
            node2 = next;
    maxSelf(maxLength[node], toLeaf[node1] + toLeaf[node2] + 2);
}

void GetDiameter()
{
    int diam = 0;
    for(int i = 1; i <= n; i++)
        maxSelf(diam, maxLength[i]);
    g << diam + 1 << '\n';
}

int main()
{
    Read();
    DFS(1, -1);
    GetDiameter();

    f.close();
    g.close();

    return 0;
}
```