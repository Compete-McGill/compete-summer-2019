---
layout: post
title: Problem Set 5
categories:
  - combinatorics
---


# Week 5 Solutions

## CodeForces


## Kattis

### [Riječi](https://open.kattis.com/problems/rijeci)

{% highlight c++ %}
/*
 * Generate the Fibonacci sequence K-1 number of times
 */

#include <bits/stdc++.h>

#define vi vector<int>
#define ii pair<int,int>
#define iii pair<int,pair<int,int>>
#define vii vector<pair<int,int>>
#define vvi vector<vector<int>>

typedef long long ll;
typedef unsigned long long ull;

#define pb push_back
#define mp make_pair
#define fi first
#define se second

using namespace std;

int n;
ull a = 0, b = 1;

int main (void) {
    ios_base::sync_with_stdio(false);

    cin >> n;
    for (int i = 1; i < n; i++) {
        swap(a,b);
        b += a;
    }
    cout << a << " " << b << endl;

    return 0;
}
{% endhighlight %}

### [Character Development](https://open.kattis.com/problems/character)

{% highlight c++ %}
/*
 * For N people:
 * Calculate Sum of N choose i, for i from 2 to N (inclusive)
 */

#include <bits/stdc++.h>

#define vi vector<int>
#define ii pair<int,int>
#define iii pair<int,pair<int,int>>
#define vii vector<pair<int,int>>
#define vvi vector<vector<int>>

typedef long long ll;
typedef unsigned long long ull;

#define pb push_back
#define mp make_pair
#define fi first
#define se second

using namespace std;

int n;
ull dp[40][40];

int main (void) {
    ios_base::sync_with_stdio(false);

    for (int i = 0; i <= 30; i++) 
        dp[i][0] = dp[i][i] = 1;
    for (int i = 0; i <= 30; i++) {
        for (int j = 1; j <= i; j++) {
            if (!dp[i][j])
                dp[i][j] = dp[i-1][j-1] + dp[i-1][j];
        }
    }

    cin >> n;
    ull result = 0;
    for (int i = 2; i <= n; i++)
        result += dp[n][i];
    cout << result << endl;

    return 0;
}
{% endhighlight %}

### [Catalan Numbers](https://open.kattis.com/problems/catalan)

{% highlight python %}
#!/usr/bin/env python3

"""
Since 1 <= x <= 5000, we can precompute the array of the first
5000 Catalan numbers in O(n) and answer the queries in O(1)

Because the numbers could get pretty large, it's best to use
Java's BigInteger or Python for this problem.
"""

if __name__ == '__main__':
    Cat = [None]*5010
    Cat[0] = 1
    for m in range(1, 5001):
        Cat[m] = Cat[m-1] * (2*m*(2*m-1))//((m+1)*m)

    for _ in range(int(input())):
        print(Cat[int(input())])
{% endhighlight %}

### [Odd Binomial Coefficients](https://open.kattis.com/problems/oddbinom)

{% highlight python %}
#!/usr/bin/env python3

"""
Find the pattern of odd numbers in a Pascal's triangle.
Compute the result recursively
"""

from math import floor, log

def count_odd(current_height, current_sum):
    max_power = floor(log(current_height, 2))
    current_sum = 3 ** max_power
    current_height -= 2 ** max_power
    if current_height == 0:
        return current_sum
    else:
        return current_sum + 2 * count_odd(current_height, current_sum)

n = int(input())
print(count_odd(n, 0))
{% endhighlight %}

### [Batmanacci](https://open.kattis.com/problems/batmanacci)

{% highlight c++ %}
/*
 * Notice the pattern of the sequences. A sequence at index i will
 * be the prefix of all sequences at index i+2k. So regardless of
 * the big N, we just need to find the index of the first prefix
 * of the sequence at N that has length greater than or equal K.
 * After that, we can use a dividing algorithm to find the character
 * at index K.
 */

#include <bits/stdc++.h>

#define vi vector<int>
#define ii pair<int,int>
#define iii pair<int,pair<int,int>>
#define vii vector<pair<int,int>>
#define vvi vector<vector<int>>

typedef long long ll;
typedef unsigned long long ull;

#define pb push_back
#define mp make_pair
#define fi first
#define se second

using namespace std;

const ull MAX_K = 1e18;
ull a = 0, b = 1, k;
ull fib[100];
int n, idx = 0;

int main (void) {
    ios_base::sync_with_stdio(false);

    while (a < MAX_K) {
        fib[idx++] = a;
        swap(a,b);
        a += b;
    }
    fib[idx++] = a;
    fib[idx++] = a+b;

    cin >> n >> k;
    int pos = lower_bound(fib, fib+idx, k) - fib;

    if ((n-pos)&1)
        pos++;

    while (pos > 2) {
        if (k > fib[pos-2]) {
            k -= fib[pos-2];
            pos--;
        } else {
            pos -= 2;
        }
    }

    cout << (pos == 1 ? "N" : "A") << endl;

    return 0;
}
{% endhighlight %}

### [Election](https://open.kattis.com/problems/election)

{% highlight c++ %}
/*
 * This is a counting problem. For each test case, we need to count
 * the number of all possible results, the number of results where
 * our candidate (V1) wins. Finally we compare the ratio with the
 * given threshold. To compare two fractions (a/b) and (c/d), compare
 * the products a*d and b*c instead, to avoid unexpected results
 * due to dividing floats.
 */

#include <bits/stdc++.h>

#define vi vector<int>
#define ii pair<int,int>
#define iii pair<int,pair<int,int>>
#define vii vector<pair<int,int>>
#define vvi vector<vector<int>>

typedef long long ll;
typedef unsigned long long ull;

#define pb push_back
#define mp make_pair
#define fi first
#define se second

using namespace std;

ull dp[55][55];
int t,n,a,b,w;

int main (void) {
    ios_base::sync_with_stdio(false);

    for (int i = 0; i <= 50; i++)
        dp[i][0] = dp[i][i] = 1;
    for (int i = 0; i <= 50; i++)
        for (int j = 0; j <= i; j++)
            if (!dp[i][j])
                dp[i][j] = dp[i-1][j-1] + dp[i-1][j];

    cin >> t;
    while (t--) {
        cin >> n >> a >> b >> w;
        int rem = n-a-b;
        ull choose_all = 0, a_wins = 0;
        for (int i = 0; i <= rem; i++) {
            choose_all += dp[rem][i];
            if (a+i > b+rem-i)
                a_wins += dp[rem][i];
        }

        ull left = 100*a_wins;
        ull right = w*choose_all;
        if (left == 0) 
            cout << "RECOUNT!" << endl;
        else if (left > right)
            cout << "GET A CRATE OF CHAMPAGNE FROM THE BASEMENT!"<< endl;
        else 
            cout << "PATIENCE, EVERYONE!" << endl;
    }

    return 0;
}
{% endhighlight %}

