# 概述

只要前面有可以走的路，就会一直向前走，直到无路可走才会回头。

无路可走有两种情况：1. 遇到了墙。2. 遇到了已经走过的路。

在无路可走的时候，沿着原路返回，直到回到了还未走过的路的路口，尝试继续走没有走过的路径。

有一些路径没有走到，这是因为找到了出口，程序就停止了。

## 树的深度优先遍历

### 前序遍历

对于任意一棵子树，先输出它的根节点，再递归输出它的左子树的所有节点，最后输出右子树的所有节点。

### 中序遍历

对于任意一棵子树，先递归输出左子树的所有节点，然后输出根节点，最后递归输出右子树的所有节点。

### 后序遍历（重要）

对于任意一棵子树，先递归输出左子树的所有节点，然后递归输出右子树的所有节点，最后输出根节点。后序遍历体现的思想是：必须先得到左右子树的结果，才能得到当前子树的结果，这一点在解决一些问题的过程中非常有用。

## 图的深度优先遍历

TODO

## 练习题

跟树有关的深度遍历，大部分情况下是从下而上，也就是满足三部曲：

1. 从左子树和右子树获取什么。
2. 在当前层处理什么。
3. 向父节点返回什么。

```java
class TreeNode {
  int val;
  TreeNode left;
  TreeNode right;
  public TreeNode(int val) {
    this.val = val;
  }
  public TreeNode(int val, TreeNode left, TreeNode right) {
    this.val = val;
    this.left = left;
    this.right = right;
  }
}
```

**求二叉树的最大高度**

```java
public int maxDepth(TreeNode root) {
  if (root == null) return 0;
  int left = maxDepth(root.left);
  int right = maxDepth(root.right);
  int height = Math.abs(left, right) + 1;
  return height;
}
```

**求二叉树的最小高度**

此题有个小坑，它是指从根节点到最近的叶子节点的最少的节点数。如下图：

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221222150503589.png" alt="image-20221222150503589" style="zoom: 25%;" />

这棵树的最小高度，不是1，因为根节点无左子树，所以它的最小深度，只能是从根节点到右子树叶子节点的最小深度。

```java
public int minDepth(TreeNode root) {
  if (root == null) return 0;
  int left = minDepth(root.left);
  int right = minDepth(root.right);
  if (root.left == null) {
    return right + 1;
  } else if (root.right == null) {
    return left + 1;
  } else {
    return Math.min(left, right) + 1;
  }
}
```

**路径总和**

