# Link
https://leetcode.com/problems/max-area-of-island/description/

# Step1
- Number of Islandsのスタックを用いたDFS([前回のPR](https://github.com/aki235/Arai60/pull/17))を少し書き換えればできそう→AC
- 再帰だと島の大きさを保持するのが大変そう
- Step1からStep2で3週間くらい空いてしまったので、何も見ないでコードを再現してみたが、変数名以外できていた。流れさえ思い出せれば、意外とできるものなのだと思った。

解答1 (スタックによるDFS)
```Python3
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        def get_island_size_from(row, col):
            cells_to_visit = [(row, col)]
            island_size = 0
            while cells_to_visit:
                row, col = cells_to_visit.pop()
                if not (0 <= row < len(grid) and 0 <= col < len(grid[0])):
                    continue
                if grid[row][col] != 1:
                    continue
                if (row, col) in visited:
                    continue

                visited.add((row, col))
                island_size += 1
                cells_to_visit.append((row - 1, col))
                cells_to_visit.append((row + 1, col))
                cells_to_visit.append((row, col - 1))
                cells_to_visit.append((row, col + 1))
            return island_size
        
        visited: set[tuple[int, int]] = set()
        max_area = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == 1 and (row, col) not in visited:
                    max_area = max(max_area, get_island_size_from(row, col))
        return max_area
```

# Step2
## 読んだコード
- https://github.com/olsen-blue/Arai60/pull/18
- https://github.com/ryosuketc/leetcode_arai60/pull/18
- https://github.com/PafsCocotte/leetcode/pull/2
- https://github.com/potrue/leetcode/pull/17
- https://github.com/n6o/leetcode_arai60/pull/18


## 感想
- 深さ優先探索(再帰、スタック)、幅優先探索(キュー)を使ったものが多かった
    - 練習のため、再帰による深さ優先探索(解答2)も書いてみた
    - 幅優先は解答1のdequeを利用して、`cells_to_visit.pop()`を`cells_to_visit.popleft()`に置き換えればできる
- 探索方向をリストで管理する方法([コメント](https://github.com/aki235/Arai60/pull/17#discussion_r2805519916))を練習してみた(解答3)
- その他参考になったコメント
    - recursionlimitを使わない方がいい場合
        - https://github.com/olsen-blue/Arai60/pull/18#discussion_r1919805259
    - 変数名は動詞の原型から始めることが多い
        - https://github.com/PafsCocotte/leetcode/pull/2#discussion_r2735732347
    - スコープとメンタルモデル、ドキュメントを見に行くモチベーション
        - https://github.com/potrue/leetcode/pull/17#discussion_r2103927399
    - 変数名の案
        - https://github.com/Hiroto-Iizuka/coding_practice/pull/18#discussion_r2725958263
        - `frontier`、`coordinate_to_visit`など
            - 自分は暫定的に`cells_to_visit`にしていたが、あまりしっくりきていないので、上記の方が良さそうに感じた

解答2 (再帰によるDFS)
```Python3
class Solution:
    from collections import deque
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        def count_island_size_from(row, col):
            if not (0 <= row < len(grid) and 0 <= col < len(grid[0])):
                return 0
            if grid[row][col] != 1:
                return 0
            if (row, col) in visited:
                return 0

            visited.add((row, col))
            return (
            1 + count_island_size_from(row - 1, col)
            + count_island_size_from(row + 1, col)
            + count_island_size_from(row, col - 1)
            + count_island_size_from(row, col + 1)
            )

        visited: set[tuple[int, int]] = set()
        max_island_size = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == 1 and (row, col) not in visited:
                    max_island_size = max(max_island_size, count_island_size_from(row, col))
        return max_island_size
```

解答3 (スタックによるDFS、探索方向をリストで管理)
```Python3
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        def get_island_size_from(row, col):
            cells_to_visit = [(row, col)]
            island_size = 0
            while cells_to_visit:
                row, col = cells_to_visit.pop()
                if not (0 <= row < len(grid) and 0 <= col < len(grid[0])):
                    continue
                if grid[row][col] != 1:
                    continue
                if (row, col) in visited:
                    continue

                visited.add((row, col))
                island_size += 1
                deltas = [(0, 1), (0, -1), (1, 0), (-1, 0)]
                for d in deltas:
                    new_row = row + d[0]
                    new_col = col + d[1]
                    cells_to_visit.append((new_row, new_col))
            return island_size
        
        visited: set[tuple[int, int]] = set()
        max_area = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == 1 and (row, col) not in visited:
                    max_area = max(max_area, get_island_size_from(row, col))
        return max_area
```

# Step3
- 解答3(スタックによるDFS)で最終確認
    - 変数名、型注釈をStep2から修正/追加
    - 型注釈を初めて使ったが、人にとって読みやすいだけでなく、書き手としても理解が深まると感じた

解答3
```Python3
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        def get_island_size_from(row, col):
            coordinate_to_visit: list[tuple[int, int]] = [(row, col)]
            island_size = 0
            while coordinate_to_visit:
                row, col = coordinate_to_visit.pop()
                if not (0 <= row < len(grid) and 0 <= col < len(grid[0])):
                    continue
                if grid[row][col] != 1:
                    continue
                if (row, col) in visited:
                    continue
                
                visited.add((row, col))
                island_size += 1
                deltas = [(0, 1), (0, -1), (1, 0), (-1, 0)]
                for d in deltas:
                    new_row = row + d[0]
                    new_col = col + d[1]
                    coordinate_to_visit.append((new_row, new_col))
            return island_size
        
        visited: set[tuple[int, int]] = set()
        max_area = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == 1 and (row, col) not in visited:
                    max_area = max(max_area, get_island_size_from(row, col))
        return max_area
```
