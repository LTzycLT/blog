---
title: Codeforces 316G3Good Substrings (30 points)
date: 2013-07-30 23:00:49
tags: acm
---

题意：给一个长为50000的字符串t，还有10个约束(p,l,r), 当某一字符串s在p中出现的次数大于等于l,小于等于r时满足约束，问t有多少个不同的子串满足所有约束。

先考虑t的子串的性质：
t 的长度为len
假设以 x 为起点的子串，终点在[l,len]范围内时满足所有约束条件中的小于部分。
那么对于以x+1为起点的子串，终点在[l1,len]范围内时满足所有约束条件中的小于部分
则有  l1 > l ，所以用 two point 先行扫描出l[0]~l[len-1] 。其中扫描过程中如何判断某一子串在约束串中出现的次数，用二分+后缀数组。
同样处理出 r[0]~r[len-1] 满足范围内时满足所有约束条件中的大于部分 
那么对于x为起点的串，终点在l[x]~r[x]之间时满足所有约束条件。
还没结束，还需要判重，后缀数组再次登程。
我们需要找到以x为起点的串，在(0,x-1)为起点的串中出现过的次数，转换一下可以变成求x为起点的串与之前串最多能匹配到多长，记录为repl[x]，从前从后扫各一遍sa数组，用树状数组和后缀数组的lcp来搞。
答案就是对 r[i]-max(repl[i],l[i])+1 (0<=i<len) 求和。

```c++
#include <stdio.h>
#include <iostream>
#include <algorithm>
#include <stdlib.h>
#include <string.h>
#include <set>
#include <queue>
#include <map>
#include <vector>
#define ll long long
#define clr(x,y) memset(x,y,sizeof(x))
#define forn(i,n) for(int i=0;i<n;i++)
using namespace std;

const int X = 11;
const int N = 100010;
int ua[N], ub[N], us[N];
char s[N];
int a[N];
int n,len[X],l[X],r[X];
int ansl[N],ansr[N];

int cmp(int *r,int a,int b,int l)
{
    return r[a]==r[b]&&r[a+l]==r[b+l];
}
void da(int *r,int *sa,int n,int m)  //da(r, sa, n + 1, 256);(r[n] = 0)
{
    int i,j,p,*x=ua,*y=ub,*t;
//r[]存放原字符串,且从char变为int
    for(i=0; i<m; i++) us[i]=0; //sa[i]表示排名为i的后缀起始下标(i>=1,sa[i]>=0)
    for(i=0; i<n; i++) us[x[i]=r[i]]++;
    for(i=1; i<m; i++) us[i]+=us[i-1];
    for(i=n-1; i>=0; i--) sa[--us[x[i]]]=i;
    for(j=1,p=1; p<n; j*=2,m=p)
    {
        for(p=0,i=n-j; i<n; i++) y[p++]=i;
        for(i=0; i<n; i++) if(sa[i]>=j) y[p++]=sa[i]-j;
        for(i=0; i<m; i++) us[i]=0;
        for(i=0; i<n; i++) us[x[i]]++;
        for(i=1; i<m; i++) us[i]+=us[i-1];
        for(i=n-1; i>=0; i--) sa[--us[x[y[i]]]]=y[i];
        for(t=x,x=y,y=t,p=1,x[sa[0]]=0,i=1; i<n; i++)
            x[sa[i]]=cmp(y,sa[i-1],sa[i],j)?p-1:p++;
    }
}

int rank[X][N],height[X][N],sa[X][N];

void calheight(int *r,int *sa,int n,int height[],int rank[])
{
    int i,j,k=0;
    for(i=1; i<=n; i++) rank[sa[i]]=i;
    for(i=0; i<n; height[rank[i++]]=k)
        for(k?k--:0,j=sa[rank[i]-1]; r[i+k]==r[j+k]; k++);
}
int mm[N];
int best[X][20][N];
void initRMQ(int n,int best[20][N],int *RMQ)
{
    int i,j,a,b;
    for(mm[0]=-1,i=1; i<=n; i++)
        mm[i]=((i&(i-1))==0)?mm[i-1]+1:mm[i-1];
    for(i=1; i<=n; i++) best[0][i]=i;
    for(i=1; i<=mm[n]; i++)
        for(j=1; j<=n+1-(1<<i); j++)
        {
            a=best[i-1][j];
            b=best[i-1][j+(1<<(i-1))];
            if(RMQ[a]<RMQ[b]) best[i][j]=a;
            else best[i][j]=b;
        }
}
int askRMQ(int a,int b,int best[20][N],int *RMQ)
{
    int t;
    t=mm[b-a+1];
    b-=(1<<t)-1;
    a=best[t][a];
    b=best[t][b];
    return RMQ[a]<RMQ[b]?a:b;
}
int lcp(int a,int b ,int x,int rank[])  //后缀r[a]和r[b]的公共前缀长度
{
    int t;
    a=rank[a];
    b=rank[b];
    if(a>b)
    {
        t=a;
        a=b;
        b=t;
    }
    return(height[x][askRMQ(a+1,b,best[x],height[x])]);
}


int binary_search(int type , int pos,int x ,int strlen)
{
    int l,r;
    if(type == 0) l=1,r=rank[x][pos];
    else l=rank[x][pos],r=len[x];
    while(r-l>1)
    {
        int mid = (l+r)/2;
        if(lcp(sa[x][mid],pos,x,rank[x])>=strlen)
        {
            if(type == 0) r = mid;
            else l = mid;
        }
        else
        {
            if(type == 0) l = mid+1;
            else r = mid-1;
        }
    }
    if(type == 0){
        if(lcp(sa[x][l],pos,x,rank[x])>=strlen) return l;
        else return r;
    }
    else{
         if(lcp(sa[x][r],pos,x,rank[x])>=strlen) return r;
        else return l;
    }

}
int getNum(int s,int t,int x)
{
    int strlen = t - s + 1;
    int up0 , down0 ,up1,down1;
    up0 = binary_search(0,s,0,strlen);
    down0 = binary_search(1,s,0,strlen);

    up1 = binary_search(0,s,x,strlen);
    down1 = binary_search(1,s,x,strlen);

    return down1-up1+1 - (down0-up0+1);
}



int repl[N];
int idx[N];
const int inf = 1e8;
int lowbit(int x){
    return x&(-x);
}
void insert(int l,int v,int type){
    for(int i=l;i<=len[0];i+=lowbit(i))
        if(type==0) idx[i] = max(idx[i],v);
        else idx[i] = min(idx[i],v);
}
int get(int l,int type){
    int ans ;
    if(type==0) ans = 0 ;
    else ans = inf;
    for(int i=l;i>=1;i-=lowbit(i)){
        if(type==0) ans = max(idx[i],ans);
        else ans = min(idx[i],ans);
    }
    return ans;
}


int main()
{
//    freopen("in","r",stdin);
    scanf("%s",s);
    len[0] = strlen(s);
    for(int i=0; i<len[0]; i++)
        a[i] = s[i] ;
    da(a,sa[0],len[0]+1,300);
    calheight(a,sa[0],len[0],height[0],rank[0]);
    initRMQ(len[0],best[0],height[0]);

    scanf("%d",&n);
    for(int i=1; i<=n; i++)
    {
        scanf("%s",s);
        int tlen = strlen(s);
        a[len[0]] = '$';
        len[i] = len[0] + tlen+1;
        for(int j=len[0]+1; j<len[i]; j++)
        {
            a[j] = s[j-len[0]-1];
        }
        a[len[i]] = 0;
        da(a,sa[i],len[i]+1,300);
        calheight(a,sa[i],len[i],height[i],rank[i]);
        initRMQ(len[i],best[i],height[i]);

        scanf("%d%d",&l[i],&r[i]);
    }
    int p = 0 ;
    for(int i=0; i<len[0]; i++)
    {
        p = max(i,p);
        while(p<len[0])
        {
            bool flag = true;
            for(int j=1; j<=n; j++)
            {
                if(getNum(i,p,j)>r[j])
                    flag = false;
            }
            if(!flag) p++;
            else break;
        }
        ansl[i] = p;
    }

    p=0;
    for(int i=0; i<len[0]; i++)
    {
        p = max(i,p);
        while(p<len[0])
        {
            bool flag = true;
            for(int j=1; j<=n; j++)
            {
                if(getNum(i,p,j) < l[j])
                    flag =false;
            }
            if(flag) p++;
            else break;
        }
        ansr[i] = p-1;
    }


    for(int i=0;i<len[0];i++) repl[i] = 0;

    for(int i=1;i<=len[0];i++) idx[i] = 0;
    for(int i=1;i<=len[0];i++){
        int t = sa[0][i];
        int s = get(t+1,0);
        if(s!=0)
            repl[t] = lcp(t,sa[0][s],0,rank[0]);
        insert(t+1,i,0);
    }

    for(int i=1;i<=len[0];i++) idx[i] = inf;
    for(int i=len[0];i>=1;i--){
        int t = sa[0][i];
        int s = get(t+1,1);
        if(s!=inf)
            repl[t] = max(repl[t],lcp(t,sa[0][s],0,rank[0]));
        insert(t+1,i,1);
    }

    ll ans = 0;
    for(int i=0; i<len[0]; i++)
    {
        ansl[i] = max(ansl[i],repl[i]+i);
        if(ansr[i]>=ansl[i])
            ans += ansr[i] - ansl[i] +1;
    }
    cout<<ans<<'\n';
    return 0;
}
```

