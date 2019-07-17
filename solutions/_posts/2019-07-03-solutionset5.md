---
layout: post
title: Problem Set 5
categories:
  - combinatorics
---


# Week 5 Solutions

## CodeForces

### [CodeForces 630F: Selection of Personnel](https://codeforces.com/problemset/problem/630/F)

{% highlight c++ %}
/*
 * Formula check:
 * You can verify that the answer is exactly nC5 + nC6 + nC7
 * To avoid integer overflow, be careful about where you multiply numbers
 * (it's not hard to show that nC(k+1) = nCk * (n-k)/(k+1), which helps
 * avoid overflow)
 *
 */
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll F = 120;
int main() {
	int n;
	cin >> n;
	ll res = 1;
	ll tot = 0;
	for(int i=0;i<5;i++) {
		res *= n;
		n--;
	}
	res /= 120;
	for(int i=0;i<2;i++) {
		tot += res;
		res *= n;n--;
		res /= (5+i+1);
	}
	tot += res;
	cout << tot << '\n';
}
{% endhighlight %}
### [CodeForces 630G: Challenge Pennants](https://codeforces.com/problemset/problem/630/G)
{% highlight c++ %}
/*
 * Formula check:
 * The stars & bars method helps show the formula
 * The solution is (n+4)C5 * (n+2)C3
 * Be careful about multiplication to avoid overflow (or use Java & BigInteger)
 */
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int main() {
	ll n;
	cin >> n;
	ll ta = 1,tb = 1;
	ll a = n+4;
	ll b = n+2;
	for(int i=0;i<5;i++) {
		ta *= a;
		ta /= (i+1);
		a--;
	}
	for(int i=0;i<3;i++) {
		tb *= b;
		tb /= (i+1);
		b--;
	}
	cout << ta*tb << '\n';
}
{% endhighlight %}
### [CodeForces 1096B: Substring Removal](https://codeforces.com/problemset/problem/1096/B)
{% highlight c++ %}
/*
 * When we remove a continuous substring, there will be a remaining left part
 * and a remaining right part (both possibly empty)
 * For a particular removal to be valid, the left part must all be the same character,
 * and the right part must all be the same character as the left part too
 * Clearly the last character determines what character the right part is all
 * equal to
 * and the first char does the same to the left
 * We can then calculate the max length that are the same char for
 * both sides (which is also the number of nonempty substrings that are the same char
 * for each side)
 *
 * If the first & last chars are different, clearly one of the left or right
 * parts remaining must actually be empty (or else it won't all be the same)
 * So the answer would be size_of_left + size_of_right + 1
 * (+1 because both sides can be empty)
 *
 * If the first & last chars are the same, for each left length (+1 for empty left side), we can do
 * all right lengths (+1 for empty right side)
 * This gives (size_of_left+1)*(size_of_right+1) 
 *
 * Remember to modulo by 998244353 because the problem says so
 * (even though the actual number fits in a long long)
 */
#include <bits/stdc++.h>
#define MD 998244353
using namespace std;
typedef long long ll;
char s[200100];

int main() {
	int n;
	scanf("%d ",&n);
	scanf("%s ",&s);
	char st=s[0],ed=s[strlen(s)-1];
	ll sl=0,el=0;
	for(int i=0;s[i]==st;i++) {sl++;}
	int l = strlen(s);
	for(int i=0;s[l-i-1]==ed;i++) {el++;}
	ll ret;
	if(st == ed) {ret = (sl+1)*(el+1);} else {
		ret = sl+el+1;
	}
	cout << ret%MD << endl;
}
{% endhighlight %}
### [CodeForces 1061C: Multiplicity](https://codeforces.com/problemset/problem/1061/C)
{% highlight c++ %}
/*
 * This one's DP (oof)
 * We effectively solve this with bottom-up DP with clever tricks to keep
 * the number of operations low
 * DP State: dp[i][] = number of good subsequences of length i
 * DP array is 2d for reasons we will see later (bottom up DP memory trick)
 *
 * For each a_i in the array, we can stick onto the end of a subsequence
 * its new length divides a_i, thus
 * dp[i][] += dp[i-1][] if i divides a_i
 *
 * The weird 2D-ness of the array is to avoid mixing old DP states & new ones
 * (see the bottom up DP memory trick)
 *
 * The DP array is n long & it is updated n times, so it looks like the 
 * algorithm is O(n^2) (TLE), but we can make it O(n^(4/3)*sqrt(MAX_N)) (AC, trust me)
 * In particular, we can factor a number N in O(sqrt(N)) time
 * We can then use this info to only the DP states of factors of a_i, since
 * all the other states don't change
 * 
 * You might be worried about too many factors, but it is well known (AKA memorize this) that a number N has about O(n^(1/3)) factors
 * (In fact, the # of factors grows sub-polynomially, but O(n^1/3) is a good working bound)
 * Thus, we can solve it in O(n^(4/3)*sqrt(MAX_N))
 * (O(n^1/3) isn't actually good enough to show that this really does AC, but whatever)
 */
#include <bits/stdc++.h>
#define mp make_pair
#define MD 1000000007
using namespace std;
typedef long long ll;
typedef vector<int> vi;
ll dp[100100][4];
ll v[100100];
vi fac(int n) {
	vi res;
	res.push_back(1);
	int c = 0;
	while(!(n%2)) {
		c++;
		n /= 2;
	}
	int val = 1;
	for(int i=0;i<c;i++) {
		val *= 2;
		res.push_back(val);
	}
	for(int i=3;i*i<=n;i+=2) {
		c=0;
		while(!(n%i)) {
			c++;
			n /= i;
		}
		val = 1;
		int sz = res.size();
		for(int j=0;j<c;j++) {
			val *= i;
			for(int k=0;k<sz;k++) {
				res.push_back(val*res[k]);
			}
		}
	}
	if(n > 1) {
		int sz = res.size();
			for(int k=0;k<sz;k++) {
				res.push_back(n*res[k]);
			}
	}
	return res;
}
int main() {
	int n;
	cin >> n;
	memset(dp,0,sizeof(dp));
	for(int i=0;i<n;i++) {
		int t;
		scanf("%d ",&t);
		v[i] = t;
	}
	dp[0][0] = 1;
	dp[0][1] = 1;
	for(int i=0;i<n;i++) {
		vi f = fac(v[i]);
		for(int k=0;k<f.size();k++) {
			int j = f[k];
			if(j > 100000) {continue;}
			dp[j][(i+1)%2] += dp[j-1][i%2];
			dp[j][(i+1)%2] %= MD;
		}
		for(int k=0;k<f.size();k++) {
			int j = f[k];
			if(j > 100000) {continue;}
			dp[j][(i)%2] = dp[j][(i+1)%2];
		}
	}
	ll sm = 0;
	for(int i=1;i<=n;i++) {
		sm += dp[i][n%2];
	}
	cout << sm%MD << '\n';
}
{% endhighlight %}
### [CodeForces 1084C: The Fair Nut and String](https://codeforces.com/problemset/problem/1084/C)
{% highlight c++ %}
/*
 * The answer is easy to visualize and annoying to count:
 * The string is essentially a's split up between b's
 * and we're asked to count the number of subsequences where
 * we pick at most 1 'a' from each block of a's
 *
 * We do this by keeping a running count of the # of values between blocks:
 * If we have N ways to do it before reaching a new block of length l,
 * We can use any of the l a's to add to any of our previous valid solutions
 * OR we can ignore this block and keep our N ways
 * OR we can start a completely new subsequence from one of these l a's
 * (because the null set is not a valid subsequence)
 *
 * Use modulo often to avoid overflow
 */
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef vector<ll> vl;
int main() {
	int ct=0;
	char c;
	vl v;
	while((c = getchar()) != EOF) {
		if(c == 'a') {ct++;}
		if(c == 'b') {v.push_back(ct);ct=0;}
	}
	v.push_back(ct);
	ll tot=0;
	for(int i=0;i<v.size();i++) {
		tot = tot+v[i]*(tot+1);
		tot %= 1000000007;
	}
	cout << tot << endl;
}
{% endhighlight %}
### [CodeForces 1105C: Ayoub and Lost Array](https://codeforces.com/problemset/problem/1105/C)
{% highlight c++ %}
/*
 * Counting & DP go together a lot
 * This one is screaming for DP to get involved
 * DP States: dp[i][j] = The number of arrays with length i that are j mod 3
 * DP Transitions: For each dp[i][j], we can add:
 * A number that is 0 mod 3, 1 mod 3, or 2 mod 3
 * To arrive at state dp[i+1][(j+k)%3] C times (having added k mod 3) where C is the number of values that are k mod 3
 * For a number n, there are (n+i-1)/k nonnegative numbers that are i mod k;
 * Thus, between l and r (inclusive) we have (r+i)/k - (l+i-1)/k numbers
 *
 * Use modulo frequently to avoid overflow
 *
 * There are 3*n dp states, each of which involves 3 transitions, for
 * O(N) complexity
 */
#include <bits/stdc++.h>
#define MD 1000000007
using namespace std;
typedef long long ll;
int n,l,r;
ll dp[200100][4];
int main() {
	scanf("%d %d %d ",&n,&l,&r);
	r++;
	memset(dp,0,sizeof(dp));
	dp[0][0] = 1;
	ll p[3] = {((r+2)/3)-((l+2)/3),((r+1)/3)-((l+1)/3),((r)/3)-((l)/3)};
	for(int i=0;i<n;i++) {
		for(int j=0;j<3;j++) {
			for(int k=0;k<3;k++) {
				dp[i+1][(j+k)%3] += dp[i][j]*p[k];
				dp[i+1][(j+k)%3] %= MD;
			}
		}
	}
	cout << dp[n][0]%MD << '\n';
}
{% endhighlight %}
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

