# 双指针

双指针在数组和链表中运用的非常广泛，双指针技巧主要分为两类：**左右指针**和**快慢指针**。

所谓左右指针，就是两个指针相向而行或者相背而行；而所谓快慢指针，就是两个指针同向而行，一快一慢。

下面直接看题目

## 快慢指针

### [leetcode26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/)

这里要我们原地修改，而不是直接新建一个数组返回，只能在原数组上操作。由于数组已经排序，重复的元素都连在一起。我们可以用到快慢指针。

我们让慢指针 `slow` 走在后面，快指针 `fast` 走在前面探路，找到一个不重复的元素就赋值给 `slow` 并让 `slow` 前进一步。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        // 慢指针
        int index = 0;
        // 快指针遍历数组
        for (int i = 1; i < nums.length; i++) {
            // 遇到不同的数
            if (nums[i] != nums[index]) {
                nums[++index] = nums[i];
            }
        }
        return index + 1;
    }
}
```

同理可以应用于[leetcode83](https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/)

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        ListNode fast = head, slow = head;
        while (fast != null) {
            if (fast.val != slow.val) {
                slow.next = fast;
                slow = slow.next;
            }
            fast = fast.next;
        }
        slow.next = null;
        return head;
    }
}
```

除了去重，还可以对数组中的指定元素进行删除操作，比如[leetcode27](https://leetcode.com/problems/remove-element/description/).

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int index = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != val) {
                nums[index++] = nums[i];
            }
        }
        return index;
    }
}
```

我们再看看[leetcode283](https://leetcode.com/problems/move-zeroes/description/)，这题其实就是删除数组中的0.

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int index = 0;
        for (int num : nums) {
            if (num != 0) {
                nums[index++] = num;
            }
        }
        while (index < nums.length) {
            nums[index++] = 0;
        }
    }
}
```

## 左右指针

二分查找是一种左右指针，这里不具体讨论了。

我们看一下[leetcode167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)

数组有序，一般都可以想到双指针，这题其实类似于二分查找。

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        // 一左一右两个指针相向而行
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) return new int[]{left + 1, right + 1};
            if (sum < target) left++;
            else right--;
        }
        return new int[2];
    }
}
```

反转数组：[leetcode344](https://leetcode.com/problems/reverse-string/description/)

```java
class Solution {
    public void reverseString(char[] s) {
        int i = 0, j = s.length - 1;
        while (i < j) {
            char c = s[i];
            s[i] = s[j];
            s[j] = c;
            i++;
            j--;
        }
    }
}
```

最长回文子串：[leetcode5](https://leetcode.com/problems/longest-palindromic-substring/description/)

这题有两个点，一个是判断回文子串，这个可以通过双指针来判断

```java
boolean isPalindrome(String s) {
    // 一左一右两个指针相向而行
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

第二点就是要找出其中最长的回文子串，这题的思路是**从中心向两端扩散的双指针技巧**。这样会有两种情况，奇数长度的字符串，中心是一个字符，偶数长度的字符串，中心是两个字符，我们遍历这个字符串，以每个字符为中心，对这两种情况分别进行扩散，找出其中最长的回文子串。

```java
class Solution {
    int left = 0, right = 0, maxLen = 0;

    public String longestPalindrome(String s) {
        for (int i = 0; i < s.length(); i++) {
            helper(s, i, i);
            helper(s, i, i + 1);
        }
        return s.substring(left, right);
    }

    private void helper(String s, int i, int j) {
        while (i >=0 && j < s.length() && s.charAt(i) == s.charAt(j)) {
            i--;
            j++;
        }
        int len = j - i - 1;
        if (len > maxLen) {
            maxLen = len;
            left = i + 1;
            right = j;
        }
    }
}
```
