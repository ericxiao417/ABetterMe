# 概述

主要有三种类型的图，无向图、有向图、加权图。加权图是指图中的每条边都带有一个权重，这里的权重可以是任何一种度量。

**名词解释：**

- 路径：从一个顶点到另一个顶点之间经过的所有顶点的集合。两个顶点之间的路径可以是很多条，不一定唯一。
- 路径长度：一条路径上经过的边的数量。
- 连通性：两个不同顶点之间存在至少一条路径，则称这两个顶点是连通的。
- 入度与出度：都是针对有向图来说。一个顶点被指向的边的数量叫入度，从一个顶点发出的边的数量叫出度。

**主要内容：**

- 并查集数据结构。
- 图的深度优先搜索。
- 图的广度优先搜索。
- 最短路径算法：Dijkstra算法、Bellman-Ford算法。
- 拓扑排序：Kahn算法。

# 图的存储

## 矩阵存图法

用一个二维数组来存图，下标代表点，值代表连边的情况，这就是所谓的矩阵存图法，也叫作邻接矩阵存图法。更具体的，我们一般使用bool数组来存储点与点之间的连边信息：

- 如果 con\[i][j] 的值为true，表示点i和点j之间连了一个边。
- 如果 con\[i][j] 的值为false，则表示两点之间没有边。

## 邻接表存图法

*TODO*

# 并查集

==只适用于无向图！！！==

两个重要的函数：

- find 函数：找到给定顶点的根节点。
- union 函数：合并两个顶点，并将他们的根节点保持一致。

## Quick Find 的工作原理

![Snipaste_2022-12-17_19-21-14-1](https://raw.githubusercontent.com/ericxiao417/Pics/main/Snipaste_2022-12-17_19-21-14-1.png)

root 数组存放的是父节点或者是父顶点，数组的索引代表当前节点。

**Example：**

比如要找 4 的根节点，先找到顶点 4， 然后看它的父节点是 1，1 != 4 ，说明 4 不是它本身的根节点。沿着父节点 1 的轨迹继续找，来到了索引是 1 的地方，发现它的父节点是 0 ， 继续寻找，到达索引 0 的地方，发现跟父节点相同，说明 0 是4的根节点，也是1 的根节点。

**优化：**



元素的值不再是父节点，而是根节点。

```java
class UnionFind {
	int[] root;
    
    pubulic UnionFind(int size) {
        // root 存放的是每个顶点的根节点。
        root = new int[size];
        //因为在刚开始，所有的顶点都是零散独立的，所以每个顶点的根节点都是它自己，
        //所以才会有root[i] = i。
        for (int i = 0; i < size; i++) {
            root[i] = i;
        }
    }
    //O(1)
    public int find(int x) {
        return root[x];
    }
    //O(n)
    public void union(int x, int y) {
        int rootX = find(x);
        int rooty = find(y);
        if (rootx != rooty) {
            for (int i = 0; i < root.length; i++) {
                if (root[i] == rooty) {
                    // 合并根节点，这样就有了：root[i] == rooty == rootx.
                    root[i] = rootx; 
                }
            }
        }
    }
    //O(1)
    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```

## Quick Union 工作原理



**Example:**



```java
class UnionFind {
    // root存放的是父节点
    int[] root;
    // 构造函数
    public UnionFind(int size) {
        root = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
        }
    }
    // O(n)
    public int find(int x) {
        while (x != root[x]) {
            x = root[x];
        }
        return x;
    }
    // O(n)
    public void union(int x, int y) {
        int rootx = find(x);
        int rooty = find(y);
        if (rootx != rooty) {
            root[y] = rootx;
        }
    }
    // O(n)
    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
    
}
```

## 按秩序合并的并查集

在 union 的时候，不再是随机选择x和y的一个父节点作为另一个节点的根节点，而是按照某种秩序，这里的秩序指的是每个顶点的高度。每次 union 两个顶点的时候，将高度大的那个顶点的根节点作为两个顶点的根节点，这样，就避免了所有顶点连成一条线。

```java
class UnionFind {
  int[] root;
  int[] rank; # represents the height of the node
  
  #construct function
  public UnionFind(int size) {
    root = new int[size];
    rank = new int[size];
    for (int i = 0; i < size; i++) {
      root[i] = i;
      rank[i] = i;
    }
  }
  
  public int find(int x) {
    while (x != root[x]) {
      x = root[x];
    }
    return x;
  }
  
  public void union(int x, int y) {
    int rootx = find(x);
    int rooty = find(y);
    if (rootx != rooty) {
      if (rank[x] > rank[y]) {
        root[rooty] = rootx;
      } else if (rank[x] < rank[y]) {
        root[rootx] = rooty;
      } else {
        root[rootx] = rooty;
        rank[rooty]++;
      }
    }
  }
  
  public boolean connected(int x, int y) {
    return find(x) ==  find(y);
  }
}
```

|            | UnionFind构造函数 | find函数 | union函数 | connected函数 |
| :--------: | :---------------: | -------- | --------- | :-----------: |
| 时间复杂度 |       O(N)        | O(logN)  | O(logN)   |    O(logN)    |

## 路径压缩优化的并查集

从上面的讲解中可以看出，要想找到一个节点的根节点，需要沿着它的父节点一直遍历下去，直到找到它的根节点为止。如果下次再查找同一个元素的根节点，又要重新做一遍。所以需要优化升级。

**优化：**

在找到根节点以后，将所有遍历过的元素的父节点都变成根节点，那么我们下次再查询同一个元素的时候，就仅仅只需要遍历两个元素就可以找到它的根节点了，这是非常高效的实现方式。如何将所有遍历过的元素的父节点都变成根节点呢？这里就需要用递归算法。这种优化叫做路径压缩，是对find函数的一个优化。

```java
class UnionFind {
  int root[];
  //constructor
  public UnionFind(int size) {
    root = new int[size];
    for (int i = 0; i < size; i++) {
      root[i] = i;
    }
  }
  // find
  public int find(int x) {
    if (x == root[x]) {
      return x;
    }
    return root[x] = find(root[x]);
  }
  // union
  public boolean union(int x, int y) {
    int rootx = find(x);
    int rooty = find(y);
    if (rootx != rooty) {
      root[rootx] = rooty;
    }
  }
  //connected
  public boolean connected(int x, int y) {
    return find(x) == find(y);
  }
}
```

|            | UnionFind构造函数 | find函数 | union函数 | connected函数 |
| :--------: | :---------------: | :------: | :-------: | :-----------: |
| 时间复杂度 |       O(N)        | O(logN)  |  O(logN)  |    O(logN)    |

# 图的深度优先搜索算法

DFS在图中的主要用途：1. 遍历图中所有节点。2. 遍历图中任意两点之间的所有路径。

## 遍历图中所有节点

DFS: 一条路走到黑，然后回退，换另一条路。需要一个栈来辅助，先进后出。通常用递归来代表栈。用栈来记录走到每一个节点时，有多少条路可以选择，即多少个选择。这样当回退到该点时，可以知道还剩余多少个选择可以选。每走到一个节点，都要检查是否访问过。DFS的重点就是一步一步往下走，直到撞南墙。

时间复杂度：O(V + E), V表示顶点数，E表示边数。

空间复杂度：O(V), V表示顶点数。

## 遍历图中任意两点的所有路径

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221218183854045.png" alt="image-20221218183854045" style="zoom:50%;" />

给一个n个节点的有向无环图(DAG), 找出所有从节点 0到节点n-1的路径并输出（不要求特定顺序）。graph[i]是一个从节点i可以访问的所有节点的列表，即从节点i到节点graph\[i][j]有一条有向边。

![image-20221218184222778](https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221218184222778.png)

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221218184438786.png" alt="image-20221218184438786" style="zoom:50%;" />

**提示：**

1. n == graph.length
2. n  is in [2, 15]
3. 0 <= graph\[i][j] < n
4. graph\[i][j] != j, 即不存在闭环
5. 保证输入是有向无环图，即DAG。

**分析：**

![image-20221218185022491](https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221218185022491.png)

该输入表示，在第0节点，指向1和2. 在第1节点，指向3. 在第2节点，指向3. 最后一个节点无任何指向，即终点。

```java
class Solution {
  //DFS
  public List<List<Integer>> allPathSourceTarget(int[][] graph) {
    List<List<Integer>> paths = new ArrayList<>();
    if (graph == null || graph.length == 0) {
      return paths;
    }
    List<Integer> path = new ArrayList<>();
    dfs(graph, 0, path, paths);
  }
  
  private void dfs(int[][] graph, int node, List<Integer> path, List<List<Integer>> paths) {
    path.add(node);
    if (node == graph.length - 1) {
      paths.add(new ArrayList<>(path));
      return;
    }
    int[] nextnodes = graph[node];
    for (int nextnode : nextnodes) {
      dfs(graph, nextnode, path, paths);
      path.remove(path.size() - 1);
    }
  }
}
```

# 图的广度优先搜索

 BFS不仅可以遍历图的所有节点，也可以遍历两个点之间的所有路径。**最高效的是：**当在权重相等且均为正数的图中，它可以迅速找到两点之间的最短路径。

BFS就是层层死磕。用到的数据结构是队列。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221218213127024.png" alt="image-20221218213127024" style="zoom:67%;" />

对于上图来说，A是第0层，C/D/B是第1层，因为它们距离A都是1。E/F是第2层，因为它们距离A都是2。

时间复杂度：O(V+E)，V代表顶点数，E代表边数。

空间复杂度：O(V)，V代表顶点数。

## 找出两点之间的最短路径

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221218213731846.png" alt="image-20221218213731846" style="zoom: 33%;" />

BFS找到的第一条从A到F的路径，A->B->F，就是最短路径。不是偶然，而是必然。第一层是A，第二层是C/D/B，第三层就是E,F，所以第一次遇到F就是最短路径。

时间复杂度：O(V+E)

空间复杂度：O(V).

上面DFS中提到的题目，用BFS解决，代码如下：

```java
class Solution {
  public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
    List<List<Integer>> paths = new ArrayList<>();
    if (graph == null || graph.length == 0) return paths;
    Queue<List<Integer>> queue = new LinkedList<>();
    List<Integer> path = new ArrayList<>();
    path.add(0);
    queue.offer(path);
    while (!queue.isEmpty()) {
      List<Integer> currentPath = new ArrayList<>();
      int currentNode = currentPath.get(currentPath.size() - 1);
      for (int node : graph[currentNode]) {
        currentPath.add(node);
        if (node == graph.length - 1) {
          paths.add(new ArrayList<>(currentPath));
        } else {
          queue.offer(new ArrayList<>(currentPath));
        }
        currentPath.remove(currentPath.size() - 1);
      }
    }
    return paths;
  }
}
```

# 最小生成树

生成树，是指在无向图中，具有该图全部顶点且边数最少的连通子图。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221220083327669.png" alt="image-20221220083327669" style="zoom:50%;" />

在上图中，所有粉色线条组成的一棵树[(A, B), (A, C), (A, D), (A, E)]，就是该无向图的其中一个生成树。其实，[(A, B), (B, C), (C, D), (D, E), (E, A)]也是一个生成树，由此可见，一个无向图可以有多个生成树。

**最小生成树**， 指的是在加权无向图中总权重最小的生成树。一个加权无向图的最小生成树也可以是多个。

生成最小生成树有两种算法及一种切分定理：Kruskal算法，Prim算法，切分定理。

## 切分定理

切分：将图切成两个部分，称之为一个切分。

横切边：如果一条边连接的两个顶点属于切分的两个部分，这个边叫做横切边。

==切分定理：==在一幅连通加权无向图中，给定任意的切分，如果有一条横切边的权值严格小于其他所有横切边，则这条边必然属于图的最小生成树的一条边。

## Kruskal算法

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221220134751812.png" alt="image-20221220134751812" style="zoom:33%;" />

**算法思想：**

1. 把所有的边按照权重从小到大排列。
2. 把这些边，根据1中的顺序，依次加入最小生成树中，如果形成环，则跳过该边。
3. 直到选择N-1条边为止。N是顶点的数量。

时间复杂度：O(ElogE),  E是边数。

空间复杂度：O(V)， V是顶点数。

**Example:**

![image-20221220135250633](https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221220135250633.png)

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221220135338701.png" alt="image-20221220135338701" style="zoom:33%;" />

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221220135410245.png" alt="image-20221220135410245" style="zoom: 33%;" />

**分析：**

在图中，如果要连接所有点，就要用到==生成树==这个数据结构。如果要求最小、最少，就要考虑到最小生成树。用一个Edge(start, end, cost)表示一条边。用堆排序算法把每条边进行排序。用并查集来检查两个点是否可以连接。

```java
class Solution {
  public int minCostConnectionPoints(int[][] points) {
    if (points == null || points.length == 0 || points[0].length == 0) return 0;
    Queue<Edge> minHeap = new PriorityQueue<>(new MyComparator());
    int number = points.length;
    for (int i = 0; i < number; i++) {
      for (int j = i + 1; j < number; j++) {
        int[] start = points[i];
        int[] end = points[j];
        int cost = Math.abs(end[0] - start[0]) + Math.abs(end[1] - start[1]);
        Edge edge = new Edge(i, j , cost);
        minHeap.add(edge);
      }
    }
    int sum = 0;
    int count = number - 1;
    UnionFind uf = new UnionFind(number);
    while (!minHeap.isEmpty() && count >= 0) {
      Edge edge = minHeap.poll();
      if (!uf.connected(edge.start, edge.end)) {
        uf.union(edge.start, edge.end);
        sum += edge.cost;
        count--;
      }
    }
    return sum;
  }
}

class Edge {
  int start;
  int end;
  int cost;
  public Edge(int start, int end, int cost) {
    this.start = start;
    this.end = end;
    this.cost = cost;
  }
}

class MyComparator implements Comparator<Edge> {
  @Override
  public int compare(Edge e1, Edge e2) {
    if (e1.cost == e2.cost) return 0;
    if (e1.cost < e2.cost) return -1;
    return 1;
  }
}

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

## Prim算法

TODO

# 单源最短路径相关算法

单源最短路径，就是指在加权图中，给定了一个起点，求出它到其他顶点的最短路径。这样，我们就可以获取两个顶点之间的最短路径了。

主要有两种算法：1. Dijkstra算法。2. Bellman-Ford算法。其中，Dijkstra算法只能解决加权有向图的权重为非负的情况。而Bellman-Ford算法能够解决权重为负的情况。

## Dijkstra算法

TODO

# 拓扑排序

拓扑排序针对的是**有向无环图**的一种算法。它是对图中所有顶点按照先后顺序的一种线性排序。换句话说，如果存在顶点u和v，想要到达顶点v，必须先到顶点u。那么在拓扑排序中，u必须在v前面。最有名的算法就是Kahn算法。

**Kakn算法：**

1. 需要借助队列这个数据结构，存的是所有入度为0的顶点。
2. 从队列取出一个顶点，遍历剩余节点，把以该节点为入度的节点的度数-1。如果为0，则加入队列。

时间复杂度：O(V+E), V是顶点，E是边。

空间复杂度：O(V+E)。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221221155050324.png" alt="image-20221221155050324" style="zoom: 50%;" />

```java
class Solution {
  public int[] findOrder(int numCourses, int[][] prerequisites) {
    int[] indegree = new int[numCourses];
    List<List<Integer>> list = new ArrayList<>();
    int length = prerequisites.length;
    for (int i = 0; i < length; i++) {
      list.add(new ArrayList<>());
    }
    for (int[] pre : prerequisites) {
      int in = pre[0];
      int out = pre[1];
      list.get(out).add(in);
      indegree[in]++;
    }
    Queue<Integer> queue = new ArrayDeque<>();
    for (int i = 0; i < numCourses; i++) {
      if (indegree[i] == 0) {
        queue.offer(i);
      }
    }
    List<Integer> ret = new ArrayList<>();
    while (!queue.isEmpty()) {
      int course = queue.poll();
      ret.add(course);
      for (int i : list.get(course)) {
        indegree[i]--;
        if (indegree[i] == 0) {
          queue.offer(i);
        }
      }
    }
    if (ret.size() != numCourses) return new int[0];
    int size = ret.size();
    int[] res = new int[size];
    for (int i = 0; i < size; i++) {
      res[i] = ret.get(i);
    }
    return res;
  }
}
```

