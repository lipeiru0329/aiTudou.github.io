---
title: Leetcode-回顾
date: 2020-06-06 22:00:50
tags:
---

## LeetCode 回顾

长时间没有刷leetcode了，最近重刷发现很多的东西，我都不记得了或者只有大概印象，但是具体写的时候都没有印象了。所以说这才像写这个总结。

<!-- More -->

### Slide Window

---

问题： https://leetcode.com/problems/longest-substring-without-repeating-characters/

这类问题是是让你找到最大或者最长substring的问题。由于是找substring，座椅说整个问题可以变成找起始点和终止点的问题 --> 这个就是Slide window

其实slide window 也是可以用在 AI 里面，CNN里面的Object Location就是利用Slide Window 去实现扫描图里面所有的小格子去找到Object的，具体的可以看另一篇[CNN-Object-Detection](https://lipeiru0329.github.io/2020/05/28/CNN-Object-Detection/#more)里面有详细讲。

回到正题，由于是找起始点和终止点， 于是就是变成找 最大起始点 和 最小终止点的问题。 

Java:
```
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        Map<Character, Integer> map = new HashMap<>(); // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            if (map.containsKey(s.charAt(j))) {
                i = Math.max(map.get(s.charAt(j)), i);
            }
            ans = Math.max(ans, j - i + 1);
            map.put(s.charAt(j), j + 1);
        }
        return ans;
    }
}
```
Golang: 
```
func lengthOfLongestSubstring(s string) int {
    
    ans := 0
    mapper := make(map[string]int)
    i, j := 0, 0
    for j < len(s) {
        if mapper[s[j:j+1]] != 0 {
            i = findMax(mapper[s[j:j+1]], i)
        }
        ans = findMax(ans, j + 1 - i)
        mapper[s[j:j+1]] = j + 1
        j += 1
    }
    
    return ans
    
}

func findMax(a, b int) int {
    if a < b {
        return b
    }

    return a
```
---

## 动态规划

这个是基本上最重要的思路了

例题：https://leetcode.com/problems/longest-palindromic-substring/

找最大回文数，这个问题可以变成中心店向外扩张的问题
P(i,j) = P(i-1, j+1) (Si == Sj)

而一共可以有2n - 1个中心点（n + n-1）
n: 可以有n个类似aba
n-1:可以有n-1个 类似 abba 这样的


Java

```
public String longestPalindrome(String s) {
    if (s == null || s.length() < 1) return "";
    int start = 0, end = 0;
    for (int i = 0; i < s.length(); i++) {
        int len1 = expandAroundCenter(s, i, i);
        int len2 = expandAroundCenter(s, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}

private int expandAroundCenter(String s, int left, int right) {
    int L = left, R = right;
    while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
        L--;
        R++;
    }
    return R - L - 1;
```

Golang:
```
func longestPalindrome(s string) string {
    ans := ""
    for i, _ := range s {
        st1 := findPalindrome(i, i, s)
        st2 := findPalindrome(i, i+1, s)
        if len(st1) > len(ans) {
            ans = st1
        }
        if len(st2) > len(ans) {
            ans = st2
        }
    }
    
    return ans
}

func findPalindrome(start int, end int, s string) (str string) {
    
    firstHalf := ""
    secondHalf := ""
    for start >= 0 && end < len(s) {
        if s[start] == s[end] {
            if start == end {
                firstHalf += s[start:start+1]
            } else {
                firstHalf = s[start:start+1] + firstHalf
                secondHalf += s[end:end + 1]
            }
            start -= 1
            end += 1
        } else {
            break
        }
    }
    

    
    return firstHalf + secondHalf
}
```

---

### [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

这题不难，但是做的时候 有点想糊涂了，可以以后重温一下

---

### [Combination Sum](https://leetcode.com/problems/combination-sum/)

这题注意：Go 里面的 递归 slice用法

---

[方法二](https://leetcode.com/problems/group-anagrams/solution/)

### [Return an array where the value at an index is the multiplication of all values other than that index of a given array.](https://stackoverflow.com/questions/2680548/given-an-array-of-numbers-return-array-of-products-of-all-other-numbers-no-div)

---

### [Jump Game](https://leetcode.com/problems/jump-game/)

---

### [Merge Interval](https://leetcode.com/problems/merge-intervals/)

---

### [permutation string](https://leetcode.com/problems/permutation-sequence/discuss/224422/a-foolish-solution-by-golang-GO-0ms)

---

### DFS能解 或者 DP 能解 但是需要再多想一点更好的方法

[Climb Stairs](https://leetcode.com/problems/climbing-stairs/)

[Unique Paths](https://leetcode.com/problems/unique-paths)

[Minimum Path](https://leetcode.com/problems/minimum-path-sum/)

---

### [Set Zeros](https://leetcode.com/problems/set-matrix-zeroes/solution/)

---

### [Quick Sort](https://leetcode.com/problems/sort-colors)

---


### [Subset](https://leetcode.com/problems/subsets/)

---

### [Search in rotated array](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)

---

### [Remove Deplicates](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

linklist 结束部分

---

### [Merge Sorted List](https://leetcode.com/problems/merge-sorted-array)

---

### [Subset](https://leetcode.com/problems/subsets-ii)

请看 golang append的时候 变化 27-31行

---

### [Decode Ways](https://leetcode.com/problems/decode-ways/)

---

### [Binary-Tree-Reverse](https://leetcode.com/problems/binary-tree-inorder-traversal)

---

### [Binary Tree Set](https://leetcode.com/problems/unique-binary-search-trees-ii/)

---

### [Check Tree](https://leetcode.com/problems/validate-binary-search-tree)

---

### [Make Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree)

---

### [SortList to Tree](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree)

---

### [Balance Tree](https://leetcode.com/problems/balanced-binary-tree)

---

### [Tree to linkList](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

开始用stack

---

### [Surround Place](https://leetcode.com/problems/surrounded-regions/)

---

### [Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/) 

不难但是需要看最优解

---

### [Single Number](https://leetcode.com/problems/single-number/)

看看bitwise 运算

---

### [Word Break](https://leetcode.com/problems/word-break/)


---

### [Cycle Link](https://leetcode.com/problems/linked-list-cycle-ii)

---

### [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

---

## [Min Stack](https://leetcode.com/problems/min-stack/)

---

### [Excel Sheet Column Title](https://leetcode.com/problems/excel-sheet-column-title/)

---

### [Factorial Trailing Zeroes](https://leetcode.com/problems/factorial-trailing-zeroes/)

DP  能做 但是可以多想想法

---

### [Largest Number](https://leetcode.com/problems/largest-number/)

---

### [Rotate Array](https://leetcode.com/problems/rotate-array/)

看看Slice Copy

---

### [Reverse Bits](https://leetcode.com/problems/reverse-bits/)

位数运算

---

### [Bitewise AND](https://leetcode.com/problems/bitwise-and-of-numbers-range/)

---

### [Maximum Squre](https://leetcode.com/problems/maximal-square/)

---

### [isomorphic-strings](https://leetcode.com/problems/isomorphic-strings)

---

### [Course Schedule](https://leetcode.com/problems/course-schedule)

---

### [Minimum Array Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)

---

### [LCA](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

---

### [Simgle Number III] (https://leetcode.com/problems/single-number-iii/)

---

### [Ugly Number](https://leetcode.com/problems/ugly-number/)

---

### [Squre Number](https://leetcode.com/problems/perfect-squares/)

---

### [Longest Increasing Substring](https://leetcode.com/problems/longest-increasing-subsequence/)

---

### [additive number](https://leetcode.com/problems/additive-number/)

---

### [longest palidormic string](https://leetcode.com/problems/longest-palindromic-substring)

---

### [Best buy and sell with cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

---

### [Minimum Tree Height](https://leetcode.com/problems/minimum-height-trees/)

---

### [Ugly Number](https://leetcode.com/problems/super-ugly-number/)

---

### [Switch Bulb](https://leetcode.com/problems/bulb-switcher/)

---

### [Coin Change](https://leetcode.com/problems/coin-change/)

---

### [Increasing Triplet Subsequence] (https://leetcode.com/problems/increasing-triplet-subsequence/)

---

### [Rob House III](https://leetcode.com/problems/house-robber-iii/)

---

### [Water and Jug Problem](https://leetcode.com/problems/water-and-jug-problem/)

---

### [super pow] (https://leetcode.com/problems/super-pow/)

---

### [guess number high and low](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

---

### [find first uniqchar](https://leetcode.com/problems/first-unique-character-in-a-string/submissions/)

---

### [Longest Substring with At Least K Repeating Characters] (https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/)

---

### 【Binary Watch](https://leetcode.com/problems/binary-watch/)

---

### [Remove K Digits](https://leetcode.com/problems/remove-k-digits/)

---

### [Arithmetic Slices] (https://leetcode.com/problems/arithmetic-slices/)
看DP

---

### [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)

---

### [Minimum Genetic Mutation](https://leetcode.com/problems/minimum-genetic-mutation/)

---

### [Path Sum III](https://leetcode.com/problems/path-sum-iii/)

---

### [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

---

### [Repeated Substring Pattern] (https://leetcode.com/problems/repeated-substring-pattern/)

---

### [CanIWin] (https://leecode.com/problems/can-i-win)

---

## DB SQL

### [Second Highest](https://leetcode.com/problems/second-highest-salary/)

---

java 线程池

blocking queue

---

