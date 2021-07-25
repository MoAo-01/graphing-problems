## (单源最短路)**spfa**
## [POJ1062 昂贵的聘礼]
- #### [题目连接](http://poj.org/problem?id=1062)
- #### [博客连接](https://www.cnblogs.com/JasonCow/p/14959809.html)
## 题目大意：
- 搜索到的最短路径中，阶级差不超过`m`
- 含有点权和边权

## **Sample Input**
```
1 4
10000 3 2
2 8000
3 5000
1000 2 1
4 200
3000 2 1
4 200
50 2 0
```
## **Sample Output**
```
5250
```

## 条件抽象

1. 相当于给出一个有向图G
2. 同时有点权和边权，额外的每个点有一个阶级属性
3. 询问等效于：可以从图上任意一点出发，到一号结点的最短路径

## 处理方法[拆点]

1. 点权下放成边权

- 通常来说我们处理的问题边权居多，所以考虑拆点
- 然后又因为出发点不唯一，而最短路问题，比较快的方法都是基于单源最短路的
- 所以，我们考虑建立一个虚拟结点n+1号结点
- 把每个点的权值下放为，edge(n+1->i)的权值

2. 阶级差的处理

- 由于出口固定，必须最后：获得1号物品
- 所以，根据一号结点的阶级level[1]可以确定，所选路径上各个结点的阶级所在的区间
- 必然为:
- [level[1]-m,level[1]]
- [level[1]-m+1,level[1]+1]
- [level[1]-m+2,level[1]+2]
- ...
- [level[1],level[1]+m]
- 其中之一

3. 直接使用单源最短路算法处理

- 转化为普通边权图后可以使用
- spfa/dijkska



## 解题思路：
- 单源最短路(`spfa`)
- 枚举`m`种区间，并加入限制条件

## `加限制的spfa`
```cpp
#define ifIsUnderTheRestrictionOfLevel if(minLevel<=level[v]&&level[v]<=maxLevel)

void spfa(int n,int minLevel,int maxLevel){
    queue<int>q;
    fill(dis,dis+n+1,INF),fill(vis,vis+n+1,0);
    q.push(0),vis[0]=1,dis[0]=0;
    while(!q.empty()) {
        int u=q.front();q.pop(),vis[u]=0;
        for(int i=0;i<G[u].size();i++) {
            edge&e=G[u][i];
            int&v=e.v,w=e.w;
            ifIsUnderTheRestrictionOfLevel          //限制条件
            if (dis[v]>dis[u]+w) {
                dis[v]=dis[u]+w;
                if(!vis[v])q.push(v),vis[v]=1;
            }
        }
    }
}
```

## (传递闭包)Floyd+(二分图匹配)匈牙利算法
## [POJ1087 A Plug for UNIX]
- #### [题目连接](http://poj.org/problem?id=1087)
- #### [博客连接](https://www.cnblogs.com/JasonCow/p/14961148.html)

## 题目大意：
- 有电器和配套的插座，以及每种无限个的装换插头
- 问：最少多少电器用不上电？

## **Sample Input**
```
4 
A 
B 
C 
D 
5 
laptop B 
phone C 
pager B 
clock B 
comb X 
3 
B X 
X A 
X D
```
## **Sample Output**
```
1
```

## 题意解读

- 插座和电器都是唯一的
- 转换插头有无限个

## 解题思路

- 首先，先预处理出电器对点的联通情况
- 可以直接使用O(N^3)的floyd
- 至此，原图转化为一个单纯的二分图
- 所有点的点集：可以简单的分为F，G两个部分
- E=F+G, edge(F)=G
- 然后转化成一个模式化的问题：二分图匹配
- 直接上匈牙利算法

## 匈牙利算法

- 对点集F进行遍历
- 对每个当前点进行dfs增广
- 更新当前答案
- 结束遍历，返回答案


## `dfs增广return bool`
```cpp
bool dfs(int u) {
    for(int i=1; i<=m; i++)
        if(!used[i]&&g[u][i]) {
            used[i]=1;
            if(match[i]==-1||dfs(match[i]))
                match[i]=u,
                return true;
        }
    return false;
}
```
## `匈牙利算法`
```cpp
int hungary() {
    int res=0;
    memset(match,-1,sizeof(match));
    for(int i=m+n+101; i<=m+n+101+n; res+=dfs(i++))
        memset(used,0,sizeof(used));
    return n-res;                       //此题问的是最少不能匹配，所以返回的是补集。
}
```

## (拓扑排序)**bfs**/(有向图找环)**floyd**
## [POJ1094 Sorting It All Out]
- #### [题目连接](http://poj.org/problem?id=1094)
- #### [博客连接](https://www.cnblogs.com/JasonCow/p/14961232.html)


## 题目大意
- 给出`m`个关于`n`个元素的偏序关系
- 问：如何排序？若不能从合适不能？或者不能确定偏序关系。
## **Sample Input**
```
4 6
A<B
A<C
B<C
C<D
B<D
A<B
3 2
A<B
B<A
26 1
A<Z
0 0
```
## **Sample Output**
```
Sorted sequence determined after 4 relations: ABCD.
Inconsistency found after 2 relations.
Sorted sequence cannot be determined.
```

## 解题思路
- **思路一：拓扑排序**
- **思路二：Floyd+判断**


## `拓扑排序`
```cpp
int map[27][27],indegree[27],q[27];
int TopoSort(int n){
    int t=0,temp[27],p,m,flag=1;  //flag=1:有序 flag=-1:不确定
    for(int i=1;i<=n;i++)temp[i]=indegree[i];
    for(int i=1;i<=n;i++){
        m=0;for(int j=1;j<=n;j++)if(temp[j]==0)m++,p=j;//查找入度为零的顶点个数
        if(m==0)return 0; //有环
        if(m>1) flag=-1;  //无序
        q[++t]=p,temp[p]=-1;//入度为零的点入队
        for(int j=1;j<=n;j++)if(map[p][j]==1)temp[j]--;
    }
    return flag;
}
```
## `floyd+判断`
```cpp
const int N=30;
int n, m, d[N][N], e[N][N];
int floyd() {
    memcpy(e, d, sizeof(e));
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++) {
                e[i][j] |= e[i][k] & e[k][j];
                if (e[i][j] == e[j][i] && e[i][j] && i != j) return -1; //成环了
            }
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (e[i][j] == e[j][i] && !e[i][j] && i != j) return 0;     //不确定
    return 1;//已经有序
}
```
## 二分+2-SAT
## [POJ2723 Get Luffy Out]

- #### [题目连接](http://poj.org/problem?id=2723)

## 题目大意
- 有2N把钥匙，分成了N份，每份两把钥匙，使用了其中一把，另一把就会自动消失，
- 现在有M层楼，每层楼有扇门，每扇门有两把锁，打开其中任意一把都可以打开这个门。
- 现在问你用这些钥匙最多能开几扇门，注意，只有打开了前一层楼的门才能有机会开下一层楼的门？

## Sample Input
```
3 6
0 3
1 2
4 5
0 1
0 2
4 1
4 2
3 5
2 2
0 0
```
## Sample Output
```
4
```

## 代码
```cpp
#include<cstdio>
#include<vector>
#include<cstring>
using namespace std;
const int N=1e5;
struct Edge {int v,next;}E[N];
int head[N],tot;
void add(int u,int v) {
    E[tot].v=v,E[tot].next=head[u],head[u]=tot++;
}

int dfn[N],low[N],scc[N],idx;
int stk[N],vis[N],top;
int keys[2][N];
int door[2][N];

int n,m;

void init() {
    tot=idx=top=0;
    memset(E,0,sizeof(E));
    memset(head,-1,sizeof(head));    
    memset(dfn,0,sizeof(dfn));
    memset(low,0,sizeof(low));
    memset(scc,0,sizeof(scc));
    memset(vis,0,sizeof(vis));
    memset(stk,0,sizeof(stk));
}

void build(int mid){
	init();
	for(int i=1; i<=n; i++) { 			//建两片互逆的钥匙
        add(keys[0][i]<<1,keys[1][i]<<1|1);
        add(keys[1][i]<<1,keys[0][i]<<1|1);
    }
    for(int i=1; i<=mid; i++) {				//只连mid条边        
        add(door[0][i]<<1|1,door[1][i]<<1);
        add(door[1][i]<<1|1,door[0][i]<<1);        
    }
}

void tarjan(int u) {
	int v;
    dfn[u]=low[u]=++idx;
    stk[top++]=u,vis[u]=true;
    for(int i=head[u]; ~i; i=E[i].next){
    	v=E[i].v;
        if(!dfn[v])tarjan(v),low[u]=min(low[u],low[v]);
        else if(vis[v])      low[u]=min(low[u],dfn[v]);
	}
    if(low[u]==dfn[u]) {    	
        do {
            v=stk[--top];
            vis[v]=false;
            scc[v]=u;
        } while(u!=v);
    }
}

bool check() {
    for(int i=0; i<(n<<2); i++)if(!dfn[i])tarjan(i);	//注意下标从零开始
    for(int i=0; i<(n<<1); i++)if(scc[i<<1]==scc[i<<1|1])return false;
    return true;
}

int main() {
    while(scanf("%d%d",&n,&m)&&(n||m)) {
        for(int i=1; i<=n; i++)scanf("%d%d",&keys[0][i],&keys[1][i]);
        for(int i=1; i<=m; i++)scanf("%d%d",&door[0][i],&door[1][i]);        
        int l=0,r=m;
        while(l<=r) {
            int mid=(l+r)>>1;
            build(mid);
            if(check())l=mid+1;
            else       r=mid-1;
        }
        printf("%d\n",r);
    }
    return 0;
}
```
