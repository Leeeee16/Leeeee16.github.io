# LeetCode面试题62. 圆圈中最后剩下的数字-随笔

题目：0,1,...,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。  
[LeetCode面试题62. 圆圈中最后剩下的数字](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)
{{< admonition type=example title="示例：" details=true >}}
- 示例 1：  
输入：n=5,m=3   
输出：3  
- 示例 2：  
输入：n=10,m=17   
输出：2
{{< /admonition >}}
{{< admonition type=tip title="限制" details=true >}}
- 1 <= n <= 10^5
- 1 <= m <= 10^6
{{< /admonition >}}  
- - -
思路1：模拟链表  
时间复杂度为O(n*m)，由于题目m和n的限制，用模拟链表会导致超时orz...  
```cpp
class Solution {
public:
    int lastRemaining(int n, int m) {
        list<int> num;
        for(int i = 0; i < n; i++){
            num.push_back(i);
        }
        int idx = 0;
        while(n > 1){
            idx = (idx + m - 1) % n;
            auto it = num.begin();
            advance(it, idx);
            num.erase(it);
            n--;
        }
        return num.front();
    }
};
```
- - -
思路2：约瑟夫环-公式法  
递推公式：f(N,M)=(f(N−1,M)+M)%N  
f(N,M)表示：N个数，每次向后数M个并删除，最终剩下的那个数的序号。  
以*示例一*为例，N = 5，M = 3。第一轮后删去2，下一轮从3开始，也就相当于后面数字的序号都向前移动了3位，那么最终剩下的那个数的序号也向前移动了3位。    
到一般情况，我们每一轮删去一个数，下一个数成为头，也就相当于把数组向前移动M位。如果我们已知 N-1 个人时，最后剩下的数的序号为f(N-1,M),那么N个人时，数组就向后移动M位，那么最终剩下的数的序号就是f(N-1,M)+M , 为了防止数组越界，则为f(N,M)=(f(N-1,M)+M)%N  
- - -
当只有一个数时，最终剩下的数的序号就是0，因此f(1,M) = 0。我们可以从2个数开始，每次增加一个数。而每加一个数，最终剩下的那个数的序号就增加 M 。  
```cpp
class Solution {
public:
    int lastRemaining(int n, int m) {
        int f = 0;
        for (int i = 2; i != n + 1; ++i)
            f = (m + f) % i;
        return f;
    }
};
```
- - -
一个讲解很详细的帖子：[https://blog.csdn.net/u011500062/article/details/72855826](https://blog.csdn.net/u011500062/article/details/72855826)  
LeetCode官方题解：[https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-by-lee/](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-by-lee/)


