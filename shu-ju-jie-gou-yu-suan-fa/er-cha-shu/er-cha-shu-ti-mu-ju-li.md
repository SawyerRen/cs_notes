# 二叉树题目举例

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

## [通过前序和中序遍历结果构造二叉树](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/) <a href="#tong-guo-qian-xu-he-zhong-xu-bian-li-jie-guo-gou-zao-er-cha-shu" id="tong-guo-qian-xu-he-zhong-xu-bian-li-jie-guo-gou-zao-er-cha-shu"></a>

**这种构造二叉树的题目，需要先确定根节点的值，先创建根节点，然后递归构造左右子树。**

让我们首先考虑一下前序遍历和中序遍历的差异

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

找到根节点很容易，前序遍历的第一个值就是根节点，关键在于怎么把前序和中序数组分成两边，构造左右子树。

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

当我们遍历前序数组的时候，下一个节点可能是当前节点的左子节点、右子节点或者父节点的右子节点，我们通过rootVal分割了中序数组为根节点的左子树和右子树，我们可以通过这个来判断下一个节点和当前节点的关系。

同时，如果我们要寻找rootVal在中序数组中的index，我们可以用HashMap来提高搜索的效率。

代码如下

```java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();
    // 遍历谦虚数组的坐标
    int preIndex = 0;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        // 构造中序数组的值和坐标的映射
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return helper(preorder, 0, preorder.length - 1);
    }
    
    // left和right表示在中序数组中的开始和终止索引
    private TreeNode helper(int[] preorder, int left, int right) {
        if (left > right) return null;
        // 拿到rootVal
        int rootVal = preorder[preIndex++];
        // 中序数组中rootval的坐标
        int inorderIndex = map.get(rootVal);
        // 创建根节点
        TreeNode root = new TreeNode(rootVal);
        // 构建左子树，中序数组保留左半部分
        root.left = helper(preorder, left, inorderIndex - 1);
        // 构建右子树，中序数组保留右半部分
        root.right = helper(preorder, inorderIndex + 1, right);
        return root;
    }
}
```

## [通过后序和中序遍历结果构造二叉树](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/description/) <a href="#tong-guo-hou-xu-he-zhong-xu-bian-li-jie-guo-gou-zao-er-cha-shu" id="tong-guo-hou-xu-he-zhong-xu-bian-li-jie-guo-gou-zao-er-cha-shu"></a>

这题要我们用后序和中序数组来构造二叉树，和上一题的思路类似。\


<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

需要注意的是，后序遍历的最后一个值是根节点。我们可以从后序数组的最后开始向前遍历，先遍历到的是右子树，后面是左子树，这和前序遍历是相反的。

代码如下

```java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();
    int postIndex = 0;

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        postIndex = postorder.length - 1;
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return helper(postorder, 0, postorder.length - 1);
    }

    private TreeNode helper(int[] postorder, int left, int right) {
        if (left > right) return null;
        int rootVal = postorder[postIndex--];
        int rootIndex = map.get(rootVal);
        TreeNode root = new TreeNode(rootVal);
        // 先右后左
        root.right = helper(postorder, rootIndex + 1, right);
        root.left = helper(postorder, left, rootIndex - 1);
        return root;
    }
}
```

## [二叉树的序列化和反序列化](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/description/)

说是序列化，其实就是遍历二叉树把数据都存到一个一维的结构中。需要思考的是，通过遍历之后序列化的数据，怎么才可以唯一确定一个二叉树的结构？

举例来说，如果我给你这样一个不包含空指针的前序遍历结果 `[1,2,3,4,5]`，那么有很多二叉树都可以满足这个遍历的结果。所以我们还需要给定空指针的信息。

还需要注意的是，只有前序和后序遍历，在包含空指针的信息的情况下，才能还原成唯一的二叉树。这边我们选择用前序遍历来解。在传统的前序遍历中，我们使用List来保存遍历的结果，序列化只要用字符串来保存结果即可。

序列化的解法如下

```java
// Encodes a tree to a single string.
public String serialize(TreeNode root) {
    StringBuilder builder = new StringBuilder();
    helper(root, builder);
    return builder.toString();
}

private void helper(TreeNode root, StringBuilder builder) {
    // 保留空指针信息
    if (root == null) {
        builder.append("null,");
        return;
    }
    // append(",")是为了后面反序列化的时候分割不同的节点的值
    builder.append(root.val).append(",");
    helper(root.left, builder);
    helper(root.right, builder);
}
```

现在考虑反序列化函数，我们可以把字符串转化成一个列表，这个列表就是前序遍历的结果。同样的，我们用前序遍历的方式，每次取出列表的第一个值，就是我们前序遍历构造二叉树的下一个节点。

```java
// Decodes your encoded data to tree.
public TreeNode deserialize(String data) {
    // 转化成list
    Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
    return helper1(queue);
}

private TreeNode helper1(Queue<String> queue) {
    if (queue.isEmpty()) return null;
    // 取出第一个值
    String poll = queue.poll();
    // 如果是null，说明是空节点
    if (poll.equals("null")) return null;
    // 构建根节点
    TreeNode root = new TreeNode(Integer.parseInt(poll));
    // 前序遍历后面的节点
    root.left = helper1(queue);
    root.right = helper1(queue);
    return root;
}
```
