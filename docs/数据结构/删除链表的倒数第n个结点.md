#### [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

### 解决方案

快慢指针法，快慢指针法有两种，一种是从一个方向出发，一种是从两边向中间出发，本题使用了第一种，从一个方向出发，让快指针先走n步，这样当快指针的next指针是null时，慢指针的next指针就是要删除的节点，

还要注意的是，链表的问题要给它的头部节点再加一个节点，这样会避免一些空指针异常

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
            if (head == null) {
                return head;
            }
            ListNode pre = new ListNode(0);
            pre.next = head;
            ListNode fast = pre;
            ListNode slow = pre;

            for (int i = 0; i < n ; i++) {
                fast = fast.next;
            }

            while (fast.next != null) {
                fast = fast.next;
                slow = slow.next;
            }

            slow.next = slow.next.next;
            
            return pre.next;

    }
}
```

