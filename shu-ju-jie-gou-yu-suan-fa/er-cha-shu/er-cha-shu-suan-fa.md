# 二叉树算法

先在开头总结一下，二叉树解题的思维模式分两类：

**1、是否可以通过遍历一遍二叉树得到答案**？如果可以，用一个 `traverse` 函数配合外部变量来实现，这叫「遍历」的思维模式。

**2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案**？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值，这叫「分解问题」的思维模式。

无论使用哪种思维模式，你都需要思考：

**如果单独抽出一个二叉树节点，它需要做什么事情？需要在什么时候（前/中/后序位置）做**？其他的节点不用你操心，递归函数会帮你在所有节点上执行相同的操作。

## 理解前中后序

首先给出二叉树遍历的框架

```java
void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // 前序位置
    traverse(root.left);
    // 中序位置
    traverse(root.right);
    // 后序位置
}
```

这个遍历和遍历数组或者链表本质上是没有区别的

```java
/* 迭代遍历数组 */
void traverse(int[] arr) {
    for (int i = 0; i < arr.length; i++) {

    }
}

/* 递归遍历数组 */
void traverse(int[] arr, int i) {
    if (i == arr.length) {
        return;
    }
    // 前序位置
    traverse(arr, i + 1);
    // 后序位置
}

/* 迭代遍历单链表 */
void traverse(ListNode head) {
    for (ListNode p = head; p != null; p = p.next) {

    }
}

/* 递归遍历单链表 */
void traverse(ListNode head) {
    if (head == null) {
        return;
    }
    // 前序位置
    traverse(head.next);
    // 后序位置
}

```

二叉树其实就是个二叉链表，因为没办法简单地写成迭代形式，所以一般用递归函数来遍历。我们也可以发现，**只要是用递归函数来遍历，都可以有前序和后序，本质上就是在递归之前操作和递归之后操作**。所谓前序遍历，就是在刚到达一个节点的时候，就进行操作，再递归后续节点；而后续遍历，就是先递归后续节点，再进行操作。代码写在不同的位置，执行的顺序也就不一样。

而说到二叉树，其实也是一样的，无非就是多了个中序遍历。**前中后序是遍历二叉树过程中处理每一个节点的三个特殊时间点，**前序位置的代码在刚刚进入一个二叉树节点的时候执行；后序位置的代码在将要离开一个二叉树节点的时候执行；中序位置的代码在一个二叉树节点左子树都遍历完，即将开始遍历右子树的时候执行。

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

说了这么多，总结起来就是：

**二叉树的所有问题，就是让你在前中后序位置注入巧妙的代码逻辑，去达到自己的目的，你只需要单独思考每一个节点应该做什么，其他的不用你管，抛给二叉树遍历框架，递归会在所有节点上做相同的操作**。

## 两种解题思路

**二叉树题目的递归解法可以分两类思路，第一类是遍历一遍二叉树得出答案，第二类是通过分解问题计算出答案。**

我们先来看一道题目，[leetcode104](https://leetcode.com/problems/maximum-depth-of-binary-tree/description/)二叉树的最大深度，也就是根节点到最远的叶子节点的节点数，这道题可以有两种思路。

可以遍历一遍二叉树，用一个变量记录所在节点的深度，取其中的最大值。

```java
class Solution {
    // 最大深度和当前深度
    int res = 0, depth = 0;

    public int maxDepth(TreeNode root) {
        helper(root);
        return res;
    }
    
    // 遍历二叉树
    private void helper(TreeNode root) {
        if (root == null) return;
        depth++;
        res = Math.max(res, depth);
        helper(root.left);
        helper(root.right);
        depth--;
    }
}
```

注意depth的变化，depth++是在前序位置，表示我们在进入节点的时候，把当前节点的深度+1，而depth--是在后序位置，表示我们在离开节点的时候，把深度-1。

第二种思路则是，二叉树的最大深度可以由它的子树来得到，这就是**分解问题计算答案。**

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        // 计算左右子树的最大深度
        int left = maxDepth(root.left);
        int right = maxDepth(root.right);
        // 取其中的最大深度+1，就是当前的最大深度
        return Math.max(left, right) + 1;
    }
}
```

通过这个简单的例子，我们可以得到二叉树题目的通用思考过程：

1. 是否可以通过遍历一遍二叉树得到答案？
2. 是否可以定义一个递归函数，通过子树的答案推导出原问题的答案？
3. 每个节点需要做什么？要在前中后哪个位置操作？

## 后序位置的特殊之处

前序位置的代码执行是自顶向下的，而后序位置的代码执行是自底向上的。

这也意味着，**前序位置的代码只能从函数参数中获取父节点传递来的数据，而后序位置的代码不仅可以获取参数数据，还可以获取到子树通过函数返回值传递回来的数据**。

当我们遇到题目和子树有关，那么大概率需要设置相应的返回值，并且用后序位置来解决。

比方说[leetcode54](https://leetcode.com/problems/diameter-of-binary-tree/description/)3，这题要我们计算二叉树的最长直径长度，这题的关键在于，每一个二叉树的直径长度，就是一个节点的左右子树的最大深度之和。当然我们可以遍历二叉树，算每个子树的直径长度，找到其中的最大值，但是这样的效率很低。这个时候，我们就可以用后序位置来解决。

```java
class Solution {
    // 最大直径
    int res = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        helper(root);
        return res;
    }

    private int helper(TreeNode root) {
        if (root == null) return 0;
        int left = helper(root.left);
        int right = helper(root.right);
        // 后序位置计算最大直径
        res = Math.max(res, left + right);
        // 返回左右子树的最大深度
        return Math.max(left, right) + 1;
    }
}
```

这样时间复杂度就只有O(n)了。遇到类似的题目，先给函数设置返回值，然后在后续位置操作。类似的题目还有[leetcode124](https://leetcode.com/problems/binary-tree-maximum-path-sum/description/)

```java
class Solution {
    int res = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        helper(root);
        return res;
    }

    private int helper(TreeNode root) {
        if (root == null) return 0;
        // 计算左右子树的最大路径和，如果小于0，则使用0值
        int left = Math.max(0, helper(root.left));
        int right = Math.max(0, helper(root.right));
        // 比较得到当前最大值
        int curMax = root.val + left + right;
        res = Math.max(res, curMax);
        // 返回当前节点的值+左右节点路径和的较大值
        return Math.max(left, right) + root.val;
    }
}
```

## 层次遍历

相较于前中后序遍历，层次遍历类似于BFS。

```java
// 输入一棵二叉树的根节点，层序遍历这棵二叉树
void levelTraverse(TreeNode root) {
    if (root == null) return;
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);

    // 从上到下遍历二叉树的每一层
    while (!q.isEmpty()) {
        int sz = q.size();
        // 从左到右遍历每一层的每个节点
        for (int i = 0; i < sz; i++) {
            TreeNode cur = q.poll();
            // 将下一层节点放入队列
            if (cur.left != null) {
                q.offer(cur.left);
            }
            if (cur.right != null) {
                q.offer(cur.right);
            }
        }
    }
}
```

这里面 while 循环和 for 循环分管从上到下和从左到右的遍历：

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>
