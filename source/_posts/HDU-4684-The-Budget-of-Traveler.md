---
title: HDU 4684 The Budget of Traveler
date: 2013-08-17 10:43:43
tags: acm
---
题意：在树上做斜率优化 （题意是不是太浓缩了= =）
以前感觉毫无想法，必须写下blog加深印象。
dp[i] = dp[j] + (sum[i]-sum[j])*p[i] + r[i]; 转移方程，其中j是i的祖先。
斜率优化要维护队头和队尾，在树上怎么搞呢？ 把树链想成队列，开个fa[]数组记录在每个节点在队列里的前一个元素，更新队尾只要把当前节点的fa设为设为相应的值，更新队头没有办法删除，只能采用三分得到实际应当成为队头的地方，正确性可以根据斜率优化的性质知道，队头不删，那队列中的g()值，也就是状态转移方程的值是先减后增的。
队尾维护时一个个父亲更新过去，最坏情况每个节点都要o(n)，因此也要三分，正确性也可以由斜率优化性质得到。（做完这题，又得重学斜率优化了）。
三分找第k个父亲采用树上倍增，所以总的复杂度是o(n*log(n)*log(n))
```c++
#pragma comment(linker, "/STACK:65536000")
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <string>
#include <iostream>
#include <vector>
#include <map>
#include <cmath>
#include <algorithm>
#define INC(x,y) {x+=y;if(x>=mod) x-=mod;}
#define Max(x,y) {if(y>x) x=y;}
#define Min(x,y) {if(y<x) x=y;}
#define ll long long
#define clr(a,b) memset(a,b,sizeof(a))
using namespace std;

const int N = 500000;
const ll inf = 1e18;
int n;
ll f[N],p[N],r[N],sum[N],num[N],dp[N];
int fa[N][20];
vector<int> e[N];


ll f1(int k,int j) {
    return dp[j] - dp[k];
}
ll f2(int k,int j) {
    return sum[j] - sum[k];
}
ll g(int i,int j) {
    return dp[j] + (sum[i]-sum[j])*p[i] + r[i];
}

int find(int u,int k){
    int c = 0;
    while(k){
        if(k&1) u =  fa[u][c];
        k>>=1;
        c++;
    }
    return u;
}
void getMax(int u,int father){
    int left = 0,right = num[father]-1;
    while(left<right){
        int mid = (left+right)/2;
        int fa0 = find(father,mid);
        int fa1 = fa[fa0][0];

        if(g(u,fa0)>g(u,fa1)) left = mid+1;
        else right = mid;
    }
    dp[u] = g(u,find(father,left));
}
int getFa(int u,int father){
    int left = 0,right = num[father]-1;
    while(left<right){
        int mid = (left+right)/2;
        int f = find(father,mid);
        int ff = fa[f][0];
        if((double)f1(ff,f)*f2(f,u)>=(double)f1(f,u)*f2(ff,f)) left = mid+1;
        else right = mid;
    }
    return find(father,left);
}

void dfs(int u,int father){
    getMax(u,father);
    int tf = getFa(u,father);
    fa[u][0] = tf;
    num[u] = num[tf]+1;
    for(int i=1;(1<<i)<=num[u]-1;i++)
        fa[u][i] = find(tf,(1<<i)-1);

    for(int i=0;i<e[u].size();i++){
        int v = e[u][i];
        if(v==father) continue;
        sum[v] = sum[u] + f[v];
        dfs(v,u);
    }
}
int main(){
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    int T,cas=0;
    scanf("%d",&T);
    while(T--){
        scanf("%d",&n);
        for(int i=1;i<=n;i++){
            e[i].clear();
            scanf("%I64d%I64d%I64d",&f[i],&p[i],&r[i]);
//            scanf("%lld%lld%lld",&f[i],&p[i],&r[i]);
        }
        for(int i=1;i<n;i++){
            int u,v;
            scanf("%d%d",&u,&v);
            e[u].push_back(v);
            e[v].push_back(u);
        }
        num[0] = 1;
        sum[1] = f[1];
        dfs(1,0);
        ll ans = 0;
        for(int i=2;i<=n;i++){
            ans += dp[i];
        }
        printf("Case #%d: %I64d\n",++cas,ans);
//        printf("Case #%d: %lld\n",++cas,ans);
    }
    return 0;
}
```

