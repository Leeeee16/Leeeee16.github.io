# 算法-排序算法


排序算法的java实现
<!--more-->
## 快速排序  
### 1.基本快速排序  
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Quick_Sort {
    public static void quick_sort(int[] q, int l, int r) {
        if (l >= r) return;
        int i = l - 1, j = r + 1; // 为了后面循环，设置边界
        int pivot = q[l + r >> 1]; // 定义中轴数
        while (i < j) {
            do i++; while (q[i] < pivot);
            do j--; while (q[j] > pivot);
            if (i < j) { // 交换
                int tmp = q[i];
                q[i] = q[j];
                q[j] = tmp;
            }
        }
        quick_sort(q, l, j);
        quick_sort(q, j + 1, r);
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine()); // 读入数组的长度
        String[] s = br.readLine().split(" "); //  读入数组
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            nums[i] = Integer.parseInt(s[i]); // 将字符串数组转换为int数组
        }
        quick_sort(nums, 0, n - 1);
        for (int i = 0; i < n; i++) {
            System.out.print(nums[i] + " ");    // 输出排序后的结果
        }
    }
}
```  

## 归并排序  
### 1.基本的归并排序  
1. [l, r] -> [l, mid], [mid + 1, r]  
2. 递归排序[l, mid] and [mid + 1, r]  
3. 归并，将左右两个有序序列合并为一个有序序列  
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;


public class Merge_Sort {
    static int N = 100010;
    static int[] tmp = new int[N];  // 定义一个辅助数组，暂时存放两个子数组合并后的结果

    public static void merge_sort(int[] nums, int l, int r) {
        if (l >= r) return;
        int mid = l + r >> 1;
        merge_sort(nums, l, mid);
        merge_sort(nums, mid + 1, r);

        int k = 0, i = l, j = mid + 1;  // k为辅助数组下标
        while (i <= mid && j <= r) {    // 合并两个子数组，将两个子数组中较小的 放入 tmp数组
            if (nums[i] <= nums[j]) tmp[k++] = nums[i++];
            else tmp[k++] = nums[j++];
        }

        while (i <= mid) tmp[k++] = nums[i++]; // 如果 前半部分还没遍历完，将剩余的放入tmp数组
        while (j <= r) tmp[k++] = nums[j++];

        for (i = l, j = 0; i <= r; i++,j++) nums[i] = tmp[j];  // 将两个子数组合并后的结果(即tmp数组) 存回 原数组
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        System.out.println("输入数组的长度：");
        int n = Integer.parseInt(br.readLine());
        System.out.println("输入待排序数组：");
        String[] s = br.readLine().split(" "); // 读取数组
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) nums[i] = Integer.parseInt(s[i]); // 将字符数组转为int数组
        merge_sort(nums, 0, n - 1);
        for (int i = 0; i < n; i++) System.out.print(nums[i] + " ");
        br.close();
    }
}
```  

### 2.逆序对的数量  
给定一个长度为n的整数数列，请你计算数列中的逆序对的数量。  
> 逆序对的定义如下：对于数列的第 i 个和第 j 个元素，如果满足 i < j 且 a[i] > a[j]，则其为一个逆序对；否则不是。  
* 思路：  
定义merge_sort()函数返回数组中的逆序对。  
采用分治思想，将数组分层左右两部分，那么逆序对存在三种情况：  
  1. 两个元素都在左边的子数组中  
  2. 两个元素都在右边的子数组中  
  3. 一个在左边，一个在右边  
* 求解：  
  1. 左半边内部的逆序对数量：merge_sort(l, mid)  
  2. 右半边内部的逆序对数量：merge_sort(mid + 1, r)  
  3. 在归并两子数组时，计算情况3的逆序对数量：`res += mid - i + 1;`  
    当左子数组的元素nums[i] 大于 右子数组的元素nums[j]时，那么 i 到 mid 之间的所有元素都大于nums[j]，即结果要加上`mid - i + 1`  

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;


public class Main {
    static int N = 100010;
    static int[] tmp = new int[N]; // 归并排序的辅助数组

    public static long merge_sort(int[] nums, int l, int r) {
        if (l >= r) return 0;
        int mid = l + r >> 1;
        long res = merge_sort(nums, l, mid) + merge_sort(nums, mid + 1, r);

        int k = 0, i = l, j = mid + 1;
        while (i <= mid && j <= r) {
            if (nums[i] <= nums[j]) tmp[k++] = nums[i++];
            else {
                res += mid - i + 1; // 逆序元素，一个在左边，一个在右边的情况
                tmp[k++] = nums[j++];
            }
        }
        while (i <= mid) tmp[k++] = nums[i++];
        while (j <= r) tmp[k++] = nums[j++];

        for(i = l, j = 0; i <= r; i++, j++) nums[i] = tmp[j];
        return res;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("输入数组的长度：");
        int n = Integer.parseInt(br.readLine());
        System.out.println("输入数组：");
        String[] s = br.readLine().split(" ");
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) nums[i] = Integer.parseInt(s[i]);
        long res = merge_sort(nums, 0, n - 1);
        System.out.println("逆序对的数量为:" + res);
        br.close();
    }
}
```




