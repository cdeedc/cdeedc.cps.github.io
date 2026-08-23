---
layout: default
title: Numar
parent: Grade 9
tags: [Number Theory]
---
## [`Numar`](https://www.infoarena.ro/problema/numar)

Se consideră un număr $n$. Să se determine toate modalitățile în care numărul dat se poate scrie ca sumă de cel puțin două numere întregi consecutive.

*Soluție.* Fie $n \in \mathbb{N}$. Presupunem că există o scriere a lui $n$ ca sumă a $k$ numere întregi consecutive, pornind de la $a$. Atunci avem

$$
\begin{align*}
n &= a + (a + 1) + \ldots + (a + k - 1) = \sum_{i = 0}^{k - 1} (a + i) \\
  &= ka + \frac{k(k - 1)}{2} = \frac{k(2a + k - 1)}{2}
\end{align*}
$$

Relația de mai sus devine $2n = k(2a + k - 1)$. Atunci

$$
\begin{cases}
k &= d \; \vert \; {2n} \\
2a + k - 1 &= \frac{2n}{d}
\end{cases}
$$

De aici $2a = \frac{2n}{d} - d + 1 \iff a = \frac{1}{2}\left(\frac{2n}{d} - d + 1\right)$.

Soluția problemei este alcătuită perechile de numere $(a, k)$, reprezentând o descompunere a lui $n$, unde:

$$
\begin{cases}
k &= d \\
a &= \frac{1}{2}\left(\frac{2n}{d} - d + 1\right) \\
\end{cases}
, \; d \; \vert \; {2n}.
$$

> *Observație.* În secțiunea *Restricții și precizări* a problemei, este menționat faptul că descompunerile trebuie afișate în ordine descrescătoare a primului număr. De aceea, vom reține toate descompunerile într-un vector pe care îl vom sorta ulterior.

Structura `Item` reține o descompunere a numărului dat: prima valoare (`firstVal` - echivalentul lui $a$ de mai sus) și numărul de elemente din descompunere (`count` - echivalentul lui $k$ de mai sus). Așadar, avem

```cpp
struct Item
{
    int firstVal;
    int count;
};
```
Deoarece dorim să sortăm vectorul pe care îl obținem, supraîncărcăm operatorul `<` din cadrul structurii `Item`:
```cpp
bool operator<(const Item& item) const
{
    return firstVal > item.firstVal;
}
```

*Codul sursă.*
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