# Dynamic Programming

动态规划两个核心要素：

1. 声明int[] dp数组。
2. 得出dp[0]。
3. 写出不同情况下的状态转移方程。

一维动态规划的解题思路：linear scan and when at i, look back at i - 1.

## Longest Ascending Subarray

Given an unsorted array, find the length of the longest ascending subarray.

```java
public int longest(int[] array) {
  if (array == null || array.length == 0) return 0;
  int[] dp = new int[array.length];
  dp[0] = 1;
  int length = 1;
  for (int i = 1; i < array.length; i++) {
    if (array[i] > array[i - 1]) {
      dp[i] = dp[i - 1] + 1;
    } else {
      dp[i] = 1;
    }
    length = Math.max(length, dp[i]);
  }
  return length;
}
```

## Maximal Product when Cutting Rope

<img src="Dynamic Programming I.assets/image-20220604194923076.png" alt="image-20220604194923076" style="zoom:50%;" />

思路：

三个变量：

​	i ：表示绳子的总长度。

​	j :   表示已经分割的长度。

​	i - j : 表示剩下的长度。

每计算出一个结果，都需要跟当前 i 对应的最大结果，分割一次的结果，剩下长度的最优结果进行对比，即 dp[i],  j * (i - j),  j * dp[i - j].

```java
public int cuttingRope(int n) {
  int[] dp = new int[n + 1];
  dp[2] = 1; // 当绳子长2米时，切一刀，只能是 1 * 1 = 1
  for (int i = 3; i <= n; i++) {
    for (int j = 2; j < i; j++) {
      int a = j * (i - j);
      int b = j * dp[i - j];
      dp[i] = Math.max(dp[i], Math.max(a, b));
    }
  }
  return dp[n];
}
```

## Array Hopper I

<img src="Dynamic Programming I.assets/image-20220604200504939.png" alt="image-20220604200504939" style="zoom:50%;" />

```java
public boolean canJump(int[] array) {
  if (array == null || array.length == 0) return true;
  boolean[] dp = new boolean[array.length];
  dp[0] = true;
  for (int i = 1; i < array.length; i++) {
    for (int j = 0; j < i; j++) {
      if (dp[j] && array[j] >= i - j) {
        dp[i] = true;
      }
    }
  }
  return dp[array.length - 1];
}
```

