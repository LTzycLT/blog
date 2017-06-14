---
title: HDU 4654 k-edge connected components
date: 2013-08-07 12:44:38
tags: acm
---


题意：求一个无向图中，k联通分量的个数。

迭代过程：
用Stoer_Wagner求出当前全局最小割，判断是否大于k，是就返回，不是就按割边把图分成两个部分继续迭代。
用Soter_Wagner按最小割把图分为两部分的方法：此算法一直把点合并，当合并到某一状态时，当前最小割被更新了，那么对于当前最小割 ，"prim"时最后加入的那个点及和他合并的点就是一部分，剩余的点就是另一部分，得到最小割时划分的两部分即可。

```
#include <stdio.h>
#include <string.h>
#include <vector>
#include <iostream>
#include <set>
#include <map>
#include <queue>
#define ll long long
#define clr(a,b) memset(a,b,sizeof(a))
using namespace std;

const int N = 105;

const int inf = 1e8;
int g[N][N],a[N][N],p[N];
bool vis[N],combine[N],par[N];
int d[N],node[N],st[N],k,s,t;
vector<int> vst[N];
vector<int> pa,pb;

int prim(int n){
    clr(vis,0);
    clr(d,0);
    int mincut = 0;
    int tmp = -1;
    s =-1, t =-1;
    int top = 0;
    for(int i=0;i<k;i++){
        int maxi = -inf;
        for(int j=0;j<k;j++){
            int u = node[j];
            if(!combine[u]&&!vis[u]&&d[u]>maxi){
                tmp = u;
                maxi = d[u];
            }
        }
        st[top++] = tmp;
        vis[tmp] = true;
        if(i==k-1)
            mincut=d[tmp];
        for(int j=0;j<k;j++){
            int u=node[j];
            if(!combine[u]&&!vis[u])
                d[u] += g[tmp][u];
        }
    }
    s = st[top-2];
    t = st[top-1];
    for(int i=0;i<top;i++)  node[i] = st[i];
    return mincut;
}
int Stoer_Wagner(int n){
    for(int i=0;i<n;i++){
        vst[i].clear();
        vst[i].push_back(i);
    }

    int ans = inf;
    clr(combine,0);
    for(int i=0;i<n;i++)
        node[i] = i;
    for(int i=1;i<n;i++){
        k = n-i+1;
        int cur = prim(n);
        if(cur<ans){
            ans =cur;
            for(int j=0;j<n;j++) par[j] = 0;
            for(int j=0;j<vst[t].size();j++){
                par[vst[t][j]] = 1;
            }

        }
        combine[t] = true;

        for(int j=0;j<vst[t].size();j++){
            vst[s].push_back(vst[t][j]);
        }
        for(int j=0;j<n;j++){
            if(j==s) continue;
            if(!combine[j]){
                g[s][j] += g[t][j];
                g[j][s] += g[j][t];
            }
        }
    }
    pa.clear();pb.clear();
    for(int i=0;i<n;i++)
        if(par[i]) pa.push_back(i);
        else pb.push_back(i);
    return ans;
}


int K;


int dfs(vector<int> t){
    int n = t.size();
    for(int i=0;i<n;i++) for(int j=0;j<n;j++) g[i][j] = a[t[i]][t[j]];
    if(Stoer_Wagner(n)>=K) return 1;
    vector<int> x,y;
    for(int i=0;i<pa.size();i++) x.push_back(t[pa[i]]);
    for(int i=0;i<pb.size();i++) y.push_back(t[pb[i]]);
    return dfs(x) + dfs(y);
}
int main(){
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    int n,m;
    while(scanf("%d%d%d",&n,&m,&K)!=EOF){
        clr(a,0);
        for(int i=0;i<m;i++){
            int u,v;
            scanf("%d%d",&u,&v);
            u--;v--;
            a[u][v] += 1;
            a[v][u] += 1;
        }
        vector<int> t;
        for(int i=0;i<n;i++) t.push_back(i);
        printf("%d\n",dfs(t));
    }
    return 0;
}
```

