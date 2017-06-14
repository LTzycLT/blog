---
title: hdu 4456 crowd
date: 2013-09-03 21:29:00
tags: acm
---

去年区域赛留下来的遗憾题之一。

此题要考的是坐标转换后的二维树状数组，难点在于内存开不下，需要20000**20000，现场赛胆大的直接开了这么大就过了，向我们这种胆小的就直接被吓傻了。

hdu上内存32768K。

由树状数组的性质可知每次最多只会更新log(n)次，因此二维树状数组总共会更新m * log(n)*log(n)个地方，所以想办法存这些值就可以了，map会超时，搞个靠谱点的hash就可以了。

另一种解法估计就是出题人想考的，第一维照样用树状数组，第二维再套个平衡树来维护在这个范围内的区间和。




第二种解法代码：

```c++
#include <stdio.h>
#include <math.h>
#include <algorithm>
#include <iostream>
#include <vector>
#include <queue>
#include <bitset>
#include <string.h>
#include <map>
#include <set>
#define ll long long
#define clr(a,b) memset(a,b,sizeof(a))
#define sqr(x) ((x)*(x))
using namespace std;
const double eps=1e-8;

int n,m;
const int N = 2000000;

int key[N],sz[N],lc[N],rc[N],w[N],sum[N];
int num;


void newnode(int i)
{
    key[i] = sz[i] = lc[i] = rc[i] = w[i] = sum[i] = 0;
}
class sbtree
{
public:
    int root;
    void clear()
    {
        newnode(++num);
        root = num;
        sz[root] = 1;
    }
    void Insert(int &root,int val,int ww)
    {
        if (root == 0)
        {
            root = ++num;
            lc[root] = rc[root] = 0;
            sz[root] = 1;
            key[root] = val;
            w[root] = ww;
            sum[root] = ww;
            return ;
        }
        sum[root] += ww;
        sz[root] ++;
        if (val < key[root])
        {
            Insert(lc[root] , val , ww);
        }
        else
        {
            Insert(rc[root] , val , ww);
        }
        maintain(root , !(val < key[root]));
    }

    int query(int val){
        int tmp = root;
        int ans = 0;
        while(tmp){
            if(key[tmp] <= val){
                ans += sum[lc[tmp]] + w[tmp];
                tmp = rc[tmp];
            }
            else{
                tmp = lc[tmp];
            }
        }
        return ans;
    }

    void LeftRotate(int &root)
    {
        int temp = rc[root];
        rc[root] = lc[temp];
        lc[temp] = root;
        sz[temp] = sz[root];
        sum[temp] = sum[root];
        sz[root] = 1 + sz[lc[root]] + sz[rc[root]];
        sum[root] = sum[lc[root]] + sum[rc[root]] + w[root];
        root = temp;
    }
    void RightRotate(int &root)
    {
        int temp = lc[root];
        lc[root] = rc[temp];
        rc[temp] = root;
        sz[temp] = sz[root];
        sum[temp] = sum[root];
        sz[root] = 1 + sz[lc[root]] + sz[rc[root]];
        sum[root] = sum[lc[root]] + sum[rc[root]] + w[root];
        root = temp;
    }
    void maintain(int &root , bool flag)
    {
        if (root == 0) return ;
        if ( !flag )   // 调整左子树
        {
            if ( sz[lc[lc[root]]] > sz[rc[root]] )
            {
                RightRotate( root );
            }
            else if ( sz[rc[lc[root]]] > sz[rc[root]] )
            {
                LeftRotate( lc[root] );
                RightRotate( root );
            }
            else
            {
                return ;
            }
        }
        else     // 调整右子树
        {
            if ( sz[rc[rc[root]]] > sz[lc[root]] )
            {
                LeftRotate( root );
            }
            else if ( sz[lc[rc[root]]] > sz[lc[root]] )
            {
                RightRotate( rc[root] );
                LeftRotate( root );
            }
            else
            {
                return ;
            }
        }
        maintain(lc[root] , false);
        maintain(rc[root] , true);
        maintain(root , false);
        maintain(root , true);
    }

} sbt[30000];



int lb(int x)
{
    return x&(-x);
}
void ch(int &x,int &y)
{
    int nx = n+x-y;
    int ny = x+y-1;
    x = nx;
    y = ny;
}
void initval()
{
    num = 0;
    for(int i=1; i<=2*n; i++)
        sbt[i].clear();
}


void add(int x,int y,int z)
{
    for(int i=x; i<=2*n; i+=lb(i))
        sbt[i].Insert(sbt[i].root,y,z);
    }
int get(int x,int y1,int y2)
{
    int ans = 0;
    for(int i=x; i>0; i-=lb(i))
        ans += sbt[i].query(y2) - sbt[i].query(y1);
    return ans;
}
int main()
{
//    freopen("/home/zyc/Documents/Code/cpp/in","r",stdin);
    while(1)
    {
        scanf("%d",&n);
        if(n==0) return 0;
        scanf("%d",&m);
        initval();
        while(m--)
        {
            int type,x,y,z;
            scanf("%d%d%d%d",&type,&x,&y,&z);
            if(type==1)
            {
                ch(x,y);
                add(x,y,z);
            }
            else
            {
                int a,b,c,d;
                a = x - z;
                b = y;
                c = x + z;
                d = y;
                ch(a,b);
                ch(c,d);

                a = max(1,a);
                c = min(2*n,c);

                a--;
                b--;
                printf("%d\n",get(c,b,d) - get(a,b,d) );
            }
        }
    }
    return 0;
}
```


