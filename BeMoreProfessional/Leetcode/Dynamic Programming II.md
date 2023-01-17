# Dynamic Programming II

## Array Hopper II

Given an array of non-negative integers, you are initially standing on the index 0 of the array. Array[i] means the maximum jump distance from index i. Determine the minimum number of jumps to reach the end. If can not, return -1.

```java
public int minJump(int[] array) {
  if (array == null || array.length == 0) return -1;
  int[] dp = new int[array.length];
  dp[0] = 0;
  for (int i = 1; i < array.length; i++) {
    dp[i] = Integer.MAX_VALUE;
    for (int j = 0; j < i; j++) {
      if (dp[j] != Integer.MAX_VALUE && array[j] >= i - j) {
        dp[i] = Math.min(dp[j] + 1, dp[i]);
      }
    }
  }
  return dp[array.length - 1];
}
```

## Largest SubArray Sum

Given an unsorted integer array, find the subarray that has the maximal sum. Return the sum.

```java
public int largestSum(int[] array) {
  if (array == null || array.length == 0) return 0;
  int[] dp = new int[array.length];
  dp[0] = array[0];
  int sum = array[0];
  for (int i = 1; i < array.length; i++) {
    if (dp[i - 1] < 0) {
      dp[i] = array[i];
    } else {
      dp[i] = dp[i - 1] + array[i];
    }
    sum = Math.max(sum, dp[i]);
  }
  return sum;
}
```

## Dictionary Word I

Given a word and a dictionary, determine if it can be composed by concatenating from the given dictionary.

<img src="Dynamic Programming II.assets/image-20220604203927180.png" alt="image-20220604203927180" style="zoom:50%;" />

```java
public boolean canBread(String input, String[] dict) {
  if (input == null || input.length() == 0) return true;
  boolean[] dp = new boolean[input.length() + 1];
  dp[0] = true;
  Set<String> set = new HashSet<>();
  for (String s : dict) {
    set.add(s);
  }
  for (int i = 1; i <=input.length(); i++) {
    for (int j = 0; j < i; j++) {
      if (dp[j] && set.contains(input.substring(j, i))) {
        dp[i] = true;
      }
    }
  }
  return dp[input.length()];
}
```

## Edit Distance

Given two strings of characters, determine the minimum number of Replace, Delete and Insert operations needed to transform one string into the other.

<img src="Dynamic Programming II.assets/image-20220605223116636.png" alt="image-20220605223116636" style="zoom:50%;" />

分析：

对于当前比较的连个字符 word1[i] 和 word2[j], 若二者相同，直接跳到下一个位置。若不相同，有三种处理方法。首先是在S1直接插入一个word2[j], 那么word2[j]的位置就跳过了，接着比较word1[i]和word2[j + 1]。第二个方法是删除，即将word1[i]字符直接删除，接着比较word1[i + 1]和word2[j]。第三种就是将word1[i]修改为word2[j]，那么接着比较word1[i + 1]和word2[j + 1]。

状态转移：f(i)(j) 表示word1的前i个字符变成word2的前j个字符，最少需要的操作数。

状态转移方程：

1. 如果word1[i] == word2[j], 则其操作数为f(i-1)(j - 1).
2. 如果word1[i]修改为word2[j]，则操作数为f(i-1)(j-1) + 1.
3. 如果将word1[i]删除，则其操作数为f(i - 1)(j) + 1.
4. 在word1[i]之前插入word2[j], 那么其操作数为f(i)(j - 1) + 1.
5. 时间复杂度O(n~2).

```java
public int editDistance(String one, String two) {
  //因为是前i个数，所以dp的下标应该是i + 1。
  int[][] dp = new int[one.length() + 1][two.length() + 1];
  for (int i = 0; i <= one.length(); i++) {
    for (int j = 0; j <= two.length(); j++) {
      if (i == 0) {
        dp[0][j] = j;
      } else if (j == 0) {
        dp[i][0] = i;
      } else {
				if (one.charAt(i - 1) == two.charAt(j - 1)) {
          dp[i][j] = dp[i - 1][j - 1];
        } else {
          dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
        }
      }
    }
  }
  return dp[one.length()][two.length()];
}
```



## Largest Square of 1s

Determine the largest square of 1s in a binary matrix (a binary matirx only contains 0 and 1). Return the length of the largest square.

分析：

dp(i)(j): 表示以matrix(i, j)为右下角的正方形的最大边长。

初始状态：`dp[0][j]` 和 `dp[i][0]`的值, 即第零行和第零列的值，这两个是计算后面的基础。

动态转移方程：dp(i)(j) = min(dp(i - 1)(j - 1), dp(i - 1)(j), dp(i)(j - 1)) + 1.



```java
public int largest(int[][] matrix) {
  int[][] dp = new int[matrix.length][matrix.length];
  int length = 0;
  for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[0].length; j++) {
			if (i == 0) {
        dp[0][j] = matrix[0][j];
      } else if (j == 0) {
        dp[i][0] = matrix[i][0];
      } else {
        if (matrix[i][j] == 0) {
          dp[i][j] = 0;
        } else {
          dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
        }
      }
      length = Math.max(length, dp[i][j]);
    }
  }
  return length;
}
```

