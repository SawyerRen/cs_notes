# 二分图

## 二分图简介

二分图的定义如下：

二分图的顶点集可分割为两个互不相交的子集，图中每条边依附的两个顶点都分属于这两个子集，且两个子集内的顶点不相邻。

听上去比较复杂，让我们玩一个游戏：

**给你一幅「图」，请你用两种颜色将图中的所有顶点着色，且使得任意一条边的两个端点的颜色都不相同，你能做到吗**？

这就是图的「双色问题」，其实这个问题就等同于二分图的判定问题，如果你能够成功地将图染色，那么这幅图就是一幅二分图，反之则不是：

<figure><img src="https://labuladong.github.io/algo/images/algo4/1.jpg" alt=""><figcaption></figcaption></figure>

## 怎么判断二分图

思路很简单，和上面的染色游戏一样，遍历一遍图，在遍历的过程中给图染色，看能不能用两种颜色给所有节点染色，并且相邻节点的颜色都不一样。

图的遍历非常简单

```java
/* 图遍历框架 */
boolean[] visited;
void traverse(Graph graph, int v) {
    // 前序遍历位置，标记节点 v 已访问
    visited[v] = true;
    for (int neighbor : graph.neighbors(v)) {
        if (!visited[neighbor]) {
            // 只遍历没标记过的相邻节点
            traverse(graph, neighbor);
        }
    }
}
```

那么判断二分图的代码框架就是这样的

```java
/* 图遍历框架 */
void traverse(Graph graph, boolean[] visited, int v) {
    visited[v] = true;
    // 遍历节点 v 的所有相邻节点 neighbor
    for (int neighbor : graph.neighbors(v)) {
        if (!visited[neighbor]) {
            // 相邻节点 neighbor 没有被访问过
            // 那么应该给节点 neighbor 涂上和节点 v 不同的颜色
            traverse(graph, visited, neighbor);
        } else {
            // 相邻节点 neighbor 已经被访问过
            // 那么应该比较节点 neighbor 和节点 v 的颜色
            // 若相同，则此图不是二分图
        }
    }
}
```

## 题目举例

### [leetcode785](https://leetcode.com/problems/is-graph-bipartite/description/)

这题要求判断一个图是不是二分图，按照上面的思路，给每个节点染色，如果出现无法染色的情况，说明不是二分图。

用DFS的解法：

```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        // 0表示没有被染色，1表示蓝色，2表示红色
        int[] colors = new int[n];
        // 因为图不一定是联通的，可能存在多个子图
        // 所以要把每个节点都作为起点进行一次遍历
        // 如果发现任何一个子图不是二分图，整幅图都不算二分图
        for (int i = 0; i < n; i++) {
            if (colors[i] == 0 && !helper(graph, i, colors, 1)) return false;
        }
        return true;
    }

    private boolean helper(int[][] graph, int cur, int[] colors, int color) {
        // 如果当前节点已经被染色，判断是不是跟之前的节点颜色不同
        if (colors[cur] != 0) {
            return colors[cur] == color;
        }
        // 给当前的节点染色
        colors[cur] = color;
        for (int next : graph[cur]) {
            // 判断相邻的节点是否是相反的颜色
            if (!helper(graph, next, colors, -color)) return false;
        }
        return true;
    }
}
```

平时DFS需要visited数组来防止重复访问，这里不需要，是因为我们的colors数组起到了防止重复的作用，如果colors\[i]不等于0，就说明这个节点已经访问过了。

同理，BFS的解法：

```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] colors = new int[n];
        for (int i = 0; i < n; i++) {
            if (colors[i] == 0 && !bfs(graph, i, colors)) return false;
        }
        return true;
    }

    private boolean bfs(int[][] graph, int start, int[] colors) {
        Queue<Integer> queue = new LinkedList<>();
        queue.add(start);
        colors[start] = 1;
        while (!queue.isEmpty()) {
            Integer poll = queue.poll();
            for (int next : graph[poll]) {
                if (colors[next] == 0) {
                    queue.add(next);
                    colors[next] = -colors[poll];
                } else {
                    if (colors[next] == colors[poll]) return false;
                }
            }
        }
        return true;
    }
}
```

### [leetcode886](https://leetcode.com/problems/possible-bipartition/description/)

这题看似复杂，其实就是个二分图的问题，把dislikes的关系看作图中的边，我们只需要判断这个图是不是二分图即可。

```java
class Solution {
    public boolean possibleBipartition(int n, int[][] dislikes) {
        // 利用dislikes构建图
        Map<Integer, Set<Integer>> map = new HashMap<>();
        for (int[] dislike : dislikes) {
            map.putIfAbsent(dislike[0], new HashSet<>());
            map.get(dislike[0]).add(dislike[1]);
            map.putIfAbsent(dislike[1], new HashSet<>());
            map.get(dislike[1]).add(dislike[0]);
        }
        // 之后的步骤和上面完全一致
        int[] colors = new int[n + 1];
        for (int i = 1; i < n + 1; i++) {
            if (colors[i] == 0 && !helper(map, colors, i, 1)) return false;
        }
        return true;
    }

    private boolean helper(Map<Integer, Set<Integer>> map, int[] colors, int cur, int color) {
        if (colors[cur] != 0) {
            return colors[cur] == color;
        }
        colors[cur] = color;
        if (map.containsKey(cur)) {
            for (Integer next : map.get(cur)) {
                if (!helper(map, colors, next, -color)) return false;
            }
        }
        return true;
    }
}
```
