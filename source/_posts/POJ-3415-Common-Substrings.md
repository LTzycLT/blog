---
title: POJ 3415 Common Substrings
date: 2013-08-11 14:31:26
tags: acm
---
题意：求两个串中相同的长度大于K的子串对数。

两个串链接起来做后缀数组。
假设height数组为： 
 0 2 1 4 3 2 4
 0 0 0 1 0 0 1
0表示是第一个串的后缀，1表示是第二个串的后缀。
如果现在求第7个后缀，能与之前第一个串的后缀得到多少的长度大于k的子串对数。
可以发现[1,2]区间的后缀和第7个后缀最长匹配都为1，[3,5]区间的后缀最长匹配为2,[6,6]区间的最长匹配为3, 因此用单调栈找到前一个比他小的height，那么这段区间的每个后缀都能得到 heigth-k+1个与第7个后缀长度大于等于k的子串。
因此需要从前往后，从后往前维护两次单调栈，分别得到每个第二个串的后缀能与之前或之后第一个串的后缀匹配的个数，答案求和即可.
```c++
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <string>
#include <iostream>
#include <vector>
#include <map>
#include <cmath>
#include <algorithm>
#define ll long long
#define clr(a,b) memset(a,b,sizeof(a))
using namespace std;

const int inf = 100000000;
const int N = 1000010;
int ua[N], ub[N], us[N];
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
int rank[N],height[N]; //height[i]为排第i-1和第i的后缀的公共前缀长度
void calheight(int *r,int *sa,int n)
{
    int i,j,k=0;
    for(i=1; i<=n; i++) rank[sa[i]]=i;
    for(i=0; i<n; height[rank[i++]]=k)
        for(k?k--:0,j=sa[rank[i]-1]; r[i+k]==r[j+k]; k++);
}

int n,m,k;
int a[N],sa[N],st[N],top;
ll sum[N];
char s[N];


ll gao(int height[]){
    for(int i=1;i<=n+m+1;i++)
        if(sa[i] < n)
            sum[i] = sum[i-1]+1;
        else
            sum[i] = sum[i-1];
    ll ans = 0;
    ll val = 0;
    top = 0;
    for(int i=1;i<=n+m+1;i++){
        while(top>0 && height[i] < height[st[top]]){
            int x = st[top] , y = st[top-1];
            if(height[x]-k>=0){
                if(y>0)
                    val -= (sum[x-1]-sum[y-1])*(height[x]-k+1);
                else
                    val -= sum[x-1]*(height[x]-k+1);
            }
            top--;
        }
        int x = i,y = st[top];
        if(height[x]-k>=0)
            if(y>0)
                val += (sum[x-1]-sum[y-1])*(height[x]-k+1);
            else
                val += sum[x-1]*(height[x]-k+1);
        st[++top] = i;
        if(sa[i]>n)
            ans += val;
    }
    return ans;
}
int main(){
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    while(1){
        scanf("%d",&k);
        if(k==0) break;
        scanf("%s",s);
        n = strlen(s);
        for(int i=0;i<n;i++) a[i] = s[i];
        a[n] = 1;
        scanf("%s",s);
        m = strlen(s);
        for(    int i=0;i<m;i++) a[i+n+1] = s[i];
        a[n+m+1] = 0;
        da(a,sa,n+m+2,256);
        calheight(a,sa,n+m+1);
        ll ans = 0;
        ans += gao(height);

        for(int i=1;i<=n+m+1;i++) st[i] = sa[n+m+1-i+1];
        for(int i=1;i<=n+m+1;i++) sa[i] = st[i];

        for(int i=2;i<=n+m+1;i++) st[i] = height[n+m+1-i+2];
        for(int i=2;i<=n+m+1;i++) height[i] = st[i];

        ans += gao(height);
        cout<<ans<<endl;
    }

    return 0;
}
```

