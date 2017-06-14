---
title: ' 两题类似的数位DP'
date: 2013-08-17 21:07:34
tags: acm
---

CF 331C3 - The Great Julya Calendar (60 points)
SGU 390 TICKETS
dfs写法的数位dp最牛的地方是:只要考虑状态怎么变，边界情况搞搞好然后就能AC。
这两题都是一段区间会对下一段区间有影响的数位DP，这个影响就用pair来记录，其他无脑写。

CF 331C3
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

pair<ll,int> dp[20][10][10];
bool vis[20][10][10];
ll l,r,k;
int dig[20];

pair<ll,int> dfs(int pos,int mx,int rem,int up){
    if(pos==-1){
        if(mx==0) return make_pair(0,0);
        if(rem>0) return make_pair(0,rem-1);
        else return make_pair(1,mx-1);
    }
    if(!up && vis[pos][mx][rem]) return dp[pos][mx][rem];

    int t;
    if(up) t = dig[pos];
    else t = 9;

    pair<ll,int> ans = make_pair(0,rem);

    for(int i=t;i>=0;i--){
        pair<ll,int> tmp = dfs(pos-1,max(mx,i),ans.second, up&&t==i);
        ans.first += tmp.first;
        ans.second  = tmp.second;
    }

    if(!up){
        vis[pos][mx][rem] = 1;
        dp[pos][mx][rem] = ans;
    }
    return ans;
}
ll  calc(){
    int p = 0;
    while(l){
        dig[p++] = l%10;
        l/=10;
    }
    return dfs(p-1,0,0,1).first;
}
int main(){
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    cin>>l;
    cout<<calc()<<endl;
    return 0;
}
```
SGU 390
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

pair<ll,int> dp[20][200][1002];
bool vis[20][200][1002];
ll l,r,k;
int digl[20],digr[20],p;

pair<ll,int> dfs(int pos,int sum,int rem,int down,int up){
    if(pos==-1){
        if(sum + rem >= k) return make_pair(1,0);
        else return make_pair(0,sum+rem);
    }
    if(!down && !up && vis[pos][sum][rem]) return dp[pos][sum][rem];

    int s,t;
    if(down) s = digl[pos];
    else s = 0;

    if(up) t = digr[pos];
    else t = 9;

    pair<ll,int> ans = make_pair(0,rem);

    for(int i=s;i<=t;i++){
        pair<ll,int> tmp = dfs(pos-1,sum+i,ans.second, down&&s==i, up&&t==i);
        ans.first += tmp.first;
        ans.second  = tmp.second;
    }

    if(!down && !up){
        vis[pos][sum][rem] = 1;
        dp[pos][sum][rem] = ans;
    }
    return ans;
}
ll  calc(){
    p = 0;
    while(l){
        digl[p++] = l%10;
        l/=10;
    }
    p = 0;
    while(r){
        digr[p++] = r %10;
        r/=10;
    }
    return dfs(p-1,0,0,1,1).first;
}
int main(){
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    cin>>l>>r>>k;
    cout<<calc()<<endl;
    return 0;
}
```



