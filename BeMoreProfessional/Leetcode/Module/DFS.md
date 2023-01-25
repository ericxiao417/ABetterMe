# 概述

只要前面有可以走的路，就会一直向前走，直到无路可走才会回头。

无路可走有两种情况：1. 遇到了墙。2. 遇到了已经走过的路。

在无路可走的时候，沿着原路返回，直到回到了还未走过路的路口，尝试继续走没有走过的路径。

有一些路径没有走到，这是因为找到了出口，程序就停止了。

**个人理解：**

DFS就是递归函数，从起点到当前层，再到下一层，直到到达边界，然后返回。首先要定义出，DFS的作用是什么，其次，要考虑出边界条件，即在什么情况下需要 return 。最后，就是在当前层要做哪些事情，其实也是每一层要做哪些事情。

另外，DFS不一定就是无返回，即void，也可以是int，boolean等等。

# 树的深度优先遍历

## 前序遍历

对于任意一棵子树，先输出它的根节点，再递归输出它的左子树的所有节点，最后输出右子树的所有节点。

## 中序遍历

对于任意一棵子树，先递归输出左子树的所有节点，然后输出根节点，最后递归输出右子树的所有节点。

## 后序遍历（重要）

对于任意一棵子树，先递归输出左子树的所有节点，然后递归输出右子树的所有节点，最后输出根节点。后序遍历体现的思想是：必须先得到左右子树的结果，才能得到当前子树的结果，这一点在解决一些问题的过程中非常有用。

# 图的深度优先遍历

TODO

# 练习题

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

## 求二叉树的最大高度

```java
public int maxDepth(TreeNode root) {
  if (root == null) return 0;
  int left = maxDepth(root.left);
  int right = maxDepth(root.right);
  int height = Math.abs(left, right) + 1;
  return height;
}
```

## 求二叉树的最小高度

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

## 路径总和

给定一个二叉树的根节点root和一个表示目标和的整数target，判断该树中是否存在从根节点到叶子节点的路径，路径上的所有节点的和等于target。如果有，返回true，否则返回false。

```java
public boolean hasPathSum(TreeNode root, int target) {
  if (root == null) return false;
  if (root.left == null && root.right == null && root.val == target) return true;
  boolean left = hasPathSum(root.left, target - root.val);
  boolean right = hasPathSum(root.right, target - root.val);
  return left || right;
}
```

## 翻转二叉树

给一棵二叉树的根节点root，翻转这棵二叉树，并返回根节点。

```java
public TreeNode invertTree(TreeNode root) {
  if (root == null) return root;
  //从上往下
  TreeNode tmp = root.left;
  root.left = root.right;
  root.right = tmp;
  invertTree(root.left);
  invertTree(root.right);
  return root;
}
```

## 相同的树

判断两棵二叉树是否相同。

```java
public boolean isSame(TreeNode p, TreeNode q) {
  if (p == null && q == null) return true;
  if (p == null || q == null) return false;
  if (p.val != q.val) return false;
  if (p.val == q.val) {
    boolean left = isSame(p.left, q.left);
    boolean right = isSame(p.right, q.right);
    return left && right;
  }
  return false;
}
```

## 对称二叉树

给定一棵二叉树，判断是否为轴对称。

```java
public boolean isSymmetric(TreeNode root) {
  if (root == null) return true;
  return helper(root.left, root.right);
}

private boolean helper(TreeNode p, TreeNode q) {
  if (p == null && q == null) return true;
  if (p == null || q == null) return false;
  if (p.val != q.val) return false;
  boolean left = helper(p.left, q.right);
  boolean right = helper(p.right, q.left);
  return left && right;
}
```

## 从根到叶子节点的数字之和

给一个二叉树的根节点root，树中每个节点都存放一个0-9的数字。计算从根节点到叶节点的数字之和。

比如：从根节点到叶子节点的路径是 1-> 2 -> 3，那么表示数字123。

```java
public int sumNumers(TreeNode root) {
  if (root == null) return 0;
  List<Integer> ret = new ArrayList<>();
  int[] num = new int[1];
  num[0] = 0;
  dfs(root, num[0], ret);
  int sum = 0;
  for (int r : ret) {
    sum += r;
  }
  return sum;
}

private void dfs(TreeNode root, int sum, List<Integer> ret) {
  if (root != null) {
    sum = sum * 10 + root.val;
    if (root.left == null && root.right == null) {
      ret.add(sum);
      return;
    }
  }
  if (root.left != null) {
    dfs(root.left, sum, ret);
  }
  if (root.right != null) {
    dfs(root.right, sum, ret);
  }
}
```

## 从前序与中序遍历中构造二叉树

给定两个整数数组preorder和inorder，请构造二叉树。

**思路：** 

1. 前序遍历的第一个点为根节点。
2. 根节点是中序遍历的分割点，左子树i个节点，右子树剩余的节点。
3. 前序左子树：1 - i， 右子树：i + 1  - N
4. 中序左子树：0 - i - 1，右子树： i + 1 - N

```java
public TreeNode buildTree(int[] preorder, int[] inorder) {
  if (preorder.length == null || inorder.length == 0) return null;
  TreeNode root = new TreeNode(preorder[0]);
  for (int i = 0; i < inorder.length; i++) {
    if (preorder[0] == inorder[i]) {
      // copyOfRange(): 前闭后开
      int[] pre_left = Arrays.copyOfRange(preorder, 1, i + 1);
      int[] pre_right = Arrays.copyOfRange(preorder, i + 1, preorder.length);
      int[] in_left = Arrays.copyOfRange(inorder, 0, i);
      // 要从i+1开始，因为i是根节点
      int[] in_right = Arrays.copyOfRange(inorder, i + 1, inorder.length);
      root.left = buildTree(pre_left, in_left);
      root.right = buildTree(pre_right, in_right);
      return root;
    }
    return root;
  }
}
```

## 从中序与后序遍历中构造二叉树

给定一棵二叉树的中序遍历数组和后序遍历数组，还原此二叉树。

**分析：**

1. 在后序遍历中，最后一个元素为树的根节点。
2. 在中序遍历中，根节点左边的树为左子树，根节点右边的树为右子树。

```java
public TreeNode buildTree(int[] inorder, int[] postorder) {
        if (inorder.length == 0 || postorder.length == 0) return null;
        int val = postorder[postorder.length - 1];
        TreeNode root = new TreeNode(val);
        for (int i = 0; i < inorder.length; i++) {
            if (val == inorder[i]) {
                int[] in_left = Arrays.copyOfRange(inorder, 0, i);
                int[] in_right = Arrays.copyOfRange(inorder, i + 1, inorder.length);
                int[] post_left = Arrays.copyOfRange(postorder, 0, i);
                int[] post_right = Arrays.copyOfRange(postorder, i, postorder.length - 1);
                root.left = buildTree(in_left, post_left);
                root.right = buildTree(in_right, post_right);
                return root;
            }
        }
        return root;
    }
```

## 前序遍历构造二叉搜索树

给定一个整数数组，它表示BST的先序遍历，构造树并返回根节点。

**思路：**

1. 根据前序遍历的特点，根节点在第一位，后面连接着左子树和右子树。
2. 根据BST的特点，左子树都比根节点的值小，右子树都比根节点的值大。

```java
public TreeNode buildTree(int[] inorder, int[] postorder) {
        if (inorder.length == 0 || postorder.length == 0) return null;
        int val = postorder[postorder.length - 1];
        TreeNode root = new TreeNode(val);
        for (int i = 0; i < inorder.length; i++) {
            if (val == inorder[i]) {
                int[] in_left = Arrays.copyOfRange(inorder, 0, i);
                int[] in_right = Arrays.copyOfRange(inorder, i + 1, inorder.length);
                int[] post_left = Arrays.copyOfRange(postorder, 0, i);
                int[] post_right = Arrays.copyOfRange(postorder, i, postorder.length - 1);
                root.left = buildTree(in_left, post_left);
                root.right = buildTree(in_right, post_right);
                return root;
            }
        }
        return root;
    }
```

## 从先序遍历还原二叉树

从二叉树的根节点开始进行深度优先搜索。

在遍历每个节点时，输出D条短线，其中D是该节点的深度，然后输出该节点的值。如果节点的深度为0，则其直接子节点的深度为D+1， 根节点的深度为0。如果节点只有一个子节点，那么保证该节点为左子节点。给出遍历输出S，返回root。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20230115131753533.png" alt="image-20230115131753533" style="zoom:50%;" />

# 栈stack

在深度优先遍历中，需要将当前遍历的节点的相邻节点暂时保存起来，以便在回退时可以继续访问他们。遍历到节点的顺序呈现出后进先出的特点，因此可以用栈来保存。

再者，深度优先遍历明显有递归结构，支持递归的数据结构也是栈。因此，实现深度优先遍历有以下两种方法：

1. 编写递归方法。
2. 编写栈，通过迭代方式来实现。

# 二叉树的三种遍历方式的非递归实现

对于二叉树的遍历，每个节点有两个处理方式：

1. 输出该节点。
2. 递归处理该节点。

我们可以在节点存入栈的时候增加一个**指令信息**，ADD表示把该节点添加到结果集合中，GO表示递归处理该节点。在栈顶元素弹出的时候，读取指令信息，如果是GO，就将当前节点的左右孩子节点按照【前序遍历】（根->左->右）/【中序遍历】/【后序遍历】的倒序压入栈中。倒序是因为栈处理元素的顺序是先进后出，弹栈的时候才能会按照我们想要的顺序输出结果集。

## 前序遍历

非递归方式，即模拟栈：

```java
public List<Integer> preorder(TreeNode root) {
  List<Integer> list = new ArrayList<>();
  if (root == null) {
    return list;
  }
  Deque<TreeNode> stack = new LinkedList<>();
  stack.offerFirst(root);
  while (!stack.isEmpty()) {
    TreeNode node = stack.pollFirst();
    if (node.right != null) {
      stack.offerFirst(node.right);
    }
    if (node.left != null) {
      stack.offerFirst(node.left);
    }
    list.add(node.val);
  }
  return list;
}
```

## 中序遍历

==颜色标记法：==

**核心思想：**

1. 使用颜色标记节点的状态，新节点为白色，已访问的节点为黑色。
2. 如果遇到白色节点，则将其标记为黑色，然后将其右子节点、自身、左子节点依次入栈。
3. 如果遇到黑色节点，则将其值输出。

**优化：**

1. TreeNode类型表示新节点，int类型表示第二次出入栈。
2. stack是可以弹入null的，n个null值占n个位置。

```java
public List<Integer> inorder(TreeNode root) {
  List<Integer> list = new ArrayList<>();
  if (root == null) return list;
  Deque<Object> stack = new LinkedList<>();
  stack.offer(root);
  while (!stack.isEmpty()) {
    Object obj = stack.pollFirst();
    if (obj instanceof TreeNode) {
      TreeNode node = (TreeNode)obj;
      if (node.right != null) {
        stack.offerFirst(node.right);
      }
      stack.offerFirst(node.val);
      if (node.left != null) {
        stack.offerFirst(node.left);
      }
    } else {
      int val = (Integer)obj;
      list.add(val);
    }
  }
  return list;
}
```

# 深度优先遍历的应用

## 无向图中的连通分量

![image-20230123111816406](https://raw.githubusercontent.com/ericxiao417/Pics/main/202301231118537.png)

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/202301231119783.png" alt="image-20230123111947729" style="zoom: 33%;" />

**分析：**

1. 用嵌套数组创建图。
2. 用dfs遍历每个顶点。

```java
class solution {
    public int countComponent(int n, int[][] edges) {
        List<Integer>[] graph = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<>();
        }
        for (int[] edge : edges) {
            // 因为是无向图，所以要添加双向引用
            graph[edge[0]].add(edge[1]);
            graph[edge[1]].add(edge[0]);
        }
        boolean[] visited = new boolean[n];
        int ret = 0;
        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                ret++;
                dfs(i, visited, graph);
            }
        }
        return ret;
    }
    
    private void dfs(int point, boolean[] visited, List<Integer>[] graph) {
        visited[point] = true;
        List<Integer> neighbor = graph[point];
        for (int n : neighbor) {
            if (!visited[n]) {
                dfs(n, visited, graph);
            }
        }
    }
}
```

## 冗余连接

树可以看成是一个连通且无环的无向图。

现有 往一棵n个节点（节点值是1-n）的树中添加一条边后的 图。添加的边的两个顶点在1-n之间，且这条附加的边不属于树中已经存在的边。用int\[]\[] edges表示一个图，edges\[i]\[j]表示在点i和j之间存在一条边。

请找出一条可以删去的边，删除后可使得剩余部分是一棵树而不是图。如果有多个答案，则返回edges中最后出现的边。

**分析：**

1. 在一棵树中，边的数量比节点的数量少1。
2. 树是一个连通无环的无向图，在树中多加了一条边就会出现环，因此附加的边即为导致环出现的边。
3. 通过并查集寻找附加的边。起初时，每个节点都是一个独立的连通分量，遍历每一条边，判断这条边连接的两个点是否属于相同的连通分量。如果属于不相同的连通分量，则说明在遍历到当前的边之前，两个顶点之间不连通，因此不会导致环的出现，合并这两个顶点的连通分量。如果属于相同的连通分量，则说明在遍历到当前的边之前，这两个顶点已经连通，因此当前的边导致环出现，即为答案。

```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        if (edges == null || edges.length == 0) return new int[0];
        List<Integer>[] arr = new ArrayList[1];
        arr[0] = new ArrayList<>();
        UnionFind uf = new UnionFind(edges.length + 1);
        for (int[] edge : edges) {
            int start = edge[0];
            int end = edge[1];
            if (!uf.connected(start, end)) {
                uf.union(start, end);
            } else {
                arr[0].add(start);
                arr[0].add(end);
            }
        }
        return new int[]{arr[0].get(0), arr[0].get(1)};
    }
}

//并查集仅用于无向图
class UnionFind {
    int[] root;
    public UnionFind(int size) {
        root = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
        }
    }
    public int find(int x) {
        if (x == root[x]) {
            return x;
        }
        return root[x] = find(root[x]);
    }
    
    public void union(int x, int y) {
        int rootx = find(x);
        int rooty = find(y);
        if (rootx != rooty) {
            root[rootx] = rooty;
        }
    }
    
    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```



## 找到最终的安全状态

有一个 n 个节点的有向图，节点按照从 0 到 n-1 编号。图由一个索引从 0 开始的2D数组graph表示，graph\[i] 是与节点 i 相邻的节点的整数数组，这意味着从节点 i 到 graph\[i] 中的每个节点都有一条边。

如果一个节点没有连出的有向边，则它是终端节点。如果没有出边，则为终端节点。如果从该节点开始的所有可能路径都通向终端节点，则该节点为安全节点。

返回一个由图中所有安全节点组成的数组作为答案。答案数组中的元素应当按生序排列。

**分析：**

1. 这是在有向图的前提下进行。
2. 安全终点的意思是：它的前驱节点可以经过有限步来到一个没有后继节点的节点。
3. 因此这个问题就是要求我们判断一个有向图是否存在环，因此可以从一个节点开始执行深度优先遍历。
4. 从顶点 u 出发的所有路径是不是有一条能够回到 u 的路径，有回路就返回 true。

```java
class Solution {
    //每个节点都有三种状态，（已遍历）访问过，（已遍历）未访问，（未遍历）无结果。
    //用Boolean而不是boolean，是因为Boolean可以接受null，而boolean不可以接受null，无法表示第三种状态。
    Boolean[] visited;
    public List<Integer> eventualSafeNodes(int[][] graph) {
        List<Integer> list = new ArrayList<>();
        if (graph == null || graph.length == 0) return list;
        visited = new Boolean[graph.length];
        for (int i = 0; i < graph.length; i++) {
            if (dfs(graph, i)) {
                continue;
            }
            list.add(i);
        }
        return list;
    }
    
    // dfs的定义就是graph里的某个点是否有环
    private boolean dfs(int[][] graph, int point) {
        if (visited[point] != null) {
            return visited[point];
        }
        //首先假设point已经访问过
        visited[point] = true;
        //检验point的所有相邻点是否能返回自身，如果能，则表示point有环
        for (int i : graph[point]) {
            if (dfs(graph, i)) {
                return true;
            }
        }
        //重置point为false，即没有环
        visited[point] = false;
        return false;
    }
}
```

## 二分图检测

给定一个无向图，当这个图为二分图时，返回true。

如果我们将一个图的节点集合分割成两个独立的子集A和B，并且使图中的每一条边的两个节点一个来自A，一个来自B，我们就称之为二分图。

```java
public boolean isBipartite(int[][] graph) {
  if (graph == null || graph.length == 0) {
    return true;
  }
  int[] colored = new int[graph.length];
  for (int i = 0; i < graph.length; i++) {
    if (colored[i] == 0 && !dfs(graph, colored, 1, i)) {
      return false;
    }
  }
  return true;
}

private boolean dfs(int[] graph, int[] colored, int color, int point) {
  if (colored[point] != 0) {
    return colored[point];
  }
  colored[point] = color;
  int[] neighbor = graph[point];
  for (int n : neighbor) {
    if (!dfs(graph, colored, -color, n)) {
      return false;
    }
  }
  return true;
}
```
