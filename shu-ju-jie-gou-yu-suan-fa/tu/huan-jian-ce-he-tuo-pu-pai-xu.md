# 环检测和拓扑排序

拓朴排序和有向图的环检测是比较常见的图算法，可以先看一下这个[链接](https://www.bilibili.com/video/BV1uf4y1U7DX/?vd\_source=2cdcfec5d7a551bef0c23320aea32803)的解释。而本质上，有向图的环检测就可以通过拓扑排序来检查，所以这里只介绍拓扑排序。

对于拓扑排序而言，DFS和BFS都是可以解决的，但是BFS相对而言更好理解，并且写法上更加简洁。

## 拓扑排序

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

拓扑排序如图所示，简单地说，就是把一个图拉平，在这个拉平的图中，所有箭头的方向都是一致的。如果一个图中存在环，那么这个图是肯定不能进行拓扑排序的，因为不能保证所有箭头的方向都是一致的。

那么如何用BFS来实现拓扑排序呢？具体分为以下步骤：

1. 利用HashMap等结构创建图，保存图中的依赖关系
2. 构建一个数组来记录每个节点的入度，即多少个节点指向它
3. 将入度为0的节点存入BFS的队列中
4. 进行BFS循环，不断弹出节点，减少相邻节点的入度，并且将入度为0的节点存入队列
5. 如果所有节点都被遍历过，说明不存在环，否则说明存在环

下图解释这个算法，节点中的数字代表该节点的入度：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/5.jpeg)

队列进行初始化后，入度为 0 的节点首先被加入队列：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/6.jpeg)

开始执行 BFS 循环，从队列中弹出一个节点，减少相邻节点的入度，同时将新产生的入度为 0 的节点加入队列：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/7.jpeg)

继续从队列弹出节点，并减少相邻节点的入度，这一次没有新产生的入度为 0 的节点：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/8.jpeg)

继续从队列弹出节点，并减少相邻节点的入度，同时将新产生的入度为 0 的节点加入队列：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/9.jpeg)

继续弹出节点，直到队列为空：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/10.jpeg)

这时候，所有节点都被遍历过一遍，也就说明图中不存在环。

反过来说，如果按照上述逻辑执行 BFS 算法，存在节点没有被遍历，则说明成环。

比如下面这种情况，队列中最初只有一个入度为 0 的节点：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/11.jpeg)

当弹出这个节点并减小相邻节点的入度之后队列为空，但并没有产生新的入度为 0 的节点加入队列，所以 BFS 算法终止：

![](https://labuladong.github.io/algo/images/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/12.jpeg)

如果存在节点没有被遍历，那么说明图中存在环。

## 题目举例

leetcode2\\
