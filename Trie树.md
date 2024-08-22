## Trie树

<img title="" src="https://oi-wiki.org/string/images/trie1.png" alt="trie1" width="625">

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end

    def startsWith(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

[1233. 删除子文件夹](https://leetcode.cn/problems/remove-sub-folders-from-the-filesystem/)

> 你是一位系统管理员，手里有一份文件夹列表 `folder`，你的任务是要删除该列表中的所有 **子文件夹**，并以 **任意顺序** 返回剩下的文件夹。
> 
> 如果文件夹 `folder[i]` 位于另一个文件夹 `folder[j]` 下，那么 `folder[i]` 就是 `folder[j]` 的 **子文件夹** 。
> 
> 文件夹的「路径」是由一个或多个按以下格式串联形成的字符串：'/' 后跟一个或者多个小写英文字母。
> 
> - 例如，`"/leetcode"` 和 `"/leetcode/problems"` 都是有效的路径，而空字符串和 `"/"` 不是。
> 
> **例1**
> 
> **输入：** folder = ["/a","/a/b","/c/d","/c/d/e","/c/f"]
> **输出：**["/a","/c/d","/c/f"]
> **解释：**"/a/b" 是 "/a" 的子文件夹，而 "/c/d/e" 是 "/c/d" 的子文件夹。

```py
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Solution:
    def __init__(self):
        self.root = TrieNode()

    def add(self, path):
        node = self.root
        parts = path.split('/')
        for part in parts[1:]:  # Skip the first empty string from split
            if part not in node.children:
                node.children[part] = TrieNode()
            node = node.children[part]
        node.is_end = True

    def is_subfolder(self, path):
        node = self.root
        parts = path.split('/')
        for part in parts[1:]:  # Skip the first empty string from split
            if node.is_end:
                return True
            if part not in node.children:
                return False
            node = node.children[part]
        return False

    def removeSubfolders(self, folder):
        # Add all folders to the Trie
        for f in folder:
            self.add(f)
        # Collect non-subfolder paths
        ans = [f for f in folder if not self.is_subfolder(f)]
        return ans

# Example usage:
sol = Solution()
folders = ["/a", "/a/b", "/c/d", "/c/d/e", "/c/f"]
print(sol.removeSubfolders(folders))
```
