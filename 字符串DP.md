## 字符串DP

> 在字符串相关的题目中，有一类是用DP解决的，一般是求一些最长的子序列or子串。这里做一个汇总。

注意

> 🎗️注意：
> 
> - 子串：要求必须是连续的
> 
> - 子序列: 不要求连续，只要求出现顺序和原字符串保持一致

- [72. 编辑距离](https://leetcode.cn/problems/edit-distance/)

> 给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数*  。
> 
> 你可以对一个单词进行如下三种操作：
> 
> - 插入一个字符
> - 删除一个字符
> - 替换一个字符

```bash
示例 1：

输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

```py
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        m = len(word1)
        n = len(word2)
        dp = [[0 for i in range(n+1)]for j in range(m+1)]
        for i in range(1, m+1):
            dp[i][0] = dp[i-1][0] + 1
        for j in range(1, n+1):
            dp[0][j] = dp[0][j-1] + 1
        for i in range(1, m+1):
            for j in range(1, n+1):
                if word1[i-1] == word2[j-1]:
                    dp[i][j] = dp[i-1][j-1]
                else:
                    dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
        return dp[m][n]
```

- [516. 最长回文子序列](https://leetcode.cn/problems/longest-palindromic-subsequence/)

> 给你一个字符串 s ，找出其中最长的回文子序列，并返回该序列的长度。
> 
> 子序列定义为：不改变剩余字符顺序的情况下，删除某些字符或者不删除任何字符形成的一个序列。

```bash
示例 1：

输入：s = "bbbab"
输出：4
解释：一个可能的最长回文子序列为 "bbbb" 。
```

```py

```

- [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

> 给你一个字符串 `s`，找到 `s` 中最长的 回文子串

```bash
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

```py
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        dp = [[0] * n for i in range(n)]
        res = 0
        for end in range(n):
            for start in range(end, -1, -1):
                if s[start] == s[end] and (end - start < 2  or dp[start+1][end-1]):
                    dp[start][end] = 1
                    if res < end - start + 1:
                        res = max(res, end - start + 1)
                        sub_str = s[start: end+1]
        return sub_str
```

- [1143. 最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)

> 给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。

```bash
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace" ，它的长度为 3 。
```

```py
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m = len(text1)
        n = len(text2)
        # dp[i][j] 表示text1[0,i]范围和text2[0,j]范围构成的最长公共子序列。
        dp = [[0 for i in range(n+1)] for j in range(m+1)]
        for i in range(1, m+1):
            for j in range(1, n+1):
                if text1[i-1] == text2[j-1]:
                    dp[i][j] = dp[i-1][j-1] + 1
                else:
                    dp[i][j] = max(dp[i][j-1], dp[i-1][j])
        return dp[m][n]
```

- 最长公共子串

> 给定两个字符串 `str1` 和 `str2`，返回这两个字符串的最长 **公共子串** 的长度。

```bash
str1 = "abcdefg"
str2 = "bcdefgh"

ans = "bcdefg"
```

```py
def longest_common_substring(str1, str2):
    m = len(str1)
    n = len(str2)
    # dp[i][j]以str1[i]和str2[j]结尾的的最长公共子串
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    max_len = 0
    end_index = 0
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
                if dp[i][j] > max_len:
                    max_len = dp[i][j]
                    end_index = i
    return str1[end_index - max_len:end_index]
```
