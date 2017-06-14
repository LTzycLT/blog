---
title: URAL 1956 Fire Signals
date: 2013-07-24 13:21:56
tags: acm
---
题意：
求一条直线满足，所有平面上点到这条直线的距离之和最小，点数n<1000，输出最小的距离纸盒之和。


经过长时间的YY，得出经过平面上的两个点的直线包含最优解。可以由  dis = |a*x+b*y+c|/sqrt(a*a+b*b) 得到。
因为在直线一侧的点 dis 的正负号是相同的，可以利用这一点。先枚举一点，另外的点按到这点的角度排序，然后扫描枚举另一点，维护两边的点的x之和，y之和，用abs(sum(a*x0+b*y0+c) -abs(a*x1+b*y1+c))/sqrt(a*a+b*b）  跟新答案，x0,y0是直线一边点的x,y的和,x1,y1是另一边的。

```c++
#include<stdio.h>
#include<vector>
#include<map>
#include<algorithm>
#include<math.h>
#include<queue>
#include<iostream>
#define clr(x,y) memset(x,y,sizeof(x))
#define ll long long
using namespace std;

const double pii = acos(-1.0);
const double ans = 1e20;

int n;
ll x[1005],y[1005],mx,my;
struct item{
    double k;
    ll x,y;
    item(){}
    item(double k,ll x,ll y):k(k),x(x),y(y){}
    bool operator<(const item b)const{
        return k<b.k;
    }
}p[1005];
bool dcmp(double a,double b){
    if(fabs(a-b)<1e-8) return 1;
    else return 0;
}

double dis(int s,int t){
    double ds = p[s].k;
    double dt = p[t].k;

    if(s<=t) return dt-ds;
    else return dt-ds+2*pii;
}

double get(double a,double b,ll sx,ll sy){
    if(dcmp(a,0) && dcmp(b,0)) return 1e18;
    ll tx = sx - (mx - sx);
    ll ty = sy - (my - sy);
    return fabs(a*tx+b*ty)/sqrt(a*a+b*b);
}

int main(){
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    scanf("%d",&n);
    for(int i=0;i<n;i++){
        cin>>x[i]>>y[i];
    }
    bool flag = true;
    for(int i=0;i<n;i++){
        for(int j=0;j<n;j++)
            if(x[i]!=x[j]||y[i]!=y[j])
                flag = false;
    }
    if(flag){
        puts("0");
        return 0;
    }

    double ans = 1e18;
    for(int i=0;i<n;i++){
        mx = 0;
        my = 0;
        for(int j=0;j<n;j++){
            ll nx = x[j] - x[i];
            ll ny = y[j] - y[i];
            mx += nx;
            my += ny;
            double nk = atan2(ny,nx);
            p[j] = item(nk,nx,ny);
        }
        sort(p,p+n);
        int r = 0;
        ll sx=0,sy=0;
        while(dis(0,r) <= pii){
            sx += p[r].x;
            sy += p[r].y;
            r = (r+1)%n;
            if(r==0) break;
        }
        for(int l = 0;l<n;l++){
            if(l!=0){
                while(dis(l,r)<=pii){
                    sx += p[r].x;
                    sy += p[r].y;
                    r =(r+1)%n;
                    if(r==l) break;
                }
            }

            ans = min(ans,get(-p[l].y,p[l].x,sx,sy));
            sx -= p[l].x;
            sy -= p[l].y;
        }
    }
    printf("%.10lf\n",ans);
    return 0;
}
```
