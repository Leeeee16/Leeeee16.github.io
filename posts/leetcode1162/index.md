# LeetCode1162.地图分析-随笔

- 题目:[1162.地图分析](https://leetcode-cn.com/problems/as-far-from-land-as-possible/)  
你现在手里有一份大小为 N x N 的『地图』（网格） grid，上面的每个『区域』（单元格）都用 0 和 1 标记好了。其中 0 代表海洋，1 代表陆地，你知道距离陆地区域最远的海洋区域是是哪一个吗？请返回该海洋区域到离它最近的陆地区域的距离。  
我们这里说的距离是『曼哈顿距离』（ Manhattan Distance）：(x0, y0) 和 (x1, y1) 这两个区域之间的距离是 |x0 - x1| + |y0 - y1| 。  
如果我们的地图上只有陆地或者海洋，请返回 -1。 
{{< admonition type=example title="示例：" details=true >}}
- 示例 1：  
[[1,0,1],  
 [0,0,0],  
 [1,0,1]]  
输出：2（海洋区域（1,1）和所有陆地区域之间的距离最大，最大距离为2）  
- 示例 2：  
[[1,0,0],  
[0,0,0],  
[0,0,0]]  
输出：4（海洋区域 (2, 2) 和所有陆地区域之间的距离都达到最大，最大距离为 4。）
{{< /admonition >}}
{{< admonition type=tip title="提示:" details=true >}}
  1. 1 <= grid.length == grid[0].length <= 100  
  2. grid[i][j]不是0就是1
{{< /admonition >}}

- - -
思路：采用多源BFS求最短路。  
- note：  
本题可以采用c++中的deque作为队列。
`deque<pair<int, int>> deq;`  
deque（双端队列），可以方便地在队列首尾进行操作。  
pair是将2个数据组合成一组数据，pair类型定义在头文件#include<utility\>中。  
两个值可以分别用pair的两个公有函数**first**和**second**访问。
- - -
```cpp
class Solution {
public:
    int maxDistance(vector<vector<int>>& grid) {
        vector<vector<int>> directions = { {-1, 0}, {1, 0}, {0, 1}, {0, -1} }; //定义一个方向向量
        const int N = grid.size();
        // 使用deque作为队列
        deque<pair<int, int>> deq;
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < N; ++j) {
                if (grid[i][j])    deq.push_back({i, j});// 所有陆地入队
            }
        }
        // 如果全是陆地 或者 海洋，则返回-1
        if (deq.size() == 0 || deq.size() == N * N)    return -1;
        int distance = -1;
        // 对队列的元素进行遍历
        while (deq.size() != 0) {
            distance ++;    //每遍历一次，distance + 1
            // 当前层的元素有多少，在该轮中一次性遍历完当前层
            int size = deq.size();
            while (size --) {
                // BFS遍历的当前元素永远是队列的开头元素
                auto cur = deq.front(); deq.pop_front();
                // 对当前元素的各个方向进行搜索
                for (auto& d : directions) {
                    int x = cur.first + d[0];
                    int y = cur.second + d[1];
                    if (x < 0 || x >= N || y < 0 || y >= N || grid[x][y] != 0) {
                        continue;   //判断是否超出边界或者已经搜索过
                    }
                    grid[x][y] = 2;           // 把grid中搜索过的元素设置为2
                    deq.push_back({x, y});    // 入队
                }
            }
        }
        return distance;
    }
};
```
参考链接：[https://blog.csdn.net/fuxuemingzhu/article/details/105175455](https://blog.csdn.net/fuxuemingzhu/article/details/105175455)
