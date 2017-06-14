---
title: CF 338E Optimize!
date: 2013-08-18 20:33:24
tags: acm
---
题意：给一个串a,长度为n，一个串b长度为len, 问a有多少个长度为len的子串满足：和b任意匹配后，每一对值的和都大于等于h。

把b从大到小排序，对a里面的元素求出和b[i]相加大于等于h的最大的i , 这样b[1]~b[i]都满足和当前a里的元素相加大于等于h。
然后分别求出a中每一段长度为len的子串里：有多少要和b[1],b[2],......,b[len]之前匹配，记为c[i]，如果存在一个c[i] > i那显然这个子串无法满足了。
那么我们从左往右扫一遍，用线段树来维护，一开始每一个点先减去i,然后扫描中把c[i]加上去，如果线段树最大值比0大，那就表示这个串不满足。
```c++
//#pragma comment(linker, "/STACK:65536000")
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<queue>
#include<algorithm>
#include<vector>
#include<iostream>
#define max(x,y)(x>y?x:y)
#define min(x,y)(x<y?x:y)
#define ll long long
#define clr(x,y) memset(x,y,sizeof(x))
using namespace std;

const int inf = 1e9;
const int N = 200000;
int a[N],b[N];
int n,m,h;
int ma[4*N],add[4*N];

bool cmp(int a,int b){
    return a>b;
}

void pushdown(int x){
    add[x+x] += add[x];
    add[x+x+1] += add[x];
    add[x] = 0;
}
void pushup(int x){
    ma[x] = max(ma[x+x]+add[x+x],ma[x+x+1]+add[x+x+1]);
}

void insert(int x,int l,int r,int tl,int tr,int v){
    if(l>tr || r<tl) return ;
    if(l<=tl && r>=tr){
        add[x] += v;
        return ;
    }
    pushdown(x);
    int mid = (tr+tl)/2;

    insert(x*2,l,r,tl,mid,v);
    insert(x*2+1,l,r,mid+1,tr,v);

    pushup(x);
}
int main(){
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    scanf("%d%d%d",&n,&m,&h);
    for(int i=1;i<=m;i++) scanf("%d",b+i);
    sort(b+1,b+m+1,cmp);
    for(int i=1;i<=n;i++){
        int tmp;
        scanf("%d",&tmp);
        int l = 1, r = m+1;
        while(r!=l){
            int mid = (l+r)>>1;
            if(b[mid] + tmp >=h) l = mid+1;
            else r = mid;
        }
        l--;
        a[i] = l;
    }
    for(int i=0;i<=m;i++) insert(1,i,i,0,m,-i);
    for(int i=1;i<=m;i++) insert(1,a[i],m,0,m,1);
    int ans = 0;
    for(int i=m;i<=n;i++){
        if((ma[1] + add[1])<=0) ans++;

        if(i!=n){
            insert(1,a[i-m+1],m,0,m,-1);
            insert(1,a[i+1],m,0,m,1);
        }
    }
    printf("%d\n",ans);
    return 0;
}

```
