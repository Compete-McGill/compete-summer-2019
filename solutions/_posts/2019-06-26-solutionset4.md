---
layout: post
title: Problem Set 4
categories:
  - union-find disjoint sets
  - connected components
---


# Week 4 Solutions

## CodeForces

### [CodeForces 843A: Sorting by Subsequences](https://codeforces.com/problemset/problem/843/A)
{% highlight c++ %}
/*
 * Note the actual values are not needed, only the indices in the sorted result
 *
 * Also note that the values are distinct, so that weird things don't happen
 * with equal elements (e.g. swapping two elements with the same value)
 *
 * We can then make the following obvervation:
 * If the nth element is in the kth position when sorted, n and k must be in 
 * the same subsequence (otherwise we can't get n into the kth position)
 * And, in fact, that's all we need to see to solve the question
 *
 * We can keep a disjoint set of subsequences where we unite subsequences
 * It turns out they must be the same
 *
 * After n unions (1 per element), we extract the subsequences by labelling
 * all the representatives and putting all the elements represented by that
 * labelled representative in the same array
 *
 * Complexity: O(n·α(n))
 */
#include <bits/stdc++.h>
using namespace std;
typedef vector<int> vi;
typedef pair<int,int> ii;
class U {
	private:
		int n,nc;
		vi p,rk,sz;
	public:
		int num() {return nc;}
		U(int v) {
			nc = n = v;
			rk.assign(n,0);
			sz.assign(n,1);
			for(int i=0;i<n;i++) {p.push_back(i);}
		}
		int find(int a) {
			if(a == p[a]) return a;
			return p[a] = find(p[a]);
		}
		bool same(int a, int b) {return find(a) == find(b);}
		int size(int a) {return sz[find(a)];}
		bool un(int a, int b) {
			int x = find(a),y = find(b);
			if(x == y) return false;
			nc--;
			if(rk[x] > rk[y]) {
				p[y] = x;
				sz[x] += sz[y];
				return true;}
			if(rk[x] == rk[y]) rk[y]++;
			p[x] = y;
			sz[y] += sz[x];
			return true;
		}
};
ii v[100100];
int rm[100100];
vi res[100100];
int main() {
	int n;
	scanf("%d ",&n);
	U sub(n);
	for(int i=0;i<n;i++) {
		int t;
		scanf("%d ",&t);
		v[i] = {t,i};
	}
	sort(v,v+n);
	for(int i=0;i<n;i++) {
		sub.un(i,v[i].second);
	}
	int ctr = 0;
	for(int i=0;i<n;i++) {
		if(sub.find(i) == i) {
			rm[i] = ctr++;
		}
	}
	for(int i=0;i<n;i++) {
		res[rm[sub.find(i)]].push_back(i);
	}
	printf("%d\n",ctr);
	for(int i=0;i<ctr;i++) {
		printf("%d",res[i].size());
		for(int j=0;j<res[i].size();j++) {
			printf(" %d",res[i][j]+1);
		}
		printf("\n");
	}
}
{% endhighlight %}
### [CodeForces 886C: Petya and Catacombs](https://codeforces.com/problemset/problem/886/C)
{% highlight c++ %}
/*
 * The hardest part of this question is understanding its description, which 
 * is essentially the following:
 * "Every element from 1 to n has an associated number, possibly pointing to
 * a 'predecessor', and each number can only be the predecessor
 * of one other number"
 * "What is the minimum number of valid 'chains' of predecessors"
 * e.g.
 * 0,1,0,1,3: 
 * Index 1 points to 0,
 * Index 2 points to 1, etc.
 * The minimum number of chains is 3, by index:
 * 2 -> 1 -> 0, 5 -> 3, 4
 *
 * We can show that a greedy solution is correct, in particular:
 * For each element, always try to attach it to its predecessor
 * Otherwise, make a new chain with just that element
 * (one can show that purposefully not doing this saves 0 or fewer chains)
 *
 * In implementation, we can avoid using a DSU or even a graph, opting for a bitset
 * Each bit in the bitset represents index i, where true means it hasn't been used as a predecessor
 * For each index, reset its predecessor to mark it as used
 * The final number of chains is then the number of bits (+1 for index 0) that are set to true
 * These correspond to the elements that end each chain
 *
 * Complexity: O(n)
 */
#include <bits/stdc++.h>
using namespace std;
typedef vector<int> vi;
const int MN = 200200;
bitset<MN> bs;
int main() {
	int n;
	scanf("%d ",&n);
	bs.set();
	for(int i=0;i<n;i++) {
		int t;
		scanf("%d ",&t);
		bs.reset(t);
	}
	printf("%d\n",n+1-MN+bs.count());
}

{% endhighlight %}
### [CodeForces 875B: Sorting the Coins](https://codeforces.com/problemset/problem/875/B)
{% highlight c++ %}
/*
 * It's easier to think about this problem by simulating this process on a few small
 * test cases:
 * e.g. OXOOXOXO
 * The process evolves thus (do this yourself):
 * OXOOXOXO -> OOOXOXOX -> OOOOXOXX -> OOOOOXXX -> OOOOOXXX (terminate)
 * Trying this on other small test cases, we notice that the end essentially
 * accumulates X's (coins in circulation), and we can show that
 * it will add exactly 1 new X per round:
 * Each X not at the end goes straight to the end & each X before that advances
 * to the position of the next X from before
 *
 * Once we know this is true, the hardness of an array is simple:
 * The # of X's - the # of X's at the end + 1
 *
 * But we need to calculate the # of X's at the end n+1 times, which is where
 * we use a DSU
 * In particular we use a DSU to keep track of all the segments of X's
 * Each segment will contain some number of contiguous element from i to j
 * This will mean to us that the segment of X's starts @ i and ends @ j
 *
 * Adding an X at position i is then equivalent to a union of i and i+1
 * (The segment ends 1 later and might end up merging with the segment that
 * starts at i+1, which is desired)
 *
 * It then becomes extremely easy to find the number of X's at the end, since
 * that's the size of the set containing n (the end of the array)
 *
 * Complexity: O(n·α(n))
 */
#include <bits/stdc++.h>
using namespace std;
typedef vector<int> vi;
class U {
	private:
		int n,nc;
		vi p,rk,sz;
	public:
		int num() {return nc;}
		U(int v) {
			nc = n = v;
			rk.assign(n,0);
			sz.assign(n,1);
			for(int i=0;i<n;i++) {p.push_back(i);}
		}
		int find(int a) {
			if(a == p[a]) return a;
			return p[a] = find(p[a]);
		}
		bool same(int a, int b) {return find(a) == find(b);}
		int size(int a) {return sz[find(a)];}
		bool un(int a, int b) {
			int x = find(a),y = find(b);
			if(x == y) return false;
			nc--;
			if(rk[x] > rk[y]) {
				p[y] = x;
				sz[x] += sz[y];
				return true;}
			if(rk[x] == rk[y]) rk[y]++;
			p[x] = y;
			sz[y] += sz[x];
			return true;
		}
};
int main() {
	int n;
	scanf("%d ",&n);
	U bs(n+1);
	printf("1");
	for(int i=0;i<n;i++) {
		int t;
		scanf("%d ",&t);t--;
		bs.un(t,t+1);
		printf(" %d",i+3-bs.size(n));
	}
	printf("\n");
}
{% endhighlight %}
### [CodeForces 1139C: Edgy Trees](https://codeforces.com/problemset/problem/1139/C)
{% highlight c++ %}
/*
 * This one includes some counting and the weird number 10^9+7
 * The number is just to make sure that, if we had infinite space, you would
 * have gotten the right answer anyway (but we don't, since ints can only go
 * so high)
 * But we do need to make sure our numbers don't overflow
 * To do this one, we use a counting trick:
 * Instead of trying to count the number of valid paths,
 * we count the number of invalid paths
 * Then, valid paths = n^k - invalid paths
 * It's much easier to find invalid paths: they only stay within one group
 * of vertices connected with red edges
 *
 * Or, more precisely...
 *
 * a CONNECTED COMPONENT with red edges
 *
 * Then, the number of invalid paths is the sum of the sizes of each connected component ^k
 * We can find the size of each connected component with either a DSU or DFS with coloring
 *
 * Be careful to do all the math modulo 10^9+7
 *
 * Compelxity: O(n·k) or O(n·log(k)) with binary exponentiation
 */
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef vector<int> vi;
typedef vector<vi> vvi;
ll dp[100100];
int n,k;
const ll M = 1e9+7;
inline ll mul(ll a, ll b) {return (a*b)%M;}
ll bp(ll b) {
	ll id = b;
	if(dp[id] != -1) {return dp[b];}
	ll ac = 1,p=k;
	while(p > 0) {
		if(p&1) {ac = mul(ac,b);}
		b = mul(b,b);
		p >>= 1;
	}
	return dp[id] = ac;
}
vvi g;
bitset<100100> bs;
int dfs(int v) {
	bs.set(v);
	int res = 1;
	for(int i=0;i<g[v].size();i++) {
		int u = g[v][i];
		if(bs[u]) continue;
		bs.set(u);
		res += dfs(u);
	}
	return res;
}
int main() {
	memset(dp,-1,sizeof(dp));
	scanf("%d %d ",&n,&k);
	g.assign(n,vi());
	for(int i=0;i<n-1;i++) {
		int a,b,c;
		scanf("%d %d %d ",&a,&b,&c);
		a--;b--;
		if(!(c&1)) {
			g[a].push_back(b);
			g[b].push_back(a);
		}
	}
	ll tot = bp(n);
	ll rt = 0;
	for(int i=0;i<n;i++) {
		ll val = bs[i]?0:dfs(i);
		rt += bp(val);
		rt %= M;
	}
	tot = tot - rt + M;
	cout << tot%M << '\n';
}
{% endhighlight %}
### [CodeForces 1131F: Asya and Kittens](https://codeforces.com/problemset/problem/1131/F)
{% highlight c++ %}
/*
 * Welcome to the twilight zone, where only the funkiest answers get AC
 *
 * It's pretty obvious that a DSU is involved, but we also need to keep the 
 * connected components in order to stick them onto each other when the 
 * kittens share cages
 *
 * This is easier than it sounds (maybe):
 * We can keep a linked list along with the DSU so that whenever 2 kittens are
 * put in the same cage, we can make it so that one segment goes right before/after
 * the other by making the end of one list point to the start of another
 *
 * Once we're done, iterate through the linked list to get the kittens in order
 * The resulting solution must be valid, since at each step we forces the
 * groups of kitten cages to be consecutive
 *
 * Alternatively we can...
 *
 * KEEP THE WHOLE ARRAY IN THE DSU (I didn't do that here but you can)
 *
 * When unioning, just stick one array onto the end of the other
 *
 * By switching from union by rank to union by size, we can show that n unions
 * will take n log(n) time, not n^2 time
 *
 * Complexity: O(n·α(n)) with linked list, O(n·log(n)·α(n)) with whole array
 */
#include <bits/stdc++.h>
using namespace std;
typedef vector<int> vi;
typedef vector<vi> vvi;
class U {
	private:
		int n,nc;
		vi p,rk,sz;
		vi nx,st,ed;
	public:
		int num() {return nc;}
		U(int v) {
			nc = n = v;
			rk.assign(n,0);
			sz.assign(n,1);
			for(int i=0;i<n;i++) {p.push_back(i);st.push_back(i);ed.push_back(i);nx.push_back(-1);}
		}
		int find(int a) {
			if(a == p[a]) return a;
			return p[a] = find(p[a]);
		}
		bool same(int a, int b) {return find(a) == find(b);}
		int size(int a) {return sz[find(a)];}
		int nxt(int v) {
			return nx[v];
		}
		bool un(int a, int b) {
			int x = find(a),y = find(b);
			if(x == y) return false;
			nc--;
			if(rk[x] > rk[y]) {
				nx[ed[x]] = st[y];
				ed[x] = ed[y];
				st[y] = st[x];
				p[y] = x;
				sz[x] += sz[y];
				return true;}
			if(rk[x] == rk[y]) rk[y]++;
			nx[ed[y]] = st[x];
			ed[y] = ed[x];
			st[x] = st[y];
			p[x] = y;
			sz[y] += sz[x];
			return true;
		}
};
int main() {
	int n;
	scanf("%d ",&n);
	U bs(n);
	for(int i=0;i<n-1;i++) {
		int a,b;
		scanf("%d %d ",&a,&b);
		a--;b--;
		bs.un(a,b);
	}
	int id = bs.find(0);
	vi res;
	while(id != -1) {
		res.push_back(id);
		id = bs.nxt(id);
	}
	for(int i=0;i<res.size();i++) {
		if(i > 0) {printf(" ");}
		printf("%d",res[i]+1);
	}
	printf("\n");
}
{% endhighlight %}
### [CodeForces 1012B: Chemical table](https://codeforces.com/problemset/problem/1012/B)
{% highlight c++ %}
/*
 * 99% of this problem is figuring out the idea for the solution
 * and why we even need a DSU, as the code is deceptively short 
 * (DSU template code + 11 lines)
 * 
 * By testing out some test cases we notice something funky:
 * Whenever we reach the limit of what we can fuse together, the elements we
 * formed on the grid make these weird "broken" rectangles
 *
 * When we add/buy an element on the same row/column as the rectangle,
 * by fusion it fills in the rest of the row/column
 *
 * Furthermore, we can have 2 or more rectangles that never meet on a row
 * or column
 * And when 2 rectangles do meet on a row/column, 
 * they do this weird merging thing where they turn into 1 big rectangle
 *
 * CHECK & VERIFY THESE PROPERTIES YOURSELF, AS OTHERWISE WHAT COMES AFTER
 * RELIES HEAVILY ON INTUITION AND THE ABOVE MAKING SENSE
 *
 * If your intuition is strong, this will feel uncannily like what a DSU does:
 * Rectangles that don't share rows/columns <-> Disjoint sets
 * The rectangle merging thing <-> Set union
 * The filling row/column thing when adding an element <-> 
 * Also set union, just with 1 element (a 1x1 rectangle is still a rectangle)
 *
 * When we try to translate from rectangle-world to DSU world, we realize that
 * they are indeed the same:
 * The grid is a DSU, not with n·m elements but n+m: One for each r value and
 * one for each c value
 * 
 * In a single connected component, we will have some row elements and 
 * some column elements:
 * Every element whose row AND column are in the same connected component can
 * be obtained through fusion (or is already present)
 *
 * Adding a new element represents a union of a row and a column
 * (Now the row and column are in the same connect component via that element)
 *
 * Obtaining a new element through fusion is just filling a connected component
 *
 * We can even verify that the fusion criteria hold according to our rules,
 * meaning that our DSU representation is correct
 *
 * (If an element not present has a representation in a DSU, there must have
 * been an element with the same row, one with the same column,
 * same column, and a third one that shares a row/column with the first 2
 * to get the DSU to exist)
 * 
 * In order to get our answer out of all this weirdness, we acknowledge that
 * The whole board will be filled if and only if there is 1 connected component
 *
 * Since adding an element is equal to a union of 2 connected components
 * (and it's not too hard to show that any 2 connected components can
 * by unioned this way),
 *
 * Our answer is the number of connected componts - 1 after all the unions
 * from adding existing elements
 *
 * Complexity: O(q·α(n+m))
 */
#include <bits/stdc++.h>
using namespace std;
typedef vector<int> vi;
class U {
	private:
		int n,nc;
		vi p,rk,sz;
	public:
		int num() {return nc;}
		U(int v) {
			nc = n = v;
			rk.assign(n,0);
			sz.assign(n,1);
			for(int i=0;i<n;i++) {p.push_back(i);}
		}
		int find(int a) {
			if(a == p[a]) return a;
			return p[a] = find(p[a]);
		}
		bool same(int a, int b) {return find(a) == find(b);}
		int size(int a) {return sz[find(a)];}
		bool un(int a, int b) {
			int x = find(a),y = find(b);
			if(x == y) return false;
			nc--;
			if(rk[x] > rk[y]) {
				p[y] = x;
				sz[x] += sz[y];
				return true;}
			if(rk[x] == rk[y]) rk[y]++;
			p[x] = y;
			sz[y] += sz[x];
			return true;
		}
};
int main() {
	int n,m,q;
	scanf("%d %d %d ",&n,&m,&q);
	U S(n+m);
	while(q--) {
		int a,b;
		scanf("%d %d ",&a,&b);
		S.un(a-1,n+b-1);
	}
	printf("%d\n",S.num()-1);
}
{% endhighlight %}


## Kattis

### [Almost Union-Find](https://open.kattis.com/problems/almostunionfind)
{% highlight c++ %}
/*
 * This problem is almost simulating the union-find disjoint set data structure,
 * except that we are now adding another operation: move, and the problem also
 * asks us for the sum of each disjoint set.
 *
 * In the classic union-find disjoint set DS, an element is strongly tied to its
 * set representative from the beginning. Updating an element's representative
 * means you are updating all other elements within the same set as well.
 *
 * In order to move one single element, we add another array (I call it set_id).
 * Basically for an element i, set_id[i] means "your set information will be stored
 * in set_parent[set_id[i]] and not set_parent[i] itself". This way, we can update
 * a single element's set information without affecting other elements.
 */

#include <bits/stdc++.h>

#define vi vector<int>
#define ii pair<int,int>
#define vii vector<pair<int,int>>

typedef long long ll;
typedef unsigned long long ull;

#define pb push_back
#define mp make_pair
#define fi first
#define se second

using namespace std;

const int N = 100100;
int n,m,op,a,b;
int set_parent[N], set_size[N], set_id[N];
ull set_sum[N];

int find_set (int i) {
    if (set_parent[i] == i)
        return i;
    else
        return (set_parent[i] = find_set(set_parent[i]));
}

void union_set (int a, int b) {
    int x = find_set(set_id[a]);
    int y = find_set(set_id[b]);
    if (x != y) {
        set_parent[y] = set_id[b] = x;
        set_size[x] += set_size[y];
        set_sum[x] += set_sum[y];
    }
}

void move (int a, int b) {
    // move a to the set containing b
    int x = find_set(set_id[a]);
    int y = find_set(set_id[b]);
    if (x != y) {
        set_id[a] = y;
        set_size[x]--;
        set_size[y]++;
        set_sum[x] -= a;
        set_sum[y] += a;
    }
}

int main (void) {
    ios_base::sync_with_stdio(false);

    while (cin >> n >> m) {
        for (int i = 1; i <= n; i++) {
            set_parent[i] = set_sum[i] = set_id[i] = i;
            set_size[i] = 1;
        }

        while (m--) {
            cin >> op;
            if (op == 1) {
                cin >> a >> b;
                union_set(a,b);
            } else if (op == 2) {
                cin >> a >> b;
                move(a,b);
            } else {
                cin >> a;
                int x = find_set(set_id[a]);
                cout << set_size[x] << " " << set_sum[x] << endl;
            }
        }
    }

    return 0;
}
{% endhighlight %}

### [Association for Control Over Minds](https://open.kattis.com/problems/control)
{% highlight c++ %}
/*
 * On reading a new recipe, we count how many ingredients have already been used,
 * and how many are unused. If all ingredients are available, we must concoct
 * that recipe. Otherwise, the current recipe must contain the same amount of
 * ingredients as all the recipes that are to be added to this new recipe.
 */

#include <bits/stdc++.h>

#define vi vector<int>
#define ii pair<int,int>
#define vii vector<pair<int,int>>

typedef long long ll;
typedef unsigned long long ull;

#define pb push_back
#define mp make_pair
#define fi first
#define se second

using namespace std;

int n,m,a,b, result = 0;
int set_parent[200100], set_size[200100], recipe_id[500100];

int find_set (int i) {
    return (set_parent[i] == i) ? i : (set_parent[i] = find_set(set_parent[i]));
}

int union_set (int a, int b) {
    int x = find_set(a);
    int y = find_set(b);
    if (x != y) {
        set_parent[y] = x;
        set_size[x] += set_size[y];
    }
}

int main (void) {
    ios_base::sync_with_stdio(false);

    scanf("%d\n", &n);
    for (int i = 1; i <= n; i++) {
        set_parent[i] = i;
        set_size[i] = 0;
        scanf("%d\n", &m);

        map<int, int> to_concoct;
        vector<int> unused_ingredients;

        // counting used and unused ingredients
        for (int j = 0; j < m; j++) {
            scanf("%d", &a);
            int recipe = find_set(recipe_id[a]);
            if (recipe != 0)
                to_concoct[recipe]++;
            else
                unused_ingredients.pb(a);
        }

        // can we concoct this recipe?
        bool can_concoct = true;
        for (auto it : to_concoct) {
            if (it.second != set_size[find_set(it.first)]) {
                can_concoct = false;
                break;
            }
        }

        // if we can concoct the current recipe, we must do that
        if (can_concoct) {
            for (int a : unused_ingredients) {
                recipe_id[a] = i;
                set_size[i]++;
            }
            for (auto it : to_concoct)
                union_set(it.first, i);
            result++;
        }
    }

    printf("%d\n", result);

    return 0;
}
{% endhighlight %}

### [Coast Length](https://open.kattis.com/problems/coast)
{% highlight c++ %}
/*
 * Some definitions:
 *  - Sea coast: all borders between land and sea
 *  - Sea: any water connected to an edge of the map only through water
 * For this problem, one approach is to find connected components using BFS.
 * We want to visit all sea components instead of islands, so we can
 * start BFS from every point on the 4 edges of the map. Everytime our BFS
 * reaches a land unit, we increment the coast length (our final result) by one,
 * since each part of the coast can only be reached exactly once by BFS.
 */

#include <bits/stdc++.h>

#define vi vector<int>
#define ii pair<int,int>
#define vii vector<pair<int,int>>

typedef long long ll;
typedef unsigned long long ull;

#define pb push_back
#define mp make_pair
#define fi first
#define se second

using namespace std;

const int N = 1010;
int n,m;
bool graph[N][N];
bool visit[N][N];
int result = 0;
// array used to retrieve the neighbouring vertices.
vii adj { {-1,0}, {1,0}, {0,-1}, {0,1} };

void bfs (int x, int y) {
    if (visit[x][y])
        return;
    if (graph[x][y]) {
        // this is a land unit
        result++;
        return;
    }

    queue<ii> q;
    q.push(mp(x,y));
    visit[x][y] = true;
    while (!q.empty()) {
        ii u = q.front(); q.pop();
        for (ii p : adj) {
            // neighbouring vertex
            ii v = mp(u.fi+p.fi, u.se+p.se);
            // if the new coordinates is still within the grid
            if ((v.fi >= 0 && v.fi < n)
                    && (v.se >=0 && v.se < m)) {
                if (!graph[v.fi][v.se]) {
                    // this is a sea unit, it must be unvisited to be added to the queue
                    if (!visit[v.fi][v.se]) {
                        visit[v.fi][v.se] = true;
                        q.push(v);
                    }
                } else {
                    // this is a land unit
                    result++;
                }
            }
        }
    }
}

int main (void) {
    ios_base::sync_with_stdio(false);

    scanf("%d %d\n", &n, &m);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            graph[i][j] = (getchar() == '1');
        }
        getchar();
    }

    // BFS left and right edges
    for (int i = 0; i < n; i++) {
        bfs(i,0);
        bfs(i,m-1);
    }

    // BFS top and bottom edges
    for (int i = 0; i < m; i++) {
        bfs(0,i);
        bfs(n-1,i);
    }

    printf("%d\n", result);

    return 0;
}
{% endhighlight %}

