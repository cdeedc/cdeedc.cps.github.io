---
layout: default
title: Darb
parent: Grades 11-12
tags: [Graph Theory]
---
## [Diametrul unui arbore (`darb`)](https://www.infoarena.ro/problema/darb)
Se cere să se determine diametrul unui arbore.

*Soluție ($\mathcal{O}(n)$).* Vom folosi metoda programării dinamice. Fie arborele $G = (V, E)$, cu $n$ noduri. Pentru fiecare vârf $v \in V$, notăm cu $v.\pi$ părintele nodului $v$ (vom fixa rădăcina arborelui în nodul $1$).

Notăm cu

$$
\texttt{toLeaf}(x) = \text{ lungimea maxim\u{a} a unui drum de la } x \text{ la o frunz\u{a} oarecare }
$$
și cu
$$
\texttt{maxLength}(x) = \text{ lungimea maxim\u{a} a unui drum al c\u{a}rui nod de nivel minim este } x
$$

Avem următoarele relații de recurență:
$$
\texttt{toLeaf}(x) = 1 + \max_{c.\pi = x} \texttt{toLeaf}(c),
$$
respectiv
$$
\texttt{maxLength}(x) = 2 + \max\limits_{a.\pi = b.\pi = x,\; a \neq b} (\texttt{toLeaf}(a) + \texttt{toLeaf}(b))
$$

Diametrul arborelui este atunci egal cu
$$
d = \max\limits_{1 \leqslant x \leqslant n} \texttt{maxLength}(x).
$$

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

**Bibliografie:** [Competitive Programmer's Handbook, Antti Laaksonen](https://usaco.guide/CPH.pdf), pag. 146