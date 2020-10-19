# 二分查找

二分查找
<!--more-->
  
## 二分算法的基本模板  
```java
boolean check(int x) {/*...*/} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r) {
    while(l < r) {
        int mid = l + r >> 1;
        if(check(mid)) r = mid;
        else l = mid + 1;
    }
    return l;
}

// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r) {
    while (l < r) {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}

//浮点二分
double bsearch_3(double l, double r) {
    double eps = 1E-6; // eps表示精度，取决于题目对精度的要求，一般比要求多两位
    while(r - l > eps) {
        double mid = (l + r) / 2;
        if(check(mid)) r = mid;
        else l = mid;
    }
    return l;
} 
```

## 1. 求一个数的平方根  
```java
public class sqrtx {
    public static double sqrt_x(double x) {
        double eps = 1E-8;
        // 一个正数的平方根，x > 1 ,范围(0,x); x < 1, 范围(0, 1)
        double l = 0, r = Math.max(1, x);
        while (r - l > eps) {
            double mid = l + (r - l) / 2;
            if (mid * mid >= x) r = mid;
            else l = mid;
        }
        return l;
    }

    public static void main(String[] args) {
        double x = 0.01;
        double res = sqrt_x(x);
        System.out.printf("%.6f", res);
    }
}
```
  
## 2. 数的三次方根  
```java
public class ACWing790 {
    static int N = 10000; // 假设数的取值范围
    public static double cubeRoot(double x) {
        double l = -N, r = N; // 注意立方根取值有正负
        double eps = 1E-8;
        while (r - l > eps) {
            double mid = l + (r - l) / 2;
            if (mid * mid * mid >= x) r = mid;
            else l = mid;
        }
        return l;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        double x = Double.parseDouble(br.readLine());
        double cube_root = cubeRoot(x);
        System.out.printf("%.6f%n", cube_root);
    }
}
```
  
## 3. 寻找比目标字母大的最小字母  
[力扣-744. 寻找比目标字母大的最小字母](https://leetcode-cn.com/problems/find-smallest-letter-greater-than-target/)  
给你一个排序后的字符列表 letters ，列表中只包含小写英文字母。另给出一个目标字母 target，请你寻找在这一有序列表里比目标字母大的最小字母。

在比较时，字母是依序循环出现的。举个例子：

如果目标字母 target = 'z' 并且字符列表为 letters = ['a', 'b']，则答案返回 'a'  
> 说明：题目中的意思应该是给的字符列表是排好序的，只有比较的时候循环比较...  
  
感觉题目不太好，就当再写一遍模板..orz  
* 思路：看到有序，考虑一下二分，比模板多一个循环边界的处理。一开始放在循环里处理一直有边界问题..orz，后来用if处理了一头一尾的边界情况，后面就是二分模板，代码如下：  
```java
public class LeetCode744 {
    public static char nextGreatestLetter(char[] letters, char target) {
        int l = 0, r = letters.length - 1;
        if(target >= letters[r] || target < letters[0]) return letters[0];
        while (l < r) {
            int mid = l + r >> 1;
            if (letters[mid] > target) r = mid;
            else l = mid + 1;
        }
        return letters[l];
    }

    public static void main(String[] args) {
        char[] letters = new char[]{'c', 'f', 'j'};
        char[] targets = new char[]{'a', 'c', 'd', 'g', 'j', 'k'};
        for (int i = 0; i < targets.length; i++) {
            System.out.print(nextGreatestLetter(letters, targets[i]) + " "); // c f f j c c 
        }
    }
}

```










- - -
* 模板参考自[ACWing](https://www.acwing.com/blog/content/277/):+1:
