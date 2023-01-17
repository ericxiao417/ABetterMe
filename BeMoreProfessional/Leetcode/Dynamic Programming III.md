# Longest Consecutive 1s

Given an array containing only 0s and 1s, find the length of the longest subarray of consecutive[连续的] 1s.

```java
public int longest(int[] array) {
  if (array == null || array.length == 0) return 0;
  int[] dp = new int[array.length];
  dp[0] = array[0] == 1 ? 1 : 0;
  int maxLength = dp[0];
  for (int i = 1; i < array.length; i++) {
    if (array[i] == 1) {
      dp[i] = dp[i - 1] + 1;
    } else {
      dp[i] = 0;
    }
    maxLength = Math.max(maxLength, dp[i]);
  }
  return maxLength;
}
```

# 重要概念区分

最长公共子序列：longest common sub-sequence, 将给定序列中的零个或者多个元素去掉之后得到的结果。

最长公共子串：longest common substring， 给定串中的任意连续的字符组成的子序列称为该串的子串。

==也就是说：==

​	子序列不一定是连续的，子串一定是连续的！

<img src="Dynamic Programming III.assets/image-20220922222323497.png" alt="image-20220922222323497" style="zoom:50%;" />

# Longest Ascending Subsequence I



Given an array of integers, find out the lengest ascending sub-sequence, return the length.

```java
public int longest(int[] array) {
  if (array == null || array.length == 0) {
    return 0;
  }
  int[] dp = new int[array.length];
  dp[0] = 1;
  int maxLength = 1;
  for (int i = 1; i < array.length; i++) {
    dp[i] = 1;
    for (int j = 0; j < i; j++) {
      if (array[i] > array[j]) {
        dp[i] = Math.max(dp[i], dp[j] + 1);
      }
    }
    maxLength = Math.max(maxLength, dp[i]);
  }
  return maxLength;
}
```

# Longest Ascending Subsequence II

Given an array of integers, find out the longest ascending subsequence. If there are multiple results, then return any valid result.

