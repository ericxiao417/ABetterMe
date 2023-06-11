# Heap && BFS

## 堆知识回顾

堆，heap，也叫做优先队列，priority queue。它的用途是：维护一个变化的数据集的最 大/小 值。

堆的实现是通过构造二叉堆，binary heap，具有以下性质：1. 堆是一棵完全二叉树。2. 任意节点 小于/大于 它的所有后裔。根节点最小的堆叫作最小堆，min heap，根节点最大的堆叫作最大堆，map heap。

<img src="BFS.assets/nDwK3im1ku.png" alt="数据结构之索引堆(IndexHeap) | 计算机科学论坛" style="zoom:40%;" />

向堆中插入一个新元素，时间复杂度是logn。

将新元素提升使其符合堆的性质，时间复杂度logn。

获取当前堆顶元素的值，时间复杂度1。

删除堆顶元素的值，时间复杂度logn。

heapify：使一个unsorted array变成一个堆，时间复杂度n。

## 典型例题

Find K smallest elements in an unsorted array. The elements should be in ascending order. K is >= 0 and smaller than or equal to array length.

[^分析]:当需要K个最小的时候，用大顶堆。Java里面默认的Integer的堆是小顶堆，需要大顶堆可以直接调用Collections.reverseOrder()。此处还要注意如果是其他数据类型，则需要写堆的实现。初始化了堆以后，会自动heapify，复杂度为n。此题时间复杂度是n + klogn。当堆poll()的时候，size是会变化的，需要注意此处。

```java
public int[] kSmallest(int[] array, int k) {
  if (array == null || array.length == 0 || k == 0) return new int[0];
  Queue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
  for (int i = 0; i < k; i++) {
    if (maxHeap.size() < k) {
      maxHeap.offer(array[i]);
    } else {
      if (maxHeap.peek() > array[i]) {
        maxHeap.poll();
        maxHeap.offer(array[i]);
      }
    }
  }
  int[] ret = new int[k];
  int size = maxHeap.size();
  for (int i = k - 1; i >= 0; i--) {
    ret[i] = maxHeap.poll();
  }
  return ret;
}
```

[^DONE]:其他数据类型的heap comparator的构造。下面这个例子是最小堆。-1 表示权重越大，在二叉堆的结构里就越靠近top。

```java
class MyComparator implements Comparator<Object> {
  @Override
  public int compare(Object o1, Object o2) {
    if (o1.value == o2.value) return 0;
    return o1.value < o2.value ? -1 : 1; 
  }
}
```

#### TOP-K解题思路

假设数据量为n，我们需要从n中找到top-k的元素。并且针对不断添加进来的数据，都要获取最新的top-k元素。

1. 先从n中取前k个元素，组成一个最小堆，此时堆顶的元素是最小的。从k+1个元素开始，和堆顶元素进行比较，如果比堆顶元素小，就不做处理，继续下一个。如果比堆顶元素大，那么把堆顶元素删除，并插入此元素。等到n个元素遍历完，那么最小堆中的k个元素就是top-k的数据了。
2. 时间主要耗在一开始构建K个元素的原始堆上了，即logk。还有删除堆顶元素和插入元素都是logk，所以总时间是nlogk。

## BFS相关知识

BFS，广度优先搜索。

```java
class GraphNode { // 图的节点的数据结构
  int value;
  string name;
  List<GraphNode> neighbors;
}
```

解题框架：

1. Maintain a FIFO queue, put all traversed nodes that have not been expanded.
2. Expand a node.
3. Generate a neighbor node: reach out to its neighbor nodes.
4. Do a loop until the queue is empty then termination.
5. Each node should be expanded and generated only once.

## 相关例题

### Traversal binary tree layer by layer

Get the list of keys in a give binary tree layer by layer from left to right.

```java
public List<List<Integer>> layerByLayer(TreeNode root) {
  List<List<Integer>> list = new ArrayList<>();
  if (root == null) return list;
  Queue<TreeNode> queue = new LinkedList<>();
  queue.offer(root);
  while (!queue.isEmpty()) {
    int size = queue.size();
    List<Integer> tmp = new ArrayList<>();
    for (int i = 0; i < size; i++) {
      TreeNode node = queue.poll();
      tmp.add(node.value);
      if (node.left != null) {
        queue.offer(node.left);
      }
      if (node.right != null) {
        queue.offer(node.right);
      }
    }
    list.add(tmp);
  }
  return list;
}
```

### Check if a binary tree is completed

[^分析]:采用bfs的形式遍历二叉树，根据完全二叉树的特点，只可能最后一层有null，且在最右边。若之前有null或者左边有null，均不是完全二叉树。

```java
public boolean isCompleted(TreeNode root) {
  if (root == null) return true;
  Queue<TreeNode> queue = new LinkedList<>();
  queue.offer(root);
  boolean flag = true; // true means no empty nodes before
  while (!queue.isEmpty()) {
    TreeNode node = queue.poll();
    if (node.left != null) {
      if (!flag) {
        return false;
      } else {
        queue.offer(node.left);
      }
    } else {
      flag = false;
    }
    if (node.right != null) {
      if (!flag) {
        return false;
      } else {
        queue.offer(node.right);
      }
    } else {
      flag = false;
    }
  }
  return true;
}
```

### Kth Smallest Number in Sorted Matrix

Given a N * M matrix. For each row and each column, elements are both sorted in ascending order. Find the Kth smallest element. k > 0 and k <= N * M.

```java
public class Solution {
  public int kthSmallest(int[][] matrix, int k) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return 0;
    Queue<Cell> minHeap = new PriorityQueue<>(new MyComparator());
    minHeap.offer(new Cell(0, 0, matrix[0][0]));
    int N = matrix.length, M = matrix[0].length;
    boolean[][] used = new boolean[N][M];
    int i = 0;
    while (!minHeap.isEmpty() && i < k - 1) {
      Cell c = minHeap.poll();
      used[c.row][c.col] = true;
      if (c.row + 1 < N - 1 && !used[c.row + 1][c.col]) {
        used[c.row + 1][c.col] = true;
        minHeap.offer(new Cell(c.row + 1, c.col, matrix[c.row + 1][c.col]));
      }
      if (c.col + 1 < M - 1 && !used[c.row][c.col + 1]) {
        used[c.row][c.col + 1] = true;
        minHeap.offer(new Cell(c.row, c.col + 1, matrix[c.row][c.col + 1]));
      }
      i++;
    }
  }
}

class Cell {
  int row;
  int col;
  int value;
  public Cell(int row, int col, int value) {
    this.row = row;
    this.col = col;
    this.value = value;
  }
}

class MyComparator implements Comparator<Cell> {
  @Override
  public int compare(Cell c1, Cell c2) {
    if (c1.value == c2.value) return 0;
    return c1.value < c2.value ? -1 : 1;
  }
}
```



