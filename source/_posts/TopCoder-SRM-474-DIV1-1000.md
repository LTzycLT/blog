---
title: TopCoder SRM 474 DIV1 1000
date: 2013-08-12 14:33:08
tags: acm
---

题意：把一棵树对应到一张无向图上有多少种方案，即满足树上两点之间有边，那图上对应的两点之间也有边的方案。N<=14

只要想到这是树型dp，这题就不难，状态为dp[i][j][mask]，表示到树上节点为i对应图上节点为j时它及子树已经用过图上节点mask的方案数。mask是状态压缩。
转移的时候尽量优化，否则会tle。
```c++
#include <cstdio>
#include <iostream>
#include <cstring>
#include <sstream>
#include <algorithm>
#include <cmath>
#include <vector>
#define INC(x,y) x = (x+y)%mod;
#define clr(x,y) memset(x,y,sizeof(x))
using namespace std ;
typedef long long ll ;
const double eps = 1e-6 ;

const ll mod = 1000000007;
int dp[15][15][1<<14];
int p[15][1<<14];
int g[20][20],t[20][20];
int n;

class GameWithGraphAndTree
{
public:
    void dfs(int u,int f){
        int fb = 0;
        for(int v=0;v<n;v++){
            if(!t[u][v] || v==f) continue;
            dfs(v,u);
            if(fb == 0){
                fb = 1;
                for(int i=0;i<n;i++)
                    for(int j=0;j<n;j++)
                        if(g[i][j])
                        for(int k=0;k<(1<<n);k++)
                            if(((k>>i)&1)==0){
                                INC(dp[u][i][k|(1<<i)],dp[v][j][k]);
                            }
            }
            else{
                clr(p,0);
                for(int i=0;i<n;i++)
                    for(int k=0;k<(1<<n);k++)
                    if(((k>>i)&1) && dp[u][i][k])
                        for(int j=0;j<n;j++)
                        if(g[i][j]){
                            int rt = (1<<n)-1-k;
                            for(int l=rt;l>0;l=(l-1)&rt){
                                if((l>>j)&1){
                                    INC(p[i][k|l] , dp[u][i][k] * dp[v][j][l]);
                                }
                            }
                        }
                for(int i=0;i<n;i++) for(int j=0;j<(1<<n);j++) dp[u][i][j] = p[i][j];
            }
        }
        if(fb==0){
            for(int i=0;i<n;i++) dp[u][i][1<<i] = 1;
        }
    }
	int calc(vector <string> g, vector <string> t){
        n = g.size();
        clr(dp,0);
        for(int i=0;i<n;i++)
            for(int j=0;j<n;j++){
                if(g[i][j]=='Y') ::g[i][j] = 1;
                else ::g[i][j] = 0;
                if(t[i][j]=='Y') ::t[i][j] = 1;
                else ::t[i][j] = 0;
            }
        dfs(0,0);
        int ans = 0;
        for(int i=0;i<n;i++) for(int j=0;j<(1<<n);j++) ans = (ans+dp[0][i][j])%mod;
		return  ans;
	}



};


// Powered by FileEdit
// Powered by TZTester 1.01 [25-Feb-2003]
// Powered by CodeProcessor

```
