# HashMap & String I

## 知识梳理

在hashmap里，存储的是key-value的键值对。其中，key不可以重复，value可以重复。

## 典型例题

### Top K Frequent Words

Given a composition with different kinds of words, return a list of the top K most frequent words in the composition. K >= 1 and the composition is not null and is not guaranteed to be sorted.

```java
public class Solution {
  public String[] topKFrequent(String[] combo, int k) {
    Map<String, Integer> map = new HashMap<>();
    for (String s : combo) {
      map.put(s, map.getOrDefault(s, 0) + 1);
    }
    Queue<Map.Entry<String, Integer>> minHeap = new PriorityQueue<>(new MyComparator());
    for (Map.Entry<String, Integer> m : map.entrySet()) {
      if (minHeap.size() < k) {
        minHeap.offer(m);
      } else {
        if (minHeap.peek().getValue() < m.getValue()) {
          minHeap.poll();
          minHeap.offer(m);
        }
      }
    }
    int size = minHeap.size();
    String[] ret = new String[size];
    for (int i = size - 1; i >= 0; i--) {
      ret[i] = minHeap.poll().getKey();
    }
    return ret;
  }
}

class MyComparator implements Comparator<Map.Entry<String, Integer>> {
  @Override
  public int compare(Map.Entry<String, Integer> m1, Map.Entry<String, Integer> m2) {
    if (m1.getValue() == m2.getValue()) return 0;
    if (m1.getValue() < m2.getValue()) return -1;
    return 1;
  }
}
```

### Missing Number I

Given an integer array of size N - 1, containing all the numbers from 1 to N except one, find the missing number. The given array is not null and N >= 1.

```java
public int missing(int[] array) {
  if (array.length == 0) return 1;
  Set<Integer> set = new HashSet();
  for (int i : array) {
    set.add(i);
  }
  for (int i = 1; i <= array.length + 1; i++) {
    if (!set.contains(i)) {
      return i;
    }
  }
  return -1;
}
```

### Common Numbers of Two sorted Arrays.

[^分析]:clarify: 1. Sorted: ascending or descending. 2. Duplicated or not. 3. The length of two arrays.

In each array, elements are sorted in ascending order and there could be duplicated numbers. Both two arrays are not null.

```java
public List<Integer> common(int[] A, int[] B) {
  List<Integer> list = new ArrayList<>();
  int i = 0, j = 0;
  while (i < A.length && j < B.length) {
    if (A[i] == B[j]) {
      list.add(A[i]);
      i++;
      j++;
    } else if (A[i] < B[j]) {
      i++;
    } else {
      j++;
    }
  }
  return list;
}
```

### String problems in Java = in-place modify a char[]

“abcd”. 5类常考问题：

1. Char remove: remove some particular chars from string; remove all leading/trailing/duplicated empty spaces from a string.
2. De-duplication. aaaabbbbc ->abc
3. Substring -> strstr: regular methods; Rabin-Carp & KMP.
4. Reversal. I love yahoo -> yahoo love I
5. Replace empty space “ ” with “%20”.

#### (Char remove) Remove a/some particular chars from a string in place.

<img src="E:\GitRepo\Typora\刷题\HashMap & String I.assets\image-20220529234831094.png" alt="image-20220529234831094" style="zoom:50%;" />

[^分析]: 利用 2 个挡板，3 个区间，同向而行。

j = 0; (fast) : the letter being processed. In other words, all letters to left side of j (**not including j**) are processed letters.

i = 0; (slow) : all letters to the left side of i (**not including i)** are all processed letters that should be kept.

All letters in [i, j - 1] are all area that we do not care.

[j, size - 1] : unknown area that need to be explored.

```java
public String remove(String input, String target) {
  if (input == null || input.length() == 0) {
    return input;
  }
  char[] arr = input.toCharArray();
  Set<Character> set = new HashSet<>();
  for (char c : target.toCharArray()) {
    set.add(c);
  }
  int slow = 0, fast = 0;
  while (fast < arr.length) {
    char tmp = arr[fast];
    if (set.contains(tmp)) {
      fast++;
    } else {
      arr[slow] = arr[fast];
      slow++;
      fast++;
    }
  }
  return new String(arr, 0, slow);
}
```

#### (Char remove) Remove all leading/trailing and duplicated empty spces(only leave one empty space if duplicated spaces happen) from the input string. (must in place)

[^分析]: j = 0, fast, the letter being processed. In other words, all letters to the left side of j (==not including j==) are processed letters.

i = 0, slow, all letters on the left side of i (==not including i==) are all processed and should be kept.

All letters between [i, j - 1] are the area that we do not need to care about.

[j, size - 1] is the area that need to be explored.

<img src="E:\GitRepo\Typora\刷题\HashMap & String I.assets\image-20220530083908457.png" alt="image-20220530083908457" style="zoom:50%;" />

```java
public String removeSpace(String input) {
  if (input == null || input.length() == 0) return input;
  char[] arr = input.toCharArray();
  int slow = 0, fast = 0;
  while (fast < arr.length) {
    char value = arr[fast];
    if (value == ' ') {
      if (fast == 0 || arr[fast - 1] == arr[fast]) {
        fast++;
      } else {
        arr[slow++] = arr[fast++];
      }
    } else {
      arr[slow++] = arr[fast++];
    }
  }
  if (arr.length - 1 >= 0 && arr[fast - 1] == ' ') {
    slow--;
  }
  if (slow > 0) {
    return new String(arr, 0, slow);
  }
  return new String();
}
```

#### (char de-duplication) Remove duplicated and adjacent letters, leave only one letter in each duplicated section.

aabbbazw - > abazw

[^分析]: j = 1, fast, the letter being processed. In other words, all letters on the left side of j (==not including j==) are processed letters already. 

i = 1, slow, all letters on the left side of i (==not including i==) are all processed letters that should be kept. 

All letters in [i, j - 1] are the area that we do not need to care about.

The area [j, size - 1] is needed to be explored.

```java
public String deDup(String input) {
  if (input == null || input.length == 0) {
    return input;
  }
  char[] arr = input.toCharArray();
  int slow = 1, fast = 1;
  while (fast < input.length()) {
    char value = arr[fast];
    char target = arr[slow - 1];
    if (value == target) {
      fast++;
    } else {
      arr[slow++] = arr[fast++];
    }
  }
  return new String(arr, 0, slow);
}
```

#### (char de-duplicate) Repeatedly remove all adjacent, repeated letters in a given string from left to right.

![image-20220530212832072](E:\GitRepo\Typora\刷题\HashMap & String I.assets\image-20220530212832072.png)

[^分析]: j = 0, fast, the letter being processed. In other words, all letters to the left side of j (==not including j==) are processed letters.

stack: all the processed letters that should be kept temperatly.

```java
public String deDup(String input) {
  if (input == null || input.length() == 0) {
    return input;
  }
  char[] arr = input.toCharArray();
  Deque<Character> stack = new ArrayDeque<>();
  int cur = 0;
  while (cur < arr.length) {
    if (cur == 0 || stack.isEmpty()) {
      stack.offerFirst(arr[cur++]);
      continue;
    }
    if (stack.peekFirst() == arr[cur]) {
      while (cur < arr.length && stack.peekFirst() == arr[cur]) {
        cur++;
      }
      stack.pollFirst();
    } else {
      stack.offerFirst(arr[cur++]);
    }
  }
  StringBuilder sb = new StringBuilder();
  while (!stack.isEmpty()) {
    sb.insert(0, stack.pollFirst());
  }
  return sb.toString();
}
```

#### (Strstr) Substring problem: how to determine whether a string is a part of another string.

<img src="E:\GitRepo\Typora\刷题\HashMap & String I.assets\image-20220530220303328.png" alt="image-20220530220303328" style="zoom:50%;" />

```java
public int strstr(String large, String small) {
  if (large.length() < small.length()) return -1;
  if (large.length() == small.length()) return 0;
  for (int i = 0; i < large.length(); i++) {
    if (isSubStr(large, small, i)) 「
      return i;
  }
  return -1;
}

private boolean isSubStr(String large, String small, int index) {
  if (small.length() + index > large.length()) return false;
  if (small.length() > large.length()) return false;
  for (int i = 0; i < small.length(); i++) {
		if (small.charAt(i) != large.charAt(index + i)) {
      return false;
    }
  }
  return true;
}
```

### Deep Copy

#### Linked List with Random Pointer

Each of the nodes in the linked list has another pointer pointing to a random node in the list or null. Make a deep copy of the original list.

```java
class RandomListNode {
    public int value;
    public RandomListNode next;
    public RandomListNode random;
    public RandomListNode(int value) {
        this.value = value;
    }
}

public RandomListNode copy(RandomListNode head) {
    if (head == null) return head;
    Map<RandomListNode, RandomListNode> map = new HashMap<>();
    RandomListNode cur = head;
    while (cur != null) {
        RandomListNode node = new RandomListNode(cur.value);
        map.put(cur, node);
        cur = cur.next;
    }
    cur = head;
    while (cur != null) {
        RandomListNode node = map.get(cur);
        if (cur.next != null) {
            node.next = map.get(cur.next);
        }
        if (cur.random != null) {
            node.random = map.get(cur.random);
        }
        cur = cur.next;
    }
    return map.get(head);
}
```

#### Undirected Graph

Make a deep copy of an undirected graph, there could be cycles in the original graph.









