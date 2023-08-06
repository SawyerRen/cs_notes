# 遍历和递归思维举例

首先，回顾一下二叉树的解题思维模式：

> **1、是否可以通过遍历一遍二叉树得到答案**？如果可以，用一个 `traverse` 函数配合外部变量来实现，这叫「遍历」的思维模式。
>
> **2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案**？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值，这叫「分解问题」的思维模式。
>
> 无论使用哪种思维模式，你都需要思考：
>
> **如果单独抽出一个二叉树节点，它需要做什么事情？需要在什么时候（前/中/后序位置）做**？其他的节点不用你操心，递归函数会帮你在所有节点上执行相同的操作。

## [翻转二叉树](https://leetcode.com/problems/invert-binary-tree/description/)

这题输入一个二叉树根节点，把整个树镜像翻转。只要我们把每一个节点的左右子节点进行交换，就可以得到翻转后的二叉树。

### **1. 能不能通过遍历来解决？**

可以的，通过一个遍历函数，遍历每个节点，把每个节点的左右子节点交换。

单独抽出来一个节点，需要做什么？交换左右子节点。

需要在什么时候做？都可以，最简单好理解的应该是前序。

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        // 交换当前节点的左右子节点
        TreeNode left = root.left;
        root.left = root.right;
        root.right = left;
        // 遍历左右节点
        invertTree(root.left);
        invertTree(root.right);
        return root;
    }
}
```

### **2. 能不能通过分解问题来解决？**

我们可以先对当前节点的左右子节点进行函数操作来翻转，最后把当前节点的左右子树交换，这样就可以完成对当前子节点的的翻转。

```java
TreeNode invertTree(TreeNode root) {
    if (root == null) {
        return null;
    }
    // 利用函数定义，先翻转左右子树
    TreeNode left = invertTree(root.left);
    TreeNode right = invertTree(root.right);

    // 然后交换左右子节点
    root.left = right;
    root.right = left;

    // 和定义逻辑自恰：以 root 为根的这棵二叉树已经被翻转，返回 root
    return root;
}
```

对于分解问题的解法，最重要的就是如何定义递归函数，如何用这个函数来解释你的代码。

## [二叉树展开为链表](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/description/) <a href="#di-er-ti-tian-chong-jie-dian-de-you-ce-zhi-zhen" id="di-er-ti-tian-chong-jie-dian-de-you-ce-zhi-zhen"></a>

### **1. 能不能通过遍历来解决？**

乍一看是可以的，对整棵树进行遍历，一遍遍历一边构造出一条链表即可。

```java
// 虚拟头节点，dummy.right 就是结果
TreeNode dummy = new TreeNode(-1);
// 用来构建链表的指针
TreeNode p = dummy;

void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // 前序位置
    p.right = new TreeNode(root.val);
    p = p.right;

    traverse(root.left);
    traverse(root.right);
}
```

但是注意，这题希望我们在原地把二叉树展开，而不是新建一个链表，简单的二叉树遍历就不太方便了。

### **2. 能不能通过分解问题来解决？**

对于一个节点 `x`，可以执行以下流程：

1、先利用 `flatten(x.left)` 和 `flatten(x.right)` 将 `x` 的左右子树拉平。

2、将 `x` 的右子树接到左子树下方，然后将整个左子树作为右子树。

这样，以 `x` 为根的整棵二叉树就被拉平了，恰好完成了 `flatten(x)` 的定义。

```java
class Solution {
    public void flatten(TreeNode root) {
        if (root == null) return;
        // 利用定义，把左右子树拉平
        flatten(root.left);
        flatten(root.right);
        /**** 后序遍历位置 ****/
        //将左子树作为右子树
        TreeNode right = root.right;
        root.right = root.left;
        root.left = null;
        // 将原先的右子树接到当前右子树的末端
        while (root.right != null) {
            root = root.right;
        }
        root.right = right;
    }
}
```

递归是怎么完成展开的工作的？这个其实是很难解释的，但是只要我们让每个节点都去做它该做的事情，然后通过定义递归函数，自然就解决了。
