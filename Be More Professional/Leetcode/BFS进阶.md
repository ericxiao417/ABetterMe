# BFS进阶

## 拓扑排序

```java
// Definition for directed graph
class DirectedGraphNode {
  int label;
  ArrayList<DirectedGraphNode> neighbors;
  public DirectedGraphNode(int label) {
    this.label = label;
    neighbors = new ArrayList<DirectedGraphNode>();
  }
}

public class Solution {
  // given a list of directed graph nodes
  // return: any topological order for the given graph.
  public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
		ArrayList<DirectedGraphNode> list = new ArrayList<>();
    if (graph == null || graph.size() == 0) return list;
    Map<DirectedGraphNode, Integer> indegree = new HashMap<>();
    Queue<DirectedGraphNode> queue = new LinkedList<>();
    int n = graph.size();
    for (int i = 0; i < n; i++) {
			List<DirectedGraphNode> neighbor = graph.get(i).neighbors;
      int m = neighbor.size();
      for (int j = 0; j < m; j++) {
        DirectedGraphNode tmp = neighbor.get(j);
        indegree.put(tmp, indegree.getOrDefault(tmp, 0) + 1);
      }
    }
    for (int i = 0; i < n; i++) {
      if (!indegree.containsKey(graph.get(i))) {
        queue.offer(graph.get(i));
      }
    }
    while (!queue.isEmpty()) {
      DirectedGraphNode now = queue.poll();
      list.add(now);
      int m = now.neighbors.size();
      for (int i = 0; i < m; i++) {
        indegree.put(now.neighbors.get(i), indegree.get(now.neighbors.get(i)) - 1);
        if (indegree.get(now.neighbors.get(i)) == 0) {
          queue.offer(now.neighbors.get(i));
        }
      }
    }
    return list;
  }
}
```

