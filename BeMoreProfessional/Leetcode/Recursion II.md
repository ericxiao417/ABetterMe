# Recursion II

## Spiral Order Traverse

### N * N

<img src="Recursion II.assets/image-20220604005329840.png" alt="image-20220604005329840" style="zoom:50%;" />

```java
public List<Integer> spiral(int[][] matrix) {
  List<Integer> list = new ArrayList<>();
  if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return list;
  recursion(matrix, matrix.length, 0, list);
  return list;
}

private void recursion(int[][] matrix, int edge, int topLeft, List<Integer> list) {
  if (edge == 0) return;
  if (edge == 1) {
    list.add(matrix[topLeft][topLeft]);
    return;
  }
  // topLeft是index，分别表示最左边和最上边的index。 botRight也是index，表示最右边和最下边的index
  int botRight = topLeft + edge - 1;
  for (int j = topLeft; j < botRight; j++) {
		list.add(matrix[topLeft][j]);
  }
  for (int i = topLeft; i < botRight; i++) {
    list.add(matrix[i][botRight]);
  }
  for (int j = botRight; j > topLeft; j--) {
    list.add(matrix[topLeft][j]);
  }
  for (int i = botRight; i > topLeft; i--) {
    list.add(matrix[i][topLeft]);
  }
  recursion(matrix, edge - 2, topLeft + 1, list);
}
```

### N * M

<img src="Recursion II.assets/image-20220604170708206.png" alt="image-20220604170708206" style="zoom:50%;" />

```java
```





## N Queens

<img src="Recursion II.assets/image-20220604100430380.png" alt="image-20220604100430380" style="zoom:50%;" />

用DFS解决。

```java
public List<List<Integer>> nQueens(int n) {
  List<List<Integer>> list = new ArrayList<>();
  List<Integer> tmp = new ArrayList<>();
  DFS(n, list, tmp, 0);
  return list;
}

private void DFS(int n, List<List<Integer>> list, List<Integer> tmp, int index) {
	if (n == index) {
    list.add(new ArrayList<>(tmp));
    return;
  }
  for (int i = 0; i < n; i++) {
    if (isValid(i, tmp)) {
      tmp.add(i);
      DFS(n, list, tmp, index + 1);
      tmp.remove(tmp.size() - 1);
    }
  }
}

private boolean isValid(int index, List<Integer> tmp) {
  int size = tmp.size();
  for (int i = 0; i < size; i++) {
    if (index == tmp.get(i) || Math.abs(size - i) == Math.abs(tmp.get(i) - index)) {
      return false;
    }
  }
  return true;
}
```

## Reverse Linked List in Pairs

<img src="Recursion II.assets/image-20220604135515620.png" alt="image-20220604135515620" style="zoom:50%;" />

```java
public ListNode reverseInParis(ListNode head) {
  if (head == null || head.next == null) return head;
  ListNode tmp = reverseInParis(head.next.next);
  ListNode next = head.next;
  next.next = head;
  head.next = tmp;
  return next;
}
```

## String Abbreviation Matching

<img src="Recursion II.assets/image-20220604140157247.png" alt="image-20220604140157247" style="zoom:50%;" />

```java
public boolean match(String input, String pattern) {
  int i = 0, p = 0;
  while (i < input.length() && p < pattern.length()) {
    if (Character.isLetter(pattern.charAt(p))) {
      if (input.charAt(i) != pattern.charAt(p)) {
        return false;
      }
      p++;
      i++;
    } else {
      int count = 0;
      while (p < pattern.length() && Character.isDigit(pattern.charAt(p))) {
        count = count * 10 + pattern.charAt(p) - '0';
        p++;
      }
      i += count;
    }
  }
  return i == input.length() && p == pattern.length();
}
```

## Tree + Recursion

第一类：从下往上返值。

三部曲：

1. 从子节点要什么，通常是这个递归函数的返回类型。
2. 在当前层处理什么。
3. 向父节点提供什么。

### Store Number of Nodes in Left Subtree

<img src="Recursion II.assets/image-20220604142214843.png" alt="image-20220604142214843" style="zoom:50%;" />

```java
class TreeNodeLeft {
  public int key;
  public TreeNodeLeft left;
  public TreeNodeLeft right;
  public int numNodesLeft;
  public TreeNodeLeft(int key) {
    this.key = key;
  }
}

public void numNodesLeft(TreeNodeLeft root) {
  helper(root);
}

private int helper(TreeNodeLeft root) {
  if (root == null) return 0;
  int left = helper(root.left);
  int right = helper(root.right);
  root.numNodesLeft = left;
  return left + right + 1;
}
```

### Lowest Common Ancestor I

Given two nodes in a binary tree, find their lowest common ancestor.

```java
public TreeNode lowest(TreeNode root, TreeNode one, TreeNode two) {
  if (root == null || root == one || root == two) {
    return root;
  }
  TreeNode left = lowest(root.left, one, two);
  TreeNode right = lowest(root.right, one, two);
  if (left == null) return right;
  if (right == null) return left;
  return root;
}
```

