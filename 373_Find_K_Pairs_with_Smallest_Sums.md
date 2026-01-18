# Link
https://leetcode.com/problems/find-k-pairs-with-smallest-sums/description/

# Step1
- ペアの合計値をヒープに入れておき、popするごとに次のペアをヒープに追加していく、という方法でできそう
    - 次のペア: nums1もしくはnums2どちらかのインデックスを+1したもの
        - これだと、任意のペアに対して2通りの辿り着き方があり、答えが重複してしまうので、既知のペアをvisitedというsetで管理することにした。一度訪問したペアはヒープへ追加しない
            - visitedの分、空間計算量が増えてしまうのが気になる。

- 上記を元に書き、AC

解答1
```Python3
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        sum_and_pair_heap = [(nums1[0] + nums2[0], 0, 0)]
        visited = set()

        result = []

        for i in range(k):
            sum_of_pairs, index_u, index_v = heapq.heappop(sum_and_pair_heap)
            result.append([nums1[index_u], nums2[index_v]])

            if (index_u + 1, index_v) not in visited and index_u + 1 < len(nums1):
                heapq.heappush(sum_and_pair_heap, (nums1[index_u + 1] + nums2[index_v], index_u + 1, index_v))
                visited.add((index_u + 1, index_v))
            
            if (index_u, index_v + 1) not in visited and index_v + 1 < len(nums2):
                heapq.heappush(sum_and_pair_heap, (nums1[index_u] + nums2[index_v + 1], index_u, index_v + 1))
                visited.add((index_u, index_v + 1))
            
        return result
```

# Step2

## 読んだコード
https://github.com/olsen-blue/Arai60/pull/10
https://github.com/hayashi-ay/leetcode/pull/66
https://github.com/ryosuketc/leetcode_arai60/pull/10
https://github.com/Fuminiton/LeetCode/pull/10

## 感想
- x=0を先にヒープに追加する方法
    - https://discord.com/channels/1084280443945353267/1192736784354918470/1220669329335648346
        - 2次元のテーブルだとして、解答1の方法では、下方向と右方向に探索しているが、最初に一番左の列を全部キューに追加しておけば、右方向の探索のみで済む
    - visitedを使って管理する必要がなくなる
- pushの関数化
    - インデックスが正しい範囲にあるかの確認、ヒープへのpush、(visitedへの追加)を別の関数にまとめているコードが多かった。こちらの方がわかりやすいと感じた。
- heapが空になった時の処理
    - https://github.com/Fuminiton/LeetCode/pull/10#discussion_r1967051129
    - https://discord.com/channels/1084280443945353267/1235971495696662578/1240949381121839115
    - heapが空なのにpopしようとしたら、pythonのheapq.heappopはIndexErrorを出す
        - https://docs.python.org/3/library/heapq.html#heapq.heappop
    - エラーメッセージを書くか、またはフォールバックとしてある分だけ結果を返すのがよい
- 変数名の修正
    - index_u, index_v -> i, j
    - result -> k_smallest_pairs
    - sum_and_pair_heap -> sum_and_pair_index_heap
    - push_pair -> push_sum_and_pair_index



解答1 (修正版)
```Python3
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        sum_and_pair_index_heap = []
        visited = set()
        k_smallest_pairs = []

        def push_sum_and_pair_index(i, j):
            if i < len(nums1) and j < len(nums2) and (i, j) not in visited:
                heapq.heappush(sum_and_pair_index_heap, (nums1[i] + nums2[j], i, j))
                visited.add((i, j))

        push_sum_and_pair_index(0, 0)
        while sum_and_pair_index_heap and len(k_smallest_pairs) < k:
            _, i, j = heapq.heappop(sum_and_pair_index_heap)
            k_smallest_pairs.append([nums1[i], nums2[j]])

            push_sum_and_pair_index(i + 1, j)
            push_sum_and_pair_index(i, j + 1)
        
        return k_smallest_pairs
```

解答2 (x=0を先に追加する方法)
```Python3
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        sum_and_pair_index_heap = []
        k_smallest_pairs = []

        def push_sum_and_pair_index(i, j):
            if i < len(nums1) and j < len(nums2):
                heapq.heappush(sum_and_pair_index_heap, (nums1[i] + nums2[j], i, j))

        for j in range(len(nums2)):
            push_sum_and_pair_index(0, j)
            if j >= k:
                break

        while sum_and_pair_index_heap and len(k_smallest_pairs) < k:
            _, i, j = heapq.heappop(sum_and_pair_index_heap)
            k_smallest_pairs.append([nums1[i], nums2[j]])

            if len(nums1) > i + 1:
                push_sum_and_pair_index(i + 1, j)
        
        return k_smallest_pairs
```

# Step3
解答1を元に最終確認

解答1 (修正版)
```Python3
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        sum_and_pair_index_heap = []
        visited = set()
        k_smallest_pairs = []

        def push_sum_and_pair_index(i, j):
            if i < len(nums1) and j < len(nums2) and (i, j) not in visited:
                heapq.heappush(sum_and_pair_index_heap, (nums1[i] + nums2[j], i, j))
                visited.add((i, j))

        push_sum_and_pair_index(0, 0)
        while sum_and_pair_index_heap and len(k_smallest_pairs) < k:
            _, i, j = heapq.heappop(sum_and_pair_index_heap)
            k_smallest_pairs.append([nums1[i], nums2[j]])

            push_sum_and_pair_index(i + 1, j)
            push_sum_and_pair_index(i, j + 1)
        
        return k_smallest_pairs
```
