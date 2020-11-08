# 双指针

  
双指针
<!--more-->  

## LeetCode-141. 环形链表  
[141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)  
给定一个链表，判断链表中是否有环。  
```java
    /**
    * Definition for singly-linked list.
    * class ListNode {
    *     int val;
    *     ListNode next;
    *     ListNode(int x) {
    *         val = x;
    *         next = null;
    *     }
    * }
    */
    public class Solution {
        public boolean hasCycle(ListNode head) {
            if (head == null || head.next == null) return false;
            ListNode fast = head;
            ListNode slow = head;
            while(fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
                if(fast == slow) return true;
            }
            return false;
        }
    }
```  

## LeetCode-142. 环形链表 II  
[142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)  
给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。  
> 思路：假设到环入口的步数为a, 环内的步数为b  
> 走到环入口的步数为：a + nb 步  
> 假设慢指针走s步，快指针走f步，那么f = 2s  
> 快指针比慢指针多走了n个环的长度，f = s + nb  
> 两式相减，得到s = nb，也就是第一次相遇时，慢指针已经走了nb步    
> 第一次相遇后，slow慢指针位置不变，将快指针指向链表头结点，两个指针每轮向前走一步，那么两个指针相遇时，分别走了a 和 a + nb步，也就是在环入口相遇  
```java
    /**
    * Definition for singly-linked list.
    * class ListNode {
    *     int val;
    *     ListNode next;
    *     ListNode(int x) {
    *         val = x;
    *         next = null;
    *     }
    * }
    */
    public class Solution {
        public ListNode detectCycle(ListNode head) {
            if(head == null || head.next == null) {
                return null;
            }
            ListNode slow = head;
            ListNode fast = head;
            boolean isCycle = false;
            while(fast.next!=null && fast.next.next!=null) {
                fast = fast.next.next;
                slow = slow.next;
                if(slow == fast) {
                    isCycle = true;
                    break;
                }
            }
            if(isCycle) {
                fast = head;
                while(slow != fast) {
                    slow = slow.next;
                    fast = fast.next;
                }
                return slow;
            }else {
                return null;
            }
        }
    }
```



