# Link
https://leetcode.com/problems/max-area-of-island/description/

# Step1
- Number of Islandsのスタックを用いた方法([前回のPR](https://github.com/aki235/Arai60/pull/17))を少し書き換えればできそう→すぐにACできた
- 再帰だと島の大きさを保持するのが大変そう

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
        
        visited = set()
        max_area = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == 1 and (row, col) not in visited:
                    max_area = max(max_area, get_island_size_from(row, col))
        return max_area
```

# Step2
## 読んだコード


## 感想


# Step3


