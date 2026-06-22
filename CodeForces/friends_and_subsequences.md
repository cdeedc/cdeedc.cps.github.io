---
title: Friends and Subsequences
parent: CodeForces
nav_order: 2
tags: [Range Minimum Query]
---

## [B. Friends and Subsequences](https://codeforces.com/edu/course/3/lesson/18/2/practice/contest/619577/problem/B)

Se dau doi vectori $a$ și $b$ cu $n$ elemente fiecare. Să se determine numărul de secvențe $[l, r]$ cu $1 \leqslant l \leqslant r \leqslant n$ care au proprietatea:

$$ \max\limits_{l \leqslant i \leqslant r} a_i = \min\limits_{l \leqslant i \leqslant r} b_i. $$

Pentru a calcula eficient $\max\limits_{l \leqslant i \leqslant r} a_i$ și $\min\limits_{l \leqslant i \leqslant r} b_i$, vom folosi metoda programării dinamice, și anume *Range Minimum Query*. Cu o precalculare în timp $\mathcal{O}(n \log n)$, vom reuși să răspundem la interogări precum valoarea minimă pe un interval din vector în timp constant: $\mathcal{O}(1)$.

Fie atunci:
* $\text{maxRmq}(p, i) = \max\limits_{i \leqslant j < i + 2^p} a_j$ - valoarea maximă din vectorul $a$ între pozițiile $[i, i + 2^p - 1]$
* $\text{minRmq}(p, i) = \min\limits_{i \leqslant j < i + 2^p} b_j$ - valoarea minimă din vectorul $b$ între pozițiile $[i, i + 2^p - 1]$

Pentru fiecare $1 \leqslant l \leqslant n$, vom număra câte valori $l \leqslant r \leqslant n$ există astfel încât să avem $\max\limits_{l \leqslant i \leqslant r} a_i = \min\limits_{l \leqslant i \leqslant r} b_i$ în $\mathcal{O}(\log n)$ cu ajutorul celor două tablouri bidimensionale menționate anterior.

Fie deci $l \in \{1, 2, \ldots, n\}$ fixat.

Definim funcțiile $f, g : \{l, l + 1, \ldots, n\} \to \mathbb{R}$ prin
* $f(r) = \max\limits_{l \leqslant i \leqslant r} a_i$ ,
* $g(r) = \min\limits_{l \leqslant i \leqslant r} b_i$ ,

unde $r \geqslant l$ reprezintă capătul dreapta al unei secvențe.

> *Observație.* Funcția $f$ este crescătoare, în timp ce $g$ este descrescătoare.

*Demonstrație.* 

```cpp
#include <iostream>
#include <fstream>

using namespace std;

inline int min(int x, int y) { return x < y ? x : y; }
inline int max(int x, int y) { return x > y ? x : y; }

const int MAX_N = 200000,
          MAX_LOG = 18;

int a[MAX_N + 1],
    b[MAX_N + 1];
int n;

int maxRmq[MAX_LOG + 1][MAX_N + 1],
    minRmq[MAX_LOG + 1][MAX_N + 1];
int log2[MAX_N + 1];

void Read()
{
    cin >> n;
    for(int i = 1; i <= n; i++)
        cin >> a[i];
    for(int i = 1; i <= n; i++)
        cin >> b[i];
}

void Precalc()
{
    log2[1] = 0;
    for(int i = 2; i <= n; i++)
        log2[i] = log2[i >> 1] + 1;
}

void Build()
{
    for(int i = 1; i <= n; i++)
    {
        maxRmq[0][i] = a[i];
        minRmq[0][i] = b[i];
    }
    for(int p = 1; p <= log2[n]; p++)
        for(int i = 1; i <= n; i++)
        {
            if(i + (1 << p) - 1 > n)
            {
                maxRmq[p][i] = maxRmq[p - 1][i];
                minRmq[p][i] = minRmq[p - 1][i];
                continue;
            }
            maxRmq[p][i] = max(maxRmq[p - 1][i], maxRmq[p - 1][i + (1 << (p - 1))]);
            minRmq[p][i] = min(minRmq[p - 1][i], minRmq[p - 1][i + (1 << (p - 1))]);
        }
}

int maxQuery(int l, int r)
{
    int len = log2[r - l + 1];
    return max(maxRmq[len][l], maxRmq[len][r - (1 << len) + 1]);
}

int minQuery(int l, int r)
{
    int len = log2[r - l + 1];
    return min(minRmq[len][l], minRmq[len][r - (1 << len) + 1]);
}

int LowerBound(int l)
{
    if(a[l] >= b[l])
        return (a[l] == b[l]) ? l : -1;
    int low = l, high = n, r = l;
    while(low <= high)
    {
        int mid = low + ((high - low) >> 1);
        if(maxQuery(l, mid) - minQuery(l, mid) < 0)
        {
            r = mid;
            low = mid + 1;
        }
        else
            high = mid - 1;
    }
    r++;
    return (maxQuery(l, r) != minQuery(l, r)) ? -1 : r;
}

int UpperBound(int l)
{
    if(a[l] > b[l] || maxQuery(l, n) - minQuery(l, n) < 0)
        return -1;
    if(maxQuery(l, n) == minQuery(l, n))
        return n;
    int low = l, high = n, r = l;
    while(low <= high)
    {
        int mid = low + ((high - low) >> 1);
        if(maxQuery(l, mid) - minQuery(l, mid) > 0)
        {
            r = mid;
            high = mid - 1;
        }
        else
            low = mid + 1;
    }
    r--;
    return (maxQuery(l, r) != minQuery(l, r)) ? -1 : r;
}

void Solve()
{
    long long cnt = 0;
    for(int l = 1; l <= n; l++)
    {
        int rLow = LowerBound(l),
            rHigh = UpperBound(l);
        if(rLow != -1 && rHigh != -1)
            cnt += (rHigh - rLow + 1);
    }
    cout << cnt << '\n';
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    #ifndef ONLINE_JUDGE
        freopen("test.in", "r", stdin);
        freopen("test.out", "w", stdout);
    #endif // ONLINE_JUDGE

    Read();
    Precalc();
    Build();
    Solve();

    return 0;
}
```