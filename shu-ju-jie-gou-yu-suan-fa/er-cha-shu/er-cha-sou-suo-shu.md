# 二叉搜索树

首先，BST的特性如下：

1. 对于 BST 的每一个节点 `node`，左子树节点的值都比 `node` 的值要小，右子树节点的值都比 `node` 的值大。
2. 对于 BST 的每一个节点 `node`，它的左侧子树和右侧子树都是 BST。
3. BST的中序遍历结果是有序的。

## 寻找第k小的元素

[leetcode230](https://leetcode.com/problems/kth-smallest-element-in-a-bst/description/)

这里就可以利用中序遍历有序的特点，直接找到第k个元素。

```java
class Solution {
    // 记录当前个数
    int count = 0;
    // 记录结果
    int res = 0;

    public int kthSmallest(TreeNode root, int k) {
        helper(root, k);
        return res;
    }

    private void helper(TreeNode root, int k) {
        if (root == null) return;
        helper(root.left, k);
        // 中序位置
        count++;
        // 找到第k个元素
        if (count == k) {
            res = root.val;
            return;
        }
        helper(root.right, k);
    }
}
```

通过一次中序遍历就可以解决这道题，时间复杂度是O(N)。其实这不是最优解法，利用BST的性质，其实是可以做到O(logN)的解法，但是需要额外的定义数据结构，这里就不展开说了。这个解法已经足够了。

## BST转化累加树

[leetcode538](https://leetcode.com/problems/convert-bst-to-greater-tree/description/)

这题还是利用BST的中序遍历特性，我们可以通过中序遍历来有序输出BST中的值，那么我们想反向输出的话，把递归的顺序改成先右节点后左节点即可。

同时我们维护一个变量cur，表示之前累加的值，然后把这个累加的值加在当前节点上即可。

```java
class Solution {
    // 记录累加和
    int cur = 0;

    public TreeNode convertBST(TreeNode root) {
        helper(root);
        return root;
    }

    private void helper(TreeNode root) {
        if (root == null) return;
        helper(root.right);
        // 维护累加和
        root.val += cur;
        // 赋给当前节点
        cur = root.val;
        helper(root.left);
    }
}
```

## 判断BST的合法性

[leetcode98](https://leetcode.com/problems/validate-binary-search-tree/description/)

对于每一个节点，都要判断是否左子树所有的值都小于它，右子树所有的值都大于它。我们通过定义一个辅助函数，传入一个节点应该处于的最小节点和最大节点之间，代码如下

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return helper(root, null, null);
    }

    private boolean helper(TreeNode root, TreeNode minNode, TreeNode maxNode) {
        if (root == null) return true;
        if (minNode != null && root.val <= minNode.val) return false;
        if (maxNode != null && root.val >= maxNode.val) return false;
        return helper(root.left, minNode, root) && helper(root.right, root, maxNode);
    }
}
```

当然，我们也可以通过中序遍历来判断，BST必须在中序遍历的时候是一个有序数组。

```java
class Solution {
    TreeNode pre = null;

    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        if (!isValidBST(root.left)) return false;
        if (pre != null && pre.val >= root.val) return false;
        pre = root;
        if (!isValidBST(root.right)) return false;
        return true;
    }
}
```
