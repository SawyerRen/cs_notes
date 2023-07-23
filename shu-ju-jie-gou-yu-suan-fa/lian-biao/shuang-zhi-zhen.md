# 双指针

本文总结一下单链表解题的基本技巧，这些技巧都用到了双指针。

1. 合并两个有序链表
2. 链表的分解
3. 寻找单链表的倒数第 `k` 个节点
4. 寻找单链表的中点
5. 判断单链表是否包含环并找出环起点
6. 判断两个单链表是否相交并找出交点

### 合并两个有序链表 <a href="#he-bing-liang-ge-you-xu-lian-biao" id="he-bing-liang-ge-you-xu-lian-biao"></a>

最基本的链表技巧，[leetcode21](https://leetcode.com/problems/merge-two-sorted-lists/)问题。

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

有两个有序的链表，我们要把它们合并成一个新的有序的链表。

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        // 虚拟头部节点
        ListNode dummy = new ListNode();
        ListNode cur = dummy;
        while (list1 != null && list2 != null) {
            // 比较list1和list2节点的大小，将较小的放到cur的后面
            if (list1.val < list2.val) {
                cur.next = list1;
                list1 = list1.next;
            } else {
                cur.next = list2;
                list2 = list2.next;
            }
            // cur向右移
            cur = cur.next;
        }
        if (list1 != null) {
            cur.next = list1;
        } else {
            cur.next = list2;
        }
        return dummy.next;
    }
}
```

每次比较两个指针指向的node的大小，将较小的节点接到结果链表上。

除了双指针，这题还用到了**虚拟头部节点，**这个虚拟头部节点让我们不需要处理cur指针为空的情况，降低了复杂性。

{% hint style="info" %}
什么时候需要虚拟头节点？**当你需要创造一条新链表的时候，可以使用虚拟头结点简化边界情况的处理**。
{% endhint %}

### 分解链表 <a href="#he-bing-liang-ge-you-xu-lian-biao" id="he-bing-liang-ge-you-xu-lian-biao"></a>

例如[leetcode86](https://leetcode.com/problems/partition-list/description/)

![](<../../.gitbook/assets/image (2).png>)

这里需要把链表一分为二，分成两个小链表，其中一个小链表的元素都大于等于x，另一个的元素都小于x，最后把这两个链表接到一起。

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        // largeHead是大于等于x的链表的虚拟头节点
        // smallHead是小于x的链表的虚拟头节点
        ListNode largeHead = new ListNode(), smallHead = new ListNode();
        // large和small是两个结果链表的指针
        ListNode large = largeHead, small = smallHead;
        // 遍历链表，将链表分为两个链表
        while (head != null) {
            // 注意这里断开了和原来链表的连接
            ListNode next = head.next;
            head.next = null;
            if (head.val < x) {
                small.next = head;
                small = small.next;
            } else {
                large.next = head;
                large = large.next;
            }
            head = next;
        }
        // 连接两个链表
        small.next = largeHead.next;
        return smallHead.next;
    }
}
```

### 单链表的倒数第 k 个节点 <a href="#dan-lian-biao-de-dao-shu-dikge-jie-dian" id="dan-lian-biao-de-dao-shu-dikge-jie-dian"></a>

当我们要寻找一个链表的倒数节点时，直觉上的方法是先遍历一遍链表，得到链表的长度为n，这样倒数第k个节点就是第`n-k+1`个节点，然后遍历去得到这个节点。

但是，我们可以用一个遍历完成这个问题嘛？

我们可以让指针1和指针2指向虚拟头节点，指针2先向右移k步，保持指针1和指针2的距离不变，一起右移，直到指针2到达链表的结尾，这时指针1的next节点就是倒数第k个节点。

例如[leetcode19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/description/)

![](<../../.gitbook/assets/image (5).png>)

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode();
        dummy.next = head;
        ListNode left = dummy, right = dummy;
         // right 先走 n 步
        for (int i = 0; i < n; i++) {
            right = right.next;
        }
        // left和right同时右移
        while (right.next != null) {
            left = left.next;
            right = right.next;
        }
        // 移除left的下一个节点
        left.next = left.next.next;
        return dummy.next;
    }
}
```

### 单链表的中点

我们当然可以先获得链表的长度，然后找到中间节点，但是我们可以用一次遍历完成吗？

我们可以用**快慢指针**，让两个指针分别指向头节点，每当慢指针前进一步，快指针就右移两步，当快指针指向结尾时，慢指针就指向中点。

例如[leetcode876](https://leetcode.com/problems/middle-of-the-linked-list/description/)

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        // 快慢指针初始化指向 head
        ListNode slow = head, fast = head;
        // 快指针走到末尾时停止
        while (fast != null && fast.next != null) {
            // 慢指针走一步，快指针走两步
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```

### 判断链表是否包含环 <a href="#pan-duan-lian-biao-shi-fou-bao-han-huan" id="pan-duan-lian-biao-shi-fou-bao-han-huan"></a>

同样用快慢指针，如果fast指针最后变成null，说明没有环，如果fast和slow相遇，说明fast比slow多跑了一圈，说明这个链表有环，[leetcode141](https://leetcode.com/problems/linked-list-cycle/description/)

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) return false;
        // 快慢指针初始化指向 head
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            // 快慢指针相遇，说明含有环
            if (slow == fast) return true;
        }
        return false;
    }
}
```

这题还有一个进阶版，就是去寻找这个环的起点，[leetcode142](https://leetcode.com/problems/linked-list-cycle-ii/description/)

当快慢指针相遇的时候，让其中一个指针指向头节点，然后以同样的速度右移，再相遇的时候，指向的节点就是环的开始节点。

![](<../../.gitbook/assets/image (4).png>)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                // 重新指向头结点
                slow = head;
                // 快慢指针同步前进，相交点就是环起点
                while (slow != fast) {
                    slow = slow.next;
                    fast = fast.next;
                }
                return slow;
            }
        }
        return null;
    }
}
```

### 两个链表是否相交 <a href="#liang-ge-lian-biao-shi-fou-xiang-jiao" id="liang-ge-lian-biao-shi-fou-xiang-jiao"></a>

[leetcode160](https://leetcode.com/problems/intersection-of-two-linked-lists/description/)

![](<../../.gitbook/assets/image (1).png>)

所以，我们可以让 `p1` 遍历完链表 `A` 之后开始遍历链表 `B`，让 `p2` 遍历完链表 `B` 之后开始遍历链表 `A`，这样相当于「逻辑上」两条链表接在了一起。

如果这样进行拼接，就可以让 `p1` 和 `p2` 同时进入公共部分，也就是同时到达相交节点 `c1`

![](<../../.gitbook/assets/image (3).png>)

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // p1 指向 A 链表头结点，p2 指向 B 链表头结点
        ListNode p1 = headA, p2 = headB;
        while (p1 != p2) {
            // p1 走一步，如果走到 A 链表末尾，转到 B 链表
            p1 = p1 == null ? headB : p1.next;
            // p2 走一步，如果走到 B 链表末尾，转到 A 链表
            p2 = p2 == null ? headA : p2.next;
        }
        return p1;
    }
}
```
