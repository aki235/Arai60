# Link
https://leetcode.com/problems/number-of-islands/description/



# Step1
- まずは入力(grid)は書き換えない方針で取り組んでみる
    - この関数を使う場面として、例えば島の数が多いものだけをフィルタリングすることが想定される
        - その場合、書き換えてしまうと、後からgridの中身を見たい時に困る
    - 入力を破壊してよいなら、他の方法も取れそう。後で考えてみる。
- 訪問済みのマスを記録しておくのがよさそう
    - 島を見つけたら上下左右に探索する。訪問済みのマスや、マップの範囲から外れるものがあればスキップ(早期リターン)する
- キューとかも使えそう？
- コードを書くときに、自分がしたことの理由を説明できるようにしたいなと思った。知識が全然足りていないので、少しずつですが...

解答1
```Python3
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        visited_grid = [[False] * len(grid[0]) for _ in range(len(grid))]
        def search_island(i, j):
            if not 0 <= i < len(grid) or not 0 <= j < len(grid[0]):
                return 0
            if visited_grid[i][j]:
                return 0
            if grid[i][j] != "1":
                visited_grid[i][j] = True
                return 0
            
            visited_grid[i][j] = True
            search_island(i - 1, j)
            search_island(i + 1, j)
            search_island(i, j - 1)
            search_island(i, j + 1)
            return 1

        land_count = 0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                land_count += search_island(i, j)
        
        return land_count
```

# Step2
## 読んだコード
- https://github.com/olsen-blue/Arai60/pull/17
- https://github.com/Mike0121/LeetCode/pull/34
- https://github.com/tshimosake/arai60/pull/8
- https://github.com/ryosuketc/leetcode_arai60/pull/17
- https://github.com/Fuminiton/LeetCode/pull/17#discussion_r1984361170

## 感想
- 入力を書き換えるものも多かったので、練習として書いた(解答2)
- 関数のネストについて
    - https://github.com/ryosuketc/leetcode_arai60/pull/17
    - [Googleのスタイルガイド](https://google.github.io/styleguide/pyguide.html#26-nestedlocalinner-classes-and-functions)
        - 関数のネスト(関数の中に関数を作ること)は、変数をクロージャで使いたいい場合はOK
            - クロージャとは: 関数+周辺環境(引数以外の変数)の組み合わせ
              ```Python3
              def make_adder(base):
                  def add(x):
                      return base + x
                  return add
              ```
              ↑baseはaddの引数ではないが、返り値に影響
        - ただし、関数を隠すためにネストするのはよくない
            - デバッグしたいときに外部から呼び出せなくなるから
    - この問題では、gridやvisitedをサブ関数内で使いたいから、ネストが適切
- 深さ優先探索: 再帰かスタックか
    - Step1では、再帰を使ってDFSを書いている。
        - Pythonのrecursion_limitは1000だが、入力の大きさはm,n <= 300なので、300*300=90000くらいまでありうる
    - スタックを使って実装した(解答3)
    - 再帰上限を書き換える方法もある
        - `sys.setrecursionlimit(900000)`
        - 可読性が高いが、クラッシュの恐れもある
    - ただ、leetcodeのテストだと再帰上限には引っかからないので、恩恵や危険性があまり実感できていない。時間があるときに自分で実験してみたい
- 変数名
    - `i, j`より`row, column`の方がわかりやすい。[コメント](https://github.com/Mike0121/LeetCode/pull/34#discussion_r1658161491)
    - `0, 1`を`WATER, ISLAND`に置き換えても良い。[コメント](https://github.com/tshimosake/arai60/pull/8#discussion_r1650065276)
- 関数呼び出し
    - https://github.com/tshimosake/arai60/pull/8#discussion_r1650349436
    - 関数呼び出しが増えてもそこまでパフォーマンスには影響しない
    - [C function direct call](http://ithare.com/infographics-operation-costs-in-cpu-clock-cycles/)は15-30 CPU cyclesなので、数十nsくらいか
- if文の書き方
    - https://github.com/tshimosake/arai60/pull/8#discussion_r1650066371
        - `not A or not B`と`not (A and B)`は好みの問題っぽい
    - 条件式が長い場合
        - バックスラッシュによる行分割はNG([スタイルガイド](https://google.github.io/styleguide/pyguide.html#32-line-length))
        - Pythonは`(),[],{}`の中で改行するのがよさそう、もしくはif文を分割    
- Union-find
    - > union-find  は、微妙に常識から外れるかな(多くの人が知っているだろうが知らなくてもドン引きはされない)、くらいの感覚です。DFS による解法のほうは常識でしょう。
    - https://discord.com/channels/1084280443945353267/1183683738635346001/1197738650998415500

解答1 (再帰、入力を書き換えない)
```Python3
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def search_island(row, col):
            if not 0 <= row < len(grid) or not 0 <= col < len(grid[0]):
                return 0
            if (row, col) in visited:
                return 0
            visited.add((row, col))
            if grid[row][col] == "0":
                return 0
            
            search_island(row - 1, col)
            search_island(row + 1, col)
            search_island(row, col - 1)
            search_island(row, col + 1)
            return 1
        
        visited = set()
        island_count = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                island_count += search_island(row, col)
        
        return island_count
```

解答2 (再帰、入力を書き換え)
```Python3
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def replace_island(row, col):
            if (not 0 <= row < len(grid) or not 0 <= col < len(grid[0])
            or grid[row][col] == "0"):
                return
            
            grid[row][col] = "0"
            replace_island(row - 1, col)
            replace_island(row + 1, col)
            replace_island(row, col - 1)
            replace_island(row, col + 1)

        island_count = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == "1":
                    replace_island(row, col)
                    island_count += 1

        return island_count
```

解答3 (スタック)
```Python3
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def add_island_to_visited(row, col):
            cells_to_visit = [(row, col)]
            while cells_to_visit:
                row, col = cells_to_visit.pop()
                
                if not (0 <= row < len(grid) and 0 <= col < len(grid[0])):
                    continue
                if (row, col) in visited:
                    continue
                visited.add((row, col))
                if grid[row][col] != "1":
                    continue
                
                cells_to_visit.append((row - 1, col))
                cells_to_visit.append((row + 1, col))
                cells_to_visit.append((row, col - 1))
                cells_to_visit.append((row, col + 1))
        
        visited = set()
        island_count = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == "1" and (row, col) not in visited:
                    add_island_to_visited(row, col)
                    island_count += 1
        
        return island_count
```

|  | アルゴリズム | 実装 | 入力の破壊 |
| ---- | ---- | ---- | ---- |
| 解答1 | DFS | 再帰 | 破壊しない |
| 解答2 | DFS | 再帰 | 破壊する |
| 解答3 | DFS | スタック | 破壊しない |




# Step3
- 解答3で最終確認
    - add_island_to_visitedの最初のif文を、`not A or not B`から`not (A and B)`に変更。前者で何回かミスをしてしまって、後者の方が読みやすく感じた

解答3 (スタック)
```Python3
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def add_island_to_visited(row, col):
            cells_to_visit = [(row, col)]
            while cells_to_visit:
                row, col = cells_to_visit.pop()

                if not (0 <= row < len(grid) and 0 <= col < len(grid[0])):
                    continue
                if (row, col) in visited:
                    continue
                visited.add((row, col))
                if grid[row][col] != "1":
                    continue
                
                cells_to_visit.append((row - 1, col))
                cells_to_visit.append((row + 1, col))
                cells_to_visit.append((row, col - 1))
                cells_to_visit.append((row, col + 1))
        
        visited = set()
        island_count = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == "1" and (row, col) not in visited:
                    add_island_to_visited(row, col)
                    island_count += 1
        return island_count
```