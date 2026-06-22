---
title: Shift
parent: Grade 10
nav_order: 2
tags: [implementation]
---
```cpp
/**
#include <iostream>

using namespace std;

*/
void shift(int *x, int n)
{
    int aux = x[0];
    for(int i = 0; i < n - 1; i++)
        x[i] = x[i + 1];
    x[n - 1] = aux;
}

/**
void print(int *x, int n)
{
    for(int i = 0; i < n; i++)
        cout << x[i] << ' ';
    cout << '\n';
}

int main()
{
    int n = 6;
    int *x = new int[6]{12, 7, 6, 3, 8, 5};
    shift(x, n);
    print(x, n);
    return 0;
}
*/
```
