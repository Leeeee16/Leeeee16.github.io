# 求x平方根


## 题目：计算并返回x的平方根
  
### 法1：二分查找
  
每次比较mid\*mid与x的大小，注意mid\*mid可能会越界，所以用long long来表示，或者可以用x\\mid和mid比较。
```cpp
class Solution {
public:
    int mySqrt(int x) {
        if(x==1 || x ==0) return x;
        int low = 1, high = x;
        while(low <= high){
            int mid = low + (high - low) / 2;
            if((long long)mid * mid == x) return mid;
            if((long long)mid * mid > x) high = mid - 1;
            else low = mid + 1;
        }
        return low-1;
    }
};
```
  
### 法2：牛顿迭代法
  
牛顿迭代法可以快速求出函数零点。  
比如题目要求 $ \\sqrt{x} $，我们可以看成求 $ \\ y = f(x)=x^2-C$ 的零点。  
牛顿迭代我的理解就是，选取初始点$ \\ x_0 $,那么$ \\ x_1 $就是 f(x) 过($ \\ x_0 $，$ f(x_0) $)的切线与x轴的交点，如图所示。不断迭代下去，就能逼近f(x)的零点。  
牛顿迭代法的地推公式：$$ \\ x_{n+1}=x_n-\frac{f\left(x_n\right)}{f^'\left(x_n\right)}$$
  
本题中带入化简后得到的递推是为：$$ \\ x_{i+1}=\frac12\left(x_i+\frac c{x_i}\right)$$
  
![](/images/niudundiedaifa.PNG)
  
```cpp
class Solution {
public:
    int mySqrt(int x) {
        if (x == 0) {
            return 0;
        }

        double C = x, x0 = x;
        while (true) {
            double xi = 0.5 * (x0 + C / x0);
            if (fabs(x0 - xi) < 1e-7) {
                break;
            }
            x0 = xi;
        }
        return int(x0);
    }
};
```
  
PS：主题的数学公式是基于KATEX，我找到一个网站可以方便地得到公式对应KATEX的Markdown代码。公式可以通过手写识别，很方便。
[Demo MathType](http://www.wiris.com/editor/demo/zh/developers#output-formats)
