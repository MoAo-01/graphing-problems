## 剑指 Offer 29. 顺时针打印矩阵

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

示例 1：
- 输入：`matrix = [[1,2,3],[4,5,6],[7,8,9]]`
- 输出：`[1,2,3,6,9,8,7,4,5]`

示例 2：
- 输入：`matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]`
- 输出：`[1,2,3,4,8,12,11,10,9,5,6,7]` 

限制：
- `0 <= matrix.length <= 100`
- `0 <= matrix[i].length <= 100`

## 解题思路

考虑直接使用dfs

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        int n=matrix.size();
        if(!n)return {};
        int m=matrix[0].size();
        auto inMatrix=[&](int x,int y){return 0<=x&&x<n && 0<=y&&y<m;};
        vector<bool>vis(n*m,0);
        vector<int>ans;
        function<void(int,int,int)>dfs=[&](int x,int y,int d){
            if(!inMatrix(x,y)||vis[x*m+y])return;
            if(!vis[x*m+y])
                vis[x*m+y]=1,
                ans.push_back(matrix[x][y]);
            switch(d){
                case 1:dfs(x,y+1,d);break;
                case 2:dfs(x+1,y,d);break;
                case 3:dfs(x,y-1,d);break;
                case 4:dfs(x-1,y,d);break;
            }
            dfs(x,y+1,1);
            dfs(x+1,y,2);
            dfs(x,y-1,3);
            dfs(x-1,y,4);
        };
        dfs(0,0,1);
        return ans;
    }
};
```
