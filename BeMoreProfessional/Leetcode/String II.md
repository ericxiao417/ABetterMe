# String II

## 题型分类

### String Reversal

#### apple -> elppa

```java
public String reverse(String input) {
  if (input == null || input.length() == 0) return input;
  char[] arr = input.toCharArray();
  int i = 0, j = arr.length - 1;
  while (i < j) {
    swap(arr, i++, j--);
  }
  return new String(arr);
}

private void swap(char[] arr, int left, int right) {
  char tmp = arr[left];
  arr[left] = arr[right];
  arr[right] = tmp;
}
```

#### I love yahoo -> yahoo love I

[^分析]:1. swap the whole sentence. 2. swap every single word. (two pointers)

*TODO*

```java
public String reverseWords(String input) {
  if (input == null || input.length() == 0) return input;
  String[] arr = input.split(" ");
  StringBuilder sb = new StringBuilder();
  for (int i = arr.length - 1; i >= 0; i--) {
    sb.append(arr[i]);
    if (i != 0) {
      sb.append(" ");
    }
  }
  return sb.toString();
}
```

#### abcdef -> efabcd  shift the whole string to the right-hand by two(n) positions.

[^分析]: 1. ReverseSubString(0, length() - 1); 2.ReverseSubString(0, n - 1); 3. ReverseSubString(n, length - 1).

```java
public String rightShift(String input, int n) {
  if (input == null || input.length() == 0 || n == 0) return input;
	n = n % input.length();
  char[] arr = input.toCharArray();
  swap(arr, 0, arr.length - 1);
  swap(arr, 0, n - 1);
  swap(arr, n, arr.length - 1);
  return new String(arr);
}

private void swap(char[] arr, int left, int right) {
  while (left < right) {
    char tmp = arr[left];
    arr[left] = arr[right];
    arr[right] = tmp;
    left++;
    right--;
  }
}
```

### Char Replacement

#### student -> studXXt (en -> XX)

<img src="String II.assets/image-20220531000846165.png" alt="image-20220531000846165" style="zoom:50%;" />

```java
public String replace(String input, String source, String target) {
  StringBuilder sb = new StringBuilder();
  int i = 0;
  while (i < input.length()) {
    if (!isSubStr(input, source, i)) {
			sb.append(input.charAt(i++);
    } else {
      sb.append(target);
      i = i + source.length();
    }
  }
  return sb.toString();
}

private boolean isSubStr(String input, String source, int index) {
  if (input.length() < source.length()) return false;
  if (source.length() + index > input.length()) return false;
  for (int i = 0; i < source.length(); i++) {
    if (source.charAt(i) != input.charAt(i + index)) {
			return false;
    }
  }
  return true;
}
```

### String Encoding/Decoding

<img src="DFS.assets/image-20220531085122041.png" alt="image-20220531085122041" style="zoom:50%;" />:

```java
public String compress(String input) {
  if (input == null || input.length() == 0) return input;
  StringBuilder sb = new StringBuilder();
  int cur = 0;
  while (cur < input.length()) {
    sb.append(input.charAt(cur));
    cur++;
    int count = 1;
    while (cur < input.length() && input.charAt(cur - 1) == input.charAt(cur)) {
      count++;
      cur++;
    }
    sb.append(count);
  }
  return sb.toString();
}
```

<img src="String II.assets/image-20220602215843867.png" alt="image-20220602215843867" style="zoom:50%;" />

```java
public String decompress(String input) {
  if (input == null || input.length() == 0) return input;
  StringBuilder sb = new StringBuilder();
  int cur = 0;
  while (cur < input.length()) {
    sb.append(input.charAt(cur));
    cur++;
    int count = input.charAt(cur) - '0';
    if (count == 0) {
      sb.deleteCharAt(cur - 1);
      cur++;
    } else {
      for (int i = 1; i < count; i++) {
        sb.append(input.charAt(cur - 1));
      }
      cur++;
    }
  }
  return sb.toString();
}
```

### Sliding Window in a string (slow + fast indices)

#### Longest substing that contains only unique char

Given a string, returns the length of the longest substing without duplicate characters.

==use two pointers, slow(s) and fast(f)==

==slow: the left border of the window==

==fast: fast - 1 is the right border of the window==

==hashmap: <key = letter, value = the frequency of all the letters in the sliding window[s, f - 1]>==

Initialization: s = 0, f = 0, hashmap map.

For each step: a[f] not in the hashmap, move fast; a[f] is in the hashmap, move slow.

```java
public int longest(String input) {
  if (input == null || input.length() == 0) {
    return 0;
  }
  int slow = 0, fast = 0, length = 0;
  Set<Character> set = new HashSet<>();
  while (fast < input.length()) {
    char c = input.charAt(fast);
    if (set.contains(c)) {
      set.remove(input.charAt(slow++));
    } else {
      set.add(input.charAt(fast++));
      length = Math.max(length, fast - slow);
    }
  }
  return length;
}
```

#### Find all anagrams of a substring S2 in a long string S1

==s: the left border, f: the right border==

==hashmap: the number of occurences in a[s….f] of each character, ignoring characters that are not in S2==

==num_matching_chars: number of matching characters==

<img src="String II.assets/image-20220603001432833.png" alt="image-20220603001432833" style="zoom:50%;" />

```java
public List<Integer> allAnagrams(String sh, String lo) {
  List<Integer> list = new ArrayList<>();
  Map<Character, Integer> map = new HashMap<>();
  for (char c : sh.toCharArray()) {
    map.put(c, map.getOrDefault(c, 0) + 1);
  }
  int cur = 0, matched = 0;
  while (cur < lo.length()) {
		char c = lo.charAt(cur);
    if (map.containsKey(c)) {
      int count = map.get(c);
      map.put(c, count - 1);
      if (count - 1 == 0) {
        matched++;
      } else if (count - 1 == -1) {
        matched--;
      }
    }
    if (cur >= sh.length() - 1) {
      if (matched == map.size()) {
        list.add(cur - sh.length() + 1);
      }
      char d = lo.charAt(cur - sh.length() + 1);
      if (map.containsKey(d)) {
				int num = map.get(d);
        map.put(d, num + 1);
        if (num + 1 == 0) {
          matched++;
        } else if (num + 1 == 1) {
          matched--;
        }
      }
    }
    cur++;
  }
  return list;
}
```

#### Given a 0-1 array, you can flip at most k size of 0 to 1. Please find the longest subarray that consists of all 1.

==滑动窗口的两个边框，左闭右开，右边表示即将要进入窗口的元素，所以最后求length需要fast - 1。

```java
public int longest(int[] nums, int k) {
  if (nums == null || nums.length == 0) return 0;
  int slow = 0, fast = 0, length = 0, num_zeros = 0;
  while (fast < nums.length) {
    if (nums[fast] == 1) {
      fast++;
    } else {
      if (num_zeros < k) {
        fast++;
      } else {
        num_zeros -= (nums[slow] == 0 ? 1 : 0);
        slow++;
      }
    }
    lengtgh = Math.max(length, fast - slow);
  }
  return length;
}
```



