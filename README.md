```cpp
//A+B problem
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll N = 1e4 + 15, mod = 998244353, inf = 1e10;

//网络最大流版 
namespace Part1 {
	int n, m, s, t;
	int head[100], to[100], nxt[100], idx;
	ll val[100];
	int dep[100];
	int cur[100];
	void add (int u, int v, ll w) {
		to[idx] = v;
		val[idx] = w;
		nxt[idx] = head[u];
		head[u] = idx ++;
	}
	bool bfs () {
		memset (dep, 0, sizeof dep);
		queue <int> q;
		q.push(s);
		dep[s] = 1;
		while (!q.empty()) {
			int u = q.front();
			q.pop();
			for (int i = head[u]; i != -1; i = nxt[i]) {
				int v = to[i];
				if (!dep[v] && val[i]) {
					dep[v] = dep[u] + 1;
					q.push(v);
					if (v == t) return 1;
				}
			}
		}
		return 0;
	}
	ll dfs (int u, ll mf) {
		if (u == t) return mf;
		ll sum = 0;
		for (int i = cur[u]; i != -1; i = nxt[i]) {
			cur[u] = i;
			int v = to[i];
			if (dep[v] == dep[u] + 1 && val[i]) {
				ll f = dfs (v, min (val[i], mf));
				val[i] -= f;
				val[i ^ 1] += f;
				mf -= f;
				sum += f;
				if (mf == 0) break;
			}
		}
		if (sum == 0) dep[u] = 0;
		return sum;
	}
	ll Dinic () {
		ll flow = 0;
		while (bfs ()) {
			memcpy (cur, head, sizeof head);
			flow += dfs (s, inf);
		}
		return flow;
	}
	void solve () {
		memset (head, -1, sizeof head);
	    n = 3, m = 2;
	    s = 0, t = n + 1;
	    add (s, 1, inf);
	    add (1, s, 0);
		for (int i = 1; i <= m; ++ i) {
			ll u = 1, v = i + 1, w;
			scanf("%lld", &w);
			add (u, v, w);
			add (v, u, 0);
		}
		for (int i = 2; i <= 3; ++ i) {
			add (i, t, inf);
			add (t, i, 0);
		}
		printf ("%lld\n", Dinic ());
	}
}

//暴力版 
namespace Part2 {
	void solve () {
		ll a, b, ans = 0;
		scanf ("%lld%lld", &a, &b);
	    if (a < 0) {
	        for (int i = 1; i <= -a; ++ i) ans --;
	    } else  for (int i = 1; i <= a; ++ i) ans ++;
		if (b < 0) {
	        for (int i = 1; i <= -b; ++ i) ans --;
	    } else  for (int i = 1; i <= b; ++ i) ans ++;
	    printf ("%lld\n", ans);
	}
}

//正常版 
namespace Part3 {
	void solve () {
		ll a, b;
		cin >> a >> b;
		cout << a + b << endl;
	}
}

//线段树版 
namespace Part4 {
	int n = 2;
	ll a[10];
	struct tree {
		int l, r;
		ll v;
	}tr[10 << 2];
	
	void push_up (int u) {
		tr[u].v = tr[u << 1].v + tr[u << 1 | 1].v;
	}
	
	void build (int u, int l, int r) {
		tr[u].l = l, tr[u].r = r;
		if (l == r) {
			tr[u].l = a[l];
			return ;
		}
		int mid = l + r >> 1;
		build (u << 1, l, mid);
		build (u << 1 | 1, mid + 1, r);
		push_up (u);
	}
	
	void update (int u, int x, int c) {
		if (tr[u].l == tr[u].r) {
			tr[u].v = c;
			return ;
		}
		int mid = tr[u].l + tr[u].r >> 1;
		if (x <= mid) update (u << 1, x, c);
		else update (u << 1 | 1, x, c);
		push_up (u);
	}
	
	ll query (int u, int l, int r) {
		if (tr[u].l >= l && tr[u].r <= r) {
			return tr[u].v;
		}
		int mid = tr[u].l + tr[u].r >> 1;
		ll ans = 0;
		if (l <= mid) ans += query (u << 1, l, r);
		if (r > mid) ans += query (u << 1 | 1, l, r);
		return ans;
	}
	
	void solve () {
		for (int i = 1; i <= n; ++ i) scanf ("%d", &a[i]);
		build (1, 1, n);
		for (int i = 1; i <= n; ++ i) update (1, i, a[i]);
		printf ("%lld\n", query (1, 1, n));
	}
}

//树状数组版 
namespace Part5 {
	int n = 2;
	ll a[10], tr[100];
	
	int lowbit (int x) {return x & -x;}
	
	void add (int x, int k) {
		for (; x <= n; x += lowbit (x)) 
			tr[x] += k;
	}
	
	ll sum (int x) {
		ll ans = 0;
		for (; x; x -= lowbit (x)) 
			ans += tr[x];
		return ans;
	}
	
	void solve () {
		for (int i = 1; i <= n; ++ i) 
			scanf ("%d", &a[i]);
		for (int i = 1; i <= n; ++ i) 
			add (1, a[i]);
		printf ("%lld\n", sum (1));
	}
}

int main () {
	srand ((unsigned)time (NULL));
	int k = rand () % 5 + 1;
	if (k == 1) Part1::solve ();
	else if (k == 2) Part2::solve ();
	else if (k == 3) Part3::solve ();
	else if (k == 4) Part4::solve ();
	else Part5::solve ();
	return 0;
}
```
