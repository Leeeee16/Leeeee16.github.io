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

## LeetCode-410. 分割数组的最大值  
[410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)  
给定一个非负整数数组和一个整数 m，你需要将这个数组分成 m 个非空的连续子数组。设计一个算法使得这 m 个子数组各自和的最大值最小。  
注意:  
数组长度 n 满足以下条件:  
1 ≤ n ≤ 1000  
1 ≤ m ≤ min(50, n)  
> 这道题的思路关键点：  
> 设：maxIntervalSum表示 连续子数组各自和的最大值  
> 那么 maxIntervalSum越大，说明分的组数 M 就越少  
> 反之，如果maxIntervalSum越小，说明分的组数 M 就越大  
> 因此 组数 和 连续子数组各自和的最大值 是一个单调递减的函数  
> 因此可以采用二分查找  

```java
public int splitArray(int[] nums, int m) {
    int max = 0;
    int sum = 0;

    // 计算 子数组各自的和的最大值 的上下界
    // 最大就是数组所有元素的和(即m=1)，最小为 数组最大的元素值
    for (int num : nums) {
        max = Math.max(max, num);
        sum += num;
    }

    // 使用二分查找，确定一个恰当的 子数组各自的和的最大值
    // 使得它对应的 子数组的分割数 恰好等于 m
    int left = max, right = sum;
    while (left < right) {
        int mid = left + (right - left) / 2;
        int splits = split(nums, mid);
        if (splits > m) {
            // 如果分割数太多，说明 子数组各自的和的最大值 太小，此时需要 增大 子数组各自的和的最大值
            // 下一轮搜索区间 [mid + 1, right]
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return left;
}

/**
    * @param nums           原始数组
    * @param maxIntervalSum 子数组各自的和的最大值
    * @return 满足不超过 子数组各自的最大值 的分割数
    */
private int split(int[] nums, int maxIntervalSum) {
    // 至少是一个分割
    int splits = 1;
    // 当前区间的和
    int curIntervalSum = 0;
    for (int num : nums) {
        // 尝试加上当前遍历的这个数，如果加上后超过了 子数组各自的和的最大值，就不加这个数
        if (curIntervalSum + num > maxIntervalSum) {
            curIntervalSum = 0;
            splits++;
        }
        curIntervalSum += num;
    }
    return splits;
}
```
  
## LeetCode-875. 爱吃香蕉的珂珂  
[875. 爱吃香蕉的珂珂](https://leetcode-cn.com/problems/koko-eating-bananas/)  
珂珂喜欢吃香蕉。这里有 N 堆香蕉，第 i 堆中有 piles[i] 根香蕉。警卫已经离开了，将在 H 小时后回来。  
珂珂可以决定她吃香蕉的速度 K （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 K 根。如果这堆香蕉少于 K 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。  
珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。  
返回她可以在 H 小时内吃掉所有香蕉的最小速度 K（K 为整数）。  
> 思路和上一题几乎一样~  
> 每小时吃的越多，小时数就越少
> 利用这个单调减函数，进行二分

```java
public int minEatingSpeed(int[] piles, int H) {
    int max = 0; // 记录最大元素
    // K 的取值：最小 = 1， 最大 = 最大元素
    // h 的取值：最大 = 元素和， 最小 = 元素个数
    for(int num : piles) {
        max = Math.max(max, num);
    }
    // 每小时吃的越多，小时数就越少
    // 二分，确定一个恰当的k，使得小时数刚好为 H
    int left = 1, right = max;
    while(left < right) {
        int mid = left + (right - left) / 2;
        int h = helper(piles, mid);
        if(h > H) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return right;

}

/**
* @param piles 原数组
* @param k 当前每小时吃的个数
* @return  返回需要的小时数
*/
public int helper(int[] piles, int k) {
    int h = 0;
    for(int num : piles) {
        // 向上取整
        // h += num % k == 0? num / k : num / k + 1;
        h += (num + k - 1) / k;
    }
    return h;
}
```  

## LeetCode-162. 寻找峰值  
[162. 寻找峰值](https://leetcode-cn.com/problems/find-peak-element/)  
峰值元素是指其值大于左右相邻值的元素。  
给定一个输入数组 nums，其中 nums[i] ≠ nums[i+1]，找到峰值元素并返回其索引。  
数组可能包含多个峰值，在这种情况下，返回任何一个峰值所在位置即可。  
你可以假设 nums[-1] = nums[n] = -∞。  

```java
public int findPeakElement(int[] nums) {
    int left = 0, right = nums.length - 1;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < nums[mid + 1]) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return left;
}
```  

## LeetCode-240. 搜索二维矩阵 II  
[LeetCode-240. 搜索二维矩阵 II ](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)  
编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：  
**每行的元素从左到右升序排列。**  
**每列的元素从上到下升序排列。**  
  
1. 方法一：  
   逐行遍历，每行采用二分查找  
   是我首先想到的解法，只用到了行有序，列有序没想到怎么用orz...  
   时间复杂度：O(m*lgn)  
```java
    public boolean searchMatrix(int[][] matrix, int target) {
            int m = matrix.length;
            if (m == 0) {
                return false;
            }
            int n = matrix[0].length;
            if (n == 0) {
                return false;
            }
            for (int i = 0; i < m; i++) {
                if (matrix[i][n - 1] < target) {
                    continue;
                }
                boolean res = bsearch(matrix[i], target);
                if (res) {
                    return true;
                }
            }
            return false;
        }

    public boolean bsearch(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return nums[left] == target;
    }
```  

2. 方法二：  
   迭代遍历矩阵对角线，从当前元素对行和列搜索  
   时间复杂度：O(lg(n!))O(lg(n!)),推导可见[官方题解](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/solution/sou-suo-er-wei-ju-zhen-ii-by-leetcode-2/)  

```java
    public boolean searchMartrix_2(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) {
            return false;
        }
        // 遍历矩阵对角线
        int shorterDim = Math.min(matrix.length, matrix[0].length);
        for (int i = 0; i < shorterDim; i++) {
            boolean verticalFound = bsearch_2(matrix, target, i, true);
            boolean horizontalFound = bsearch_2(matrix, target, i, false);
            if (verticalFound || horizontalFound) {
                return true;
            }
        }
        return false;
    }

    /**
    * @param martrix  原始数组
    * @param target   目标值
    * @param start    开始搜索的下标
    * @param vertical 判断是行搜索还是列搜索
    * @return
    */
    public boolean bsearch_2(int[][] martrix, int target, int start, boolean vertical) {
        int lo = start;
        int hi = vertical ? martrix[0].length - 1 : martrix.length - 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (vertical) {  // 搜索行
                if (martrix[start][mid] < target) {
                    lo = mid + 1;
                } else {
                    hi = mid;
                }
            } else {    // 搜索列
                if (martrix[mid][start] < target) {
                    lo = mid + 1;
                } else {
                    hi = mid;
                }
            }
        }
        if (vertical) {
            return martrix[start][lo] == target;
        } else {
            return martrix[lo][start] == target;
        }
    }
```  

3. 方法三(tql)：  
由题目条件可知，**每行的元素从左到右升序排列。**，**每列的元素从上到下升序排列。**因此我们可以在搜索过程中修剪O(m)或者O(n)。  
* 首先初始化一个指向矩阵左下角的(row, col)指针。  
* 直到找到目标并返回true,或者指针移动到矩阵维度之外，我们执行以下操作做：  
   如果当前值大于目标值，则向上移动一行，即`row--`；如果当前值小于目标值，则向右移动一列，即`col++`。  
* 这个二维矩阵，对于每个数来说，上面的数都小于它，右边的数都大于它，因此可以在搜索的时候进行修剪。  
* 时间复杂度为：O(m + n)  

```java
    public boolean searchMatrix_3(int[][] matrix, int target) {
        // 从矩阵左下角开始搜索
        int row = matrix.length - 1;
        int col = 0;
        while (row >= 0 && col < matrix[0].length) {
            if (matrix[row][col] > target) {
                row--;
            } else if (matrix[row][col] < target) {
                col++;
            } else {
                return true;
            }
        }
        return false;
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
* [LeetCode-410. 分割数组的最大值 题解参考](https://leetcode-cn.com/problems/split-array-largest-sum/solution/er-fen-cha-zhao-by-liweiwei1419-4/)
