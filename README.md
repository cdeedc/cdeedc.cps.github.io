# cdeedc_cps.github.io
This website includes solutions (explanation and code) of competitive programming problems (`cps` stands for Competitive Programming Solutions).

`SecventaK` (#1447). Se consideră un şir format din $n$ numere naturale şi un număr natural dat $k$. Să se determine numărul secvenţelor din şir care au proprietatea că suma elementelor secvenţei este de cel puţin de $k$ ori mai mare sau egală decât numărul elementelor secvenţei.

*Soluție:* Fie $a = (a_1, a_2, \ldots, a_n)$ șirul dat. Vom număra toate secvențele $a_i, \ldots, a_j$ cu proprietatea 

$$a_i + a_{i + 1} + \cdots + a_j \geqslant (j - i + 1) \cdot k \iff \displaystyle \sum_{p = i}^{j} (a_p - k) \geqslant 0.$$

După ce vom scădea $k$ din fiecare element al șirului $a$, va trebui să numărăm câte secvențe au suma nenegativă. Introducând și sumele parțiale asociate acestui șir,

$$sp_i = \displaystyle \sum_{j \leqslant i} a_j,$$

o secvență are proprietatea din enunț dacă $sp_j - sp_{i - 1} \geqslant 0 \iff sp_j \geqslant sp_{i - 1}$. 

Astfel, la pasul $1 \leqslant j \leqslant n$, vom număra câte sume anterioare sunt mai mici sau egale cu $sp_j$ folosind un arbore indexat binar / arbore de intervale. Deoarece sumele pot fi foarte mari (de ordinul lui $10^{16}$), va trebui întâi să realizăm o normalizare a acestor valori.

```cpp
#include <fstream>
#include <cstdint>
#include <map>

using namespace std;

ifstream f("secventak.in");
ofstream g("secventak.out");

const int NMAX = 100000;

class FenwickTree
{
private:
    int tree[NMAX + 5],
        cnt;
public:
    FenwickTree(int cnt = 0)
    {
        this->cnt = cnt;
        for(int i = 1; i <= cnt; i++)
            tree[i] = 0;
    }
    void Update(int pos, int val)
    {
        while(pos <= cnt)
        {
            tree[pos] += val;
            pos += pos & -pos;
        }
    }
    int64_t Query(int pos)
    {
        int64_t sum = 0;
        while(pos > 0)
        {
            sum += (int64_t)tree[pos];
            pos &= pos - 1;
        }
        return sum;
    }
};

map<int64_t, int> vals;
int64_t sums[NMAX + 1];
int n, k;

void Read()
{
    f >> n >> k;
    for(int i = 1; i <= n; i++)
    {
        int x;
        f >> x;
        sums[i] = sums[i - 1] + (x - k);
    }
}

void Normalise()
{
    for(int i = 1; i <= n; i++)
        vals[sums[i]] = 1;
    int timer = 0;
    for(auto& [sum, val] : vals)
        val = (++timer);
}

void CountSeq()
{
    int64_t ans = 0;
    FenwickTree freq(n);
    for(int i = 1; i <= n; i++)
    {
        ans += freq.Query(vals[sums[i]]);
        if(sums[i] >= 0)
            ans++;
        freq.Update(vals[sums[i]], 1);
    }
    g << ans << '\n';
}

int main()
{
    Read();
    Normalise();
    CountSeq();
    f.close();
    g.close();
    return 0;
}

```

 {% raw %}
  $$a^2 + b^2 = c^2$$ --> note that all equations between these tags will not need escaping! 
 {% endraw %}
