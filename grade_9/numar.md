---
layout: default
title: Numar
parent: Grade 9
tags: [Number Theory]
---
## [`Numar`](https://www.infoarena.ro/problema/numar)

Se consideră un număr $n$. Să se determine toate modalitățile în care numărul dat se poate scrie ca sumă de cel puțin două numere întregi consecutive.

*Soluție.* Fie $n \in \mathbb{N}$. Presupunem că există o scriere a lui $n$ ca sumă a $k$ numere întregi consecutive, pornind de la $a$. Atunci
$$
\begin{align*}
n &= a + (a + 1) + \ldots + (a + k - 1) \\
  &= ka + \frac{k(k - 1)}{2} \\
  &= \frac{k(2a + k - 1)}{2}
\end{align*}
$$

```cpp
#include <fstream>
#include <algorithm>
#include <vector>

using namespace std;

ifstream fin("numar.in");
ofstream fout("numar.out");

struct Item
{
    int firstVal;
    int count;

    bool operator<(const Item& item) const
    {
        return firstVal > item.firstVal;
    }
};
vector<Item> res;

vector<int> GetDivs(int val)
{
    vector<int> res;
    int d;
    for(d = 1; d * d < val; d++)
        if(val % d == 0)
        {
            res.push_back(d);
            res.push_back(val / d);
        }
    if(d * d == val)
        res.push_back(d);
    res.erase(res.begin());
    return res;
}

void Solve()
{
    int n;
    fin >> n;
    n <<= 1;

    vector<int> divs = GetDivs(n);
    for(const int& d : divs)
    {
        if((n / d - d + 1) & 1)
            continue;
        Item item;
        item.firstVal = (n / d - d + 1) >> 1;
        item.count = d;
        res.push_back(item);
    }
}

void WriteRes()
{
    for(const Item& item : res)
        fout << item.firstVal << ' ' << item.count << '\n';
}

int main()
{
    Solve();
    sort(res.begin(), res.end());
    WriteRes();

    fin.close();
    fout.close();

    return 0;
}
```