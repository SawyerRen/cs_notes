# 遍历算法

图其实本质上类似一个多叉树，或者说树本就是一个有向无环图。适用于树的DFS和BFS，都可以用在图上面。

那么多叉树是怎么进行DFS遍历的呢？

```java
/* 多叉树遍历框架 */
void traverse(TreeNode root) {
    if (root == null) return;
    // 前序位置
    for (TreeNode child : root.children) {
        traverse(child);
    }
    // 后序位置
}
```

图和多叉树的区别是图是可能有环的，为了防止重复遍历，需要一个辅助结构来保存已经遍历过的节点。

```java
// 记录被遍历过的节点
boolean[] visited;

/* 图遍历框架 */
void traverse(Graph graph, int s) {
    if (visited[s]) return;
    // 经过节点 s，标记为已遍历
    visited[s] = true;
    // 操作
    for (int neighbor : graph.neighbors(s)) {
        traverse(graph, neighbor);
    }
    // 操作
}
```

## 例题

### 所有可能的路径

[leetcode797](https://leetcode.com/problems/all-paths-from-source-to-target/description/)

题目中的图都是有向无环图，我们就不需要visited数组来保存已经访问过的节点。解法非常简单，以0为起点遍历图，记录遍历过的路径，当遍历到终点的时候记录即可。

```java
class Solution {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        // 记录所有路径
        List<List<Integer>> res = new ArrayList<>();
        helper(res, new ArrayList<Integer>(), graph, 0);
        return res;
    }

    private void helper(List<List<Integer>> res, ArrayList<Integer> list, int[][] graph, int cur) {
        // 添加节点 s 到路径
        list.add(cur);
        // 到达终点
        if (cur == graph.length - 1) {
            res.add(new ArrayList<>(list));
        } else {
        // 递归每个相邻节点
            for (int next : graph[cur]) {
                helper(res, list, graph, next);
            }
        }
        // 从路径移出节点
        list.remove(list.size() - 1);
    }
}
```
