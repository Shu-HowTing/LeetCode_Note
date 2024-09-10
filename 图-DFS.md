## 图的DFS

leetcode有一些题型，是在一个二位矩形中寻找最优解，这一类题目通常涉及到图的DFS算法，且经常是用回溯的思路那个来解决，这里做一些记录和总结。

### [单词搜索](https://leetcode-cn.com/problems/word-search/)

> 给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

![img](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)

> 输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
> 输出：true

```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        self.m = len(board)
        self.n = len(board[0])
        self.direct = [[0, 1], [0, -1], [1, 0], [-1, 0]]
        self.len = len(word)
        mask = [[0] * self.n for _ in range(self.m)]
        for i in range(self.m):
            for j in range(self.n):
                # 分别以board的每一个元素为首元素，进行探索，尝试周到一条复合题意的路径，只要找到一条就结束，返回True
                if self.backtrack(board, mask, i, j, 0, word):
                    return True
        # 走到这一步，说明全都都尝试了，找不到，返回False
        return False

    '''基本描述

    回溯函数，返回是否能找到这样一条路径：以board[i][j]为首元素，向四个方向遍历，能否找到一条路径, 组成的单词与word[k:]对应

    Args:
        board (List[List[str]]): 二维网格
        mask (List[List[int]]): 二维记录板，mask[i][j]记录了board对应位置的元素board[i][j]是否已经被遍历过, 0：没有探索过 1:已经探索过
        i(int): 当前正在遍历的元素的行
        j(int): 当前正在遍历的元素的列
        k(int): 当前需要处理的字符word[j]的下标
        word(str): 单词

    Returns:
        True/False: 代表以当前元素board[i][j]为首元素，是否存在一条路径, 与单词word[k:]完美对应
    '''
    def backtrack(self, board, mask, i, j, k, word):
        if k == self.len - 1:
            return word[k] == board[i][j]
        if board[i][j] == word[k]:
            mask[i][j] = 1
            # 以board[i][j]为起始点，开始向四个方向探索
            for d in self.direct:
                cur_i = i + d[0]
                cur_j = j + d[1]
                # 不越界 且 满足单词的字符
                if 0 <= cur_i < self.m and 0 <= cur_j < self.n:
                    # 如果mask[cur_i][cur_j]=1，说明board[cur_i][cur_j]已经在路径中了, 再遍历一次路径就成环了
                    # 所以跳过，转而探索其他方向
                    if mask[cur_i][cur_j] == 1:
                        continue
                    if self.backtrack(board, mask, cur_i, cur_j, k+1, word):
                        return True
            # 四个方向都探索一次后，发现都不满足，即以当前元素board[i][j]为首元素，不存在这样一条路径, 与单词word[k:]完美对应
            # 也就是返回False，接下来回到主函数(exist)中，继续探索以board[i][j+1]为首元素，路径是否存在
            # 但在此之前需要先把mask[i][j]=0，回复原来的状态
            mask[i][j] = 0
        return False
```

### [N 皇后](https://leetcode-cn.com/problems/n-queens/)

> n 皇后问题 研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。
> 
> 给你一个整数 n ，返回所有不同的 n 皇后问题 的解决方案。

![img](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

> ```bash
> 输入：n = 4
> 输出：[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
> ```

```python
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        board = [['.'] * n for _ in range(n)]
        self.result = []
        self.backtrack(board, 0, n)
        return self.result
    # 判断board[i][j]能否放置棋子
    def is_valid(self, board, row, col):
        n = len(board)
        # 判断列
        for i in range(row, -1, -1):
            if board[i][col] == 'Q':
                return False
        # 判断左上斜线
        j = col
        for i in range(row, -1, -1):
            if j>= 0 and board[i][j] == 'Q':
                return False
            j -= 1
        # 判断右上斜线
        j = col
        for i in range(row, -1, -1):
            if j < n and board[i][j] == 'Q':
                return False
            j += 1
        return True

    def backtrack(self, board, row, n):
        if row == n:
            self.result.append([''.join(board[i]) for i in range(n)])
            return 

        for col in range(n):
            if not self.is_valid(board, row, col):
                continue
            board[row][col] = 'Q'
            self.backtrack(board, row + 1, n)
            board[row][col] = '.'
```

### [解数独](https://leetcode-cn.com/problems/sudoku-solver/)

> 编写一个程序，通过填充空格来解决数独问题。
> 
> 数独的解法需 遵循如下规则：
> 
> - 数字 1-9 在每一行只能出现一次。
> - 数字 1-9 在每一列只能出现一次。
> - 数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。（请参考示例图）
> - 数独部分空格内已填入了数字，空白格用 '.' 表示。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/04/12/250px-sudoku-by-l2g-20050714svg.png)

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        # row_used[i][num]: 代表board的第i行， num这个数字是否已经被用过了
        # col_used[j][num]: 代表board的第j列， num这个数字是否已经被用过了
        # box_used[k][num]: 代表board的第k个3*3小宫格中(0~8,一共九个)， num这个数字是否已经被用过了
        row_used = [[False] * 9 for _ in range(9)]
        col_used = [[False] * 9 for _ in range(9)]
        box_used = [[False] * 9 for _ in range(9)]
        for row in range(9):
            for col in range(9):
                if board[row][col] != '.':
                    num = ord(board[row][col]) - ord('1')
                    k = row // 3 * 3  + col // 3
                    row_used[row][num] = True
                    col_used[col][num] = True
                    box_used[k][num] = True

        self.backtrack(board, row_used, col_used, box_used, 0) 


    def backtrack(self, board, row_used, col_used, box_used, n):
        if n == 81:
            return True
        row = n // 9
        col = n % 9
        if board[row][col] != '.':
            return self.backtrack(board, row_used, col_used, box_used, n+1)
        k = row // 3 * 3  + col // 3
        for num in range(1, 10):
            if row_used[row][num-1] or col_used[col][num-1] or box_used[k][num-1]:
                continue
            row_used[row][num-1] = True
            col_used[col][num-1] = True
            box_used[k][num-1] = True
            # 做出选择
            board[row][col] = str(num)
            if self.backtrack(board, row_used, col_used, box_used, n+1):
                return True
            row_used[row][num-1] = False
            col_used[col][num-1] = False
            box_used[k][num-1] = False
            # 撤销选择
            board[row][col] = '.'
        return False
```

### [矩阵中的最长递增路径](https://leetcode-cn.com/problems/longest-increasing-path-in-a-matrix/)

> 给定一个 m x n 整数矩阵 matrix ，找出其中 最长递增路径 的长度。
> 
> 对于每个单元格，你可以往上，下，左，右四个方向移动。 你 不能 在 对角线 方向上移动或移动到 边界外（即不允许环绕）。

![img](https://assets.leetcode.com/uploads/2021/01/05/grid1.jpg)

> ```bash
> 输入：matrix = [[9,9,4],[6,6,8],[2,1,1]]
> 输出：4 
> 解释：最长递增路径为 [1, 2, 6, 9]。
> ```

```python
class Solution:
    def longestIncreasingPath(self, matrix: List[List[int]]) -> int:
        m = len(matrix)
        n = len(matrix[0])
        # memo_table[i][j]记录了以matrix[i][j]为首元素的最长递增路径的长度
        memo_table = [[0] * n for _ in range(m)]
        result = 0
        for row in range(m):
            for col in range(n):
                # 遍历矩阵每个位置深度搜索，result记录最大值，
                result = max(result, self.backtrack(matrix, memo_table, row, col, m, n))
        return result

    def backtrack(self, matrix, memo_table, row, col, m, n):
        if memo_table[row][col]:
            return memo_table[row][col]
        directs = [[0, -1], [0, 1], [1, 0], [-1, 0]]
        result = 0
        for d in directs:
            cur_row = row + d[0]
            cur_col = col + d[1]
            # 这里不需要mask来记录当前路径上哪些元素已经使用了，因为matrix[cur_row][cur_col] > matrix[row][col]这个条件
            # 会阻断路径成环的可能，所以不用担心
            if  0 <= cur_col < n and 0 <= cur_row < m and matrix[cur_row][cur_col] > matrix[row][col]:
                memo_table[row][col] = max(self.backtrack(matrix, memo_table, cur_row, cur_col, m, n) + 1, memo_table[row][col])

            else:
                # self.backtrack(matrix, memo_table, cur_row, cur_col, m, n) = 0
                memo_table[row][col] = max(memo_table[row][col], 1)

        return memo_table[row][col]
```

### 最长不重复路径

> 给定一个 m x n 整数矩阵 matrix，每个元素都是一个字母，需要你找到路径，使得这条路径上的字母都不重复，请问这个最长的路径是多长
> 
> 对于每个单元格，你可以往上，下，左，右四个方向移动。 你不能在对角线方向上移动或移动到边界外（即不允许环绕）。

```python
class Solution:
    def longestUniquePath(self, matrix) -> int:
        m = len(matrix)
        n = len(matrix[0])
        mask = [[0] * n for _ in range(m)]
        result = 0
        for row in range(m):
            for col in range(n):
                result = max(result, self.backtrack(matrix, mask, row, col, m, n, []))
        return result

    def backtrack(self, matrix, mask, row, col, m, n, path):
        directs = [[0, -1], [0, 1], [1, 0], [-1, 0]]
        result = 0
        if matrix[row][col] not in path:
            path.append(matrix[row][col])
            mask[row][col] = 1
            for d in directs:
                cur_row = row + d[0]
                cur_col = col + d[1]
                if  0 <= cur_col < n and 0 <= cur_row < m and matrix[cur_row][cur_col] != matrix[row][col]:
                    if mask[cur_row][cur_col] == 1:
                        continue
                    result = max(self.backtrack(matrix, mask, cur_row, cur_col, m, n, path) + 1, result)
                else:
                    result = max(result, 1)
            mask[row][col] = 0
        # else:
        #     return  0
        return result
```

- [130. 被围绕的区域](https://leetcode.cn/problems/surrounded-regions/)

> 给你一个 `m x n` 的矩阵 `board` ，由若干字符 `'X'` 和 `'O'` 组成，**捕获** 所有 **被围绕的区域**：
> 
> - **连接：**一个单元格与水平或垂直方向上相邻的单元格连接。
> - **区域：连接所有** `'O'` 的单元格来形成一个区域。
> - **围绕：**如果您可以用 `'X'` 单元格 **连接这个区域**，并且区域中没有任何单元格位于 `board` 边缘，则该区域被 `'X'` 单元格围绕。
> 
> 通过将输入矩阵 `board` 中的所有 `'O'` 替换为 `'X'` 来 **捕获被围绕的区域**。

> **示例 1：**
> 
> **输入：**board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
> 
> **输出：**[["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
> 
> **解释：**
> 
> ![](https://pic.leetcode.cn/1718167191-XNjUTG-image.png)
> 
> 在上图中，底部的区域没有被捕获，因为它在 board 的边缘并且不能被围绕。

> 这类问题都需要原地替换，即空间复杂度为O(1). 一般都是用一个不冲突的字符串先占位，然后再改为符合题意的字符串

```py
class Solution:
    def solve(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
        m, n = len(board), len(board[0])
        def dfs(x: int, y: int):
            if x in [-1, m] or y in [-1, n] or board[x][y] != 'O':
                return
            board[x][y] = 'V' # visited
            for dx, dy in directions:
                dfs(x + dx, y + dy)


        # 处理边缘的'O'
        # 把不能替换成'X'的'0'先用'V'占位
        for i in range(m):
            for j in [0, n - 1]:
                if board[i][j] == 'O':
                    dfs(i, j)
        for i in [0, m - 1]:
            for j in range(n):
                if board[i][j] == 'O':
                    dfs(i, j)
    
        for i, j in product(range(m), range(n)):
            # V -> O; O -> X 
            board[i][j] = 'O' if board[i][j] == 'V' else 'X'
```
