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
### [CodeForces 886A: Petya and Catacombs](https://codeforces.com/problemset/problem/886/C)
### [CodeForces 875B: Sorting the Coins](https://codeforces.com/problemset/problem/875/B)
### [CodeForces 1139C: Edgy Trees](https://codeforces.com/problemset/problem/1139/C)
### [CodeForces 1131F: Asya and Kittens](https://codeforces.com/problemset/problem/1131/F)
### [CodeForces 1012B: Chemical table](https://codeforces.com/problemset/problem/1012/B)


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

