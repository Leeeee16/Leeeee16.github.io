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

## 求一个数的平方根  
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
  
## 数的三次方根  
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
  
## 寻找比目标字母大的最小字母  
[力扣-744. 寻找比目标字母大的最小字母](https://leetcode-cn.com/problems/find-smallest-letter-greater-than-target/)  
给你一个排序后的字符列表 letters ，列表中只包含小写英文字母。另给出一个目标字母 target，请你寻找在这一有序列表里比目标字母大的最小字母。

在比较时，字母是依序循环出现的。举个例子：

如果目标字母 target = 'z' 并且字符列表为 letters = ['a', 'b']，则答案返回 'a'  
> 说明：题目中的意思应该是给的字符列表是排好序的，只有比较的时候循环比较...  
  
* 思路：看到有序，考虑一下二分。先用if判断下特殊情况，后面就是二分模板，代码如下：  
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

## LeetCode-4. 寻找两个正序数组的中位数  
[4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)  

给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。  
请你找出并返回这两个正序数组的中位数。  
时间复杂度为 O(log(m + n))  

```java
public class LeetCode4 {
    public static void main(String[] args) {
        int[] nums1 = {1, 3};
        int[] nums2 = {2};
        LeetCode4 lc = new LeetCode4();
        // double res = lc.findMedianSortedArrays_bs(nums1, nums2);
        double res = lc.findMedianSortedArrays_ms(nums1, nums2);
        System.out.println(res);
    }

    /**
     * 1. 二分查找
     * 时间复杂度：O(log( min(m,n) )),对较短长度的数组进行二分
     * 空间复杂度：O(1)
     */
    public double findMedianSortedArrays_bs(int[] nums1, int[] nums2) {
        int len1 = nums1.length;
        int len2 = nums2.length;
        // 对较短数组进行二分
        if (len1 > len2) return findMedianSortedArrays_bs(nums2, nums1);
        int len = len1 + len2;
        // 某一个数组为空，直接计算
        if (len1 == 0) return (nums2[(len2 - 1) / 2] + nums2[len2 / 2]) / 2.0;
        // 初始化二分边界
        int i = 0, j = 0;
        int low = 0, high = len1;
        double res = 0;
        while (low <= high) {
            // 对nums1数组做partition
            i = (low + high + 1) / 2;
            j = (len + 1) / 2 - i;
            double L1 = i == 0 ? Integer.MIN_VALUE : nums1[i - 1];
            double R1 = i == len1 ? Integer.MAX_VALUE : nums1[i];
            double L2 = j == 0 ? Integer.MIN_VALUE : nums2[j - 1];
            double R2 = j == len2 ? Integer.MAX_VALUE : nums2[j];
            // 判断是否满足条件
            if (L1 <= R2 && L2 <= R1) {
                // 偶数
                if ((len1 + len2) % 2 == 0) {
                    res = (Math.max(L1, L2) + Math.min(R1, R2)) / 2.0;
                } else {
                    // 奇数
                    res = Math.max(L1, L2);
                }
                break;
            } else if (L1 > R2) {
                high = i - 1;
            } else {
                low = i + 1;
            }
        }
        return res;
    }

    /**
     *  2. 归并
     *  时间复杂度：O(m + n)，需要遍历两个数组
     *  空间复杂度：O(m + n)，需要辅助数组，保存归并后的两个数组
     */
    public double findMedianSortedArrays_ms(int[] nums1, int[] nums2) {
        int len1 = nums1.length;
        int len2 = nums2.length;
        int i = 0, j = 0, k = 0;
        int[] tmp = new int[len1 + len2];
        while (i < len1 && j < len2) {
            if (nums1[i] < nums2[j]) tmp[k++] = nums1[i++];
            else tmp[k++] = nums2[j++];
        }
        while (i < len1) tmp[k++] = nums1[i++];
        while (j < len2) tmp[k++] = nums2[j++];

        if (k % 2 == 0) return (tmp[(k - 1) / 2] + tmp[k / 2]) / 2.0;
        else return tmp[k / 2];
    }
}
```  

## LeetCode-35. 搜索插入位置  
[LeetCode-35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)  
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。  
如果目标值不存在于数组中，返回它将会被按顺序插入的位置。  
你可以假设数组中无重复元素。  

```java
public int searchInsert(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    if(target > nums[right]) {
        return right + 1;
    }
    while (left < right) {
        int mid = left + right >> 1;
        // 小于target一定不是解
        if (nums[mid] < target) {
            // 下一轮搜索区间 [mid + 1, right]
            left = mid + 1;
        } else {
            // 下一轮搜索区间 [left, mid]
            right = mid;
        }
    }
    return left;
}
```
  
## LeetCode-33. 搜索旋转排序数组  
[LeetCode-33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)  
给你一个升序排列的整数数组 nums ，和一个整数 target。  
假设按照升序排序的数组在预先未知的某个点上进行了旋转。（例如，数组[0,1,2,4,5,6,7]可能变为[4,5,6,7,0,1,2] ）。  
请你在数组中搜索target ，如果数组中存在这个目标值，则返回它的索引，否则返回-1。  
> !!!把比较好些的判断（target 落在有序的那部分）放在 if 的开头考虑，把剩下的情况放在 else 里面。!!!  
> 
```java
public class LeetCode33 {
    public int search(int[] nums, int target) {
        int len = nums.length;
        int left = 0, right = len - 1;
        while (left < right) {
//            int mid = left + (right - left) / 2;
            // 采用向上取整，[left, mid - 1], [mid, right]
            int mid = left + (right - left + 1) / 2;
            if (nums[mid] == target) {
                return mid;
            }
            if(nums[mid] < nums[right]) {  // 右区间有序
                // target在有序区间内
                if(nums[mid] <= target && target <= nums[right]) {
                    left = mid;
                }else {
                    right = mid - 1;
                }
            }else { // 左区间有序
                // [left, mid] 有序，但是为了和上一个 if 有同样的收缩行为，
                // 我们故意只认为 [left, mid - 1] 有序
                // 当区间只有 2 个元素的时候 int mid = (left + right + 1) >>> 1; 一定会取到右边
                // 此时 mid - 1 不会越界，就是这么刚刚好
                // target在有序区间内
                if(nums[left] <= target && target <= nums[mid - 1]) {
                    right = mid - 1;
                }else {
                    left = mid;
                }
            }
        }
        return nums[left] == target ? left : -1;
    }
}
```  
  
## LeetCode-81. 搜索旋转排序数组 II  
[81. 搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)  
假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。

编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。
> 数组中可能有重复元素，增加了一种case  

```java
public boolean search(int[] nums, int target) {
    int len = nums.length;
    if (len == 0) {
        return false;
    }
    int left = 0, right = len - 1;
    while (left < right) {
        // 采用向上取整，[left, mid - 1], [mid, right]
        int mid = left + (right - left + 1) / 2;
        if (nums[mid] == target) {
            return true;
        }
        if (nums[mid] < nums[right]) {  // 右区间有序（严格小于）
            // target在有序区间内
            if (nums[mid] <= target && target <= nums[right]) {
                left = mid;
            } else {
                right = mid - 1;
            }
        } else if (nums[mid] > nums[right]) {
            // 右半部分一定无序，做边可能有序，可能重复
            // 因此判断条件不能写成nums[mid] > nums[right]
            // 为了保持收缩一致，我们认为[left, mid - 1]是有序的（同33题）
            // 如果target在[left, mid - 1]区间内
            if (nums[left] <= target && target <= nums[mid - 1]) {
                right = mid - 1;
            } else {
                left = mid;
            }
        } else {
            // nums[mid] == nums[right]
            // 表示nums[right] != target，将right位置排除
            right--;
        }
    }
    return nums[left] == target;
}
```  

## LeetCode-153. 寻找旋转排序数组中的最小值  
[LeetCode-153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)  
假设按照升序排序的数组在预先未知的某个点上进行了旋转。  
( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。  
请找出其中最小的元素。  
你可以假设数组中不存在重复元素。  
- 有了上面两题的铺垫，这题比较容易解决
```java
public int findMin(int[] nums) {
        int len = nums.length;
        int min = Integer.MAX_VALUE;
        if (len == 0) {
            return 0;
        }
        if (len == 1) {
            return nums[0];
        }
        int left = 0, right = len - 1;
        while (left < right) {
            int mid = left + (right - left + 1) / 2;
            if (nums[mid] > nums[left]) { // 左区间有序
                min = Math.min(min, nums[left]);
                left = mid;
            } else {
                min = Math.min(min, nums[mid]);
                right = mid - 1;
            }
        }
        return min;
    }
```  

## LeetCode-154. 寻找旋转排序数组中的最小值 II  
[LeetCode-154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)  
假设按照升序排序的数组在预先未知的某个点上进行了旋转。  
( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。  
请找出其中最小的元素。  
注意数组中可能存在重复的元素。  
    
```java
public int findMin(int[] nums) {
    int len = nums.length;
    int min = Integer.MAX_VALUE;
    if (len == 0) {
        return 0;
    }
    if (len == 1) {
        return nums[0];
    }
    int left = 0, right = len - 1;
    while (left < right) {
        int mid = left + (right - left + 1) / 2;
        if (nums[mid] > nums[left]) {
            min = Math.min(min, nums[left]);
            left = mid;
        } else if (nums[mid] < nums[right]) {
            min = Math.min(min, nums[mid]);
            right = mid - 1;
        } else {
            min = Math.min(min, nums[right--]);
        }
    }
    return min;
}
```
  
## LeetCode-300. 最长上升子序列  
[LeetCode-300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)  
给定一个无序的整数数组，找到其中最长上升子序列的长度。  

```java
/**
* 1. 动态规划
* 状态：dp[i]表示：以nums[i]结尾的 上升子序列 的长度
* 状态转移方程：dp[i] = max{1 + dp[j] for j < i if nums[j] > nums[i]}
* 时间复杂的：O(N^2)
* 空间复杂度：O(N)
*/
public int lengthOfLIS_dp(int[] nums) {
    int len = nums.length;
    if (len == 0 || len == 1) {
        return len;
    }
    int[] dp = new int[len];
    Arrays.fill(dp, 1);

    for (int i = 1; i < len; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
    }
    int res = 0;
    for (int i = 0; i < len; i++) {
        res = Math.max(res, dp[i]);
    }
    return res;
}

/**
    * 2. 修改状态定义（贪心 + 二分）
    */
public int lengthOfLIS_2(int[] nums) {
    int len = nums.length;
    if(len <= 1) {
        return len;
    }
    // tail数组的定义：长度为i+1的上升子序列的末尾最小是几
    int[] tail = new int[len];
    tail[0] = nums[0];
    // end表示有序数组tail的最后一个已经赋值的索引
    int end = 0;
    for (int i = 1; i < len; i++) {
        // 比tail数组实际有效的末尾的那个元素还大
        if(nums[i] > tail[end]) {
            // 直接添加在那个元素的后面，end先+1
            end++;
            tail[end] = nums[i];
        }else {
            // 使用二分查找，在有序数组tail中
            // 找到第一个大于等于nums[i]的元素，尝试让那个元素更小
            int left = 0, right = end;
            while(left < right) {
                int mid = left + (right - left) / 2;
                if(tail[mid] < nums[i]) {
                    // 中位数肯定不是要找的数，把他算进分支前面
                    left = mid + 1;
                }else {
                    right = mid;
                }
            }
            // 走到这里是因为 【逻辑 1】 的反面，因此一定能找到第 1 个大于等于 nums[i] 的元素
            // 因此，无需再单独判断
            tail[left] = nums[i];
        }
    }
    // 此时end是有序数组tail最后一个元素的索引
    // 题目要求返回的是长度，因此为end++
    end++;
    return end;
}
```  

## LeetCode-852. 山脉数组的峰顶索引  
[852. 山脉数组的峰顶索引](https://leetcode-cn.com/problems/peak-index-in-a-mountain-array/)  
我们把符合下列属性的数组 A 称作山脉：  
A.length >= 3  
存在 0 < i < A.length - 1 使得A[0] < A[1] < ... A[i-1] < A[i] > A[i+1] > ... > A[A.length - 1]  
给定一个确定为山脉的数组，返回任何满足 A[0] < A[1] < ... A[i-1] < A[i] > A[i+1] > ... > A[A.length - 1] 的 i 的值。  

```java
    public int peekIndexInMountainArray(int[] arr) {
        int len = arr.length;
        int left = 0, right = len - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] < arr[mid + 1]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
```  

## LeetCode-1095. 山脉数组中查找目标值  
[1095. 山脉数组中查找目标值](https://leetcode-cn.com/problems/find-in-mountain-array/)  
给你一个 山脉数组 mountainArr，请你返回能够使得 mountainArr.get(index) 等于 target 最小 的下标 index 值。  
如果不存在这样的下标 index，就请返回 -1。  
何为山脉数组？如果数组 A 是一个山脉数组的话，那它满足如下条件：  
首先，A.length >= 3  
其次，在 0 < i < A.length - 1 条件下，存在 i 使得：  
A[0] < A[1] < ... A[i-1] < A[i]  
A[i] > A[i+1] > ... > A[A.length - 1]  
你将 不能直接访问该山脉数组，必须通过 MountainArray 接口来获取数据：  
MountainArray.get(k) - 会返回数组中索引为k 的元素（下标从 0 开始）  
MountainArray.length() - 会返回该数组的长度  
注意：  
对 MountainArray.get 发起超过 100 次调用的提交将被视为错误答案。此外，任何试图规避判题系统的解决方案都将会导致比赛资格被取消。  

> 思路：  
> 1. 先找到山峰顶点坐标，左边就为递增序列，右边为递减序列  
> 2. 先对左边递增序列，二分查找，找到则返回下标，没有就返回-1  
> 3. 左边如果返回-1，再对右边区间用二分查找  
  
```java
interface MountainArray {
    public int get(int index);

    public int length();
}

class MountainArrayImpl implements MountainArray {
    private int[] arr;
    private int size;

    public MountainArrayImpl(int[] arr) {
        this.arr = arr;
        this.size = arr.length;
    }

    @Override
    public int get(int index) {
        return this.arr[index];
    }

    @Override
    public int length() {
        return this.size;
    }

}


public class LeetCode1095 {
    public int findInMountainArray(int target, MountainArray mountainArr) {
        int mountainTop = peekMountainTop(mountainArr);
        int res = findLeft(mountainArr, 0, mountainTop, target);
        if (res != -1) {
            return res;
        } else {
            return findRight(mountainArr, mountainTop, mountainArr.length() - 1, target);
        }
    }

    // 在做递增区间找target
    public int findLeft(MountainArray arr, int lo, int hi, int target) {
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (arr.get(mid) == target) {
                return mid;
            }
            if (arr.get(mid) < target) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        if (arr.get(lo) == target) {
            return lo;
        } else { // 不在左区间
            return -1;
        }
    }

    public int findRight(MountainArray arr, int lo, int hi, int target) {
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (arr.get(mid) == target) {
                return mid;
            }
            if (arr.get(mid) > target) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        if (arr.get(lo) == target) {
            return lo;
        } else {
            return -1;
        }
    }

    // 找山顶坐标
    public int peekMountainTop(MountainArray arr) {
        int len = arr.length();
        int left = 0, right = len - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (arr.get(mid) < arr.get(mid + 1)) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
```

## LeetCode-1300. 转变数组后最接近目标值的数组和  
[LeetCode-1300. 转变数组后最接近目标值的数组和 ](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)  
给你一个整数数组 arr 和一个目标值 target ，请你返回一个整数 value ，使得将数组中所有大于 value 的值变成 value 后，数组的和最接近  target （最接近表示两者之差的绝对值最小）。  
如果有多种使得和最接近 target 的方案，请你返回这些整数中的最小值。  
请注意，答案不一定是 arr 中的数字。  

```java
public int findBestValue(int[] arr, int target) {
        Arrays.sort(arr);
        int lo = 0, hi = arr[arr.length - 1];
        while (lo < hi) {
            int mid = lo + (hi - lo + 1) / 2;
            int sum_l = helper(arr, mid - 1, target);
            int sum_m = helper(arr, mid, target);
            int sum_r = helper(arr, mid + 1, target);
            if (sum_l > sum_m) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }
        return lo;
    }

// 计算数组和 与 target 的绝对值
public int helper(int[] arr, int value, int target) {
    int sum = 0;
    for (int num : arr) {
        sum += Math.min(num, value);
    }
    return Math.abs(sum - target);
}
```  

## 





- - -  
参考链接：  
* 模板参考自[ACWing](https://www.acwing.com/blog/content/277/):+1:  
* [LeetCode-4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/er-fen-fa-duo-yu-yan-javajs4-xun-zhao-liang-ge-zhe/)  
* [LeetCode-35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/solution/te-bie-hao-yong-de-er-fen-cha-fa-fa-mo-ban-python-/)：这个写的很好:+1:  
* [旋转数组参考链接](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/er-fen-fa-python-dai-ma-java-dai-ma-by-liweiwei141/)  
* [旋转数组参考链接2](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/solution/er-fen-cha-zhao-by-liweiwei1419/)  
* [LeetCode-300. 最长上升子序列 题解参考链接](https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/dong-tai-gui-hua-er-fen-cha-zhao-tan-xin-suan-fa-p/)
