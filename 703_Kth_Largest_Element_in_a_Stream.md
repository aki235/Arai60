# Link


# Step1
- 単純な解法としては、addが実行されるごとにnumsをソートして、k番目を返せば良いが、それだと不要な計算が多い
    - k番目に大きい数さえ求めればいいので、numsは長さkまで切り詰められそう
    - また、1回のadd呼び出しでは、1個しか数を追加しないので、ソートもする必要がなく、valとリストの要素を先頭から大小比較を行い、適切な位置に追加してあげればO(N)で済む

- 例外処理も考えた方がいいが、コードが簡潔にするためには、あらかじめ処理の順序を考えた方がよさそう
    - 例外処理: 与えられたリストがkより小さかった場合の処理(※leetcodeではこのようなケースはないが、考慮するべきだと思ったため)

- 流れとしては、以下を考えた。
    - 方針: numsを、常に(新規作成/編集するごとに)、降順に並んでおり、かつ長さがk以下になるように維持する
    - __init__関数における初期化時には、numsをソートし、k個以下になるように切り詰める
    - add関数では、
        - まず、valをリストの大小関係を保つ位置に追加する。
        - 次に、k以下になるように、リストの切り詰めを行う。
        - 答えを返す
            - リストの長さがkより小さかった場合、Noneを返す
            - それ以外の場合、リストの最後の要素を返す

- 下記のコードでAC
    - ただ、時間、空間計算量ともに平均よりかなり多くかかっている

```Python3
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums_sorted = sorted(nums, reverse=True)

        if len(self.nums_sorted) > k:
            self.nums_sorted = [self.nums_sorted[i] for i in range(k)]
        return None

    def add(self, val: int) -> int:
        for i in range(len(self.nums_sorted)):
            if val > self.nums_sorted[i]:
                self.nums_sorted.insert(i, val)
                break
        if not self.nums_sorted or val <= self.nums_sorted[-1]:
            self.nums_sorted.append(val)
        
        while len(self.nums_sorted) > self.k:
            self.nums_sorted.pop()
        
        if len(self.nums_sorted) < self.k:
            return None
```


# Step2
- [他の方の解答](https://github.com/olsen-blue/Arai60/pull/8)を見る
    - heapqを使えば、わざわざソートする必要はない
        - heapqの優先度付きキューは、完全ソートされているわけではないが、先頭の要素が常に最小であることは保証されている
    - リストをheapqのキューに変換する方法
        - 1. `heapq.heappush`をすれば、それ以降はキューになる
        - 2. 最初に`heapq.heapify`を行う
    - `self.add(num)`でもできるのか。なるほど。([参考コメント](https://github.com/olsen-blue/Arai60/pull/8))

- [こちらの解答](https://github.com/hayashi-ay/leetcode/pull/54)では、要素追加 & k個以下へのキープを、関数化している。これもありだと思った
    - `_add_while_keeping_num_of_items`という命名が、何をしているかわかりやすかった。
        - [スタイルガイド](https://google.github.io/styleguide/pyguide.html#3162-naming-conventions)によると、先頭に`_`をつけると、モジュール内部の保護された関数の意になるらしい。

- 上記を参考にコードを修正
    - 変数名
        - kはそのまま変えない方がよさそう？
        - [コメント](https://github.com/olsen-blue/Arai60/pull/8#discussion_r1909062434)を参考に、キューはtop_k_heapとした
    - 補助関数は、どこまで一般化した方がいいのだろうか
        - リンク先では、self.top_k_heapは引数に取らず、クラス内の変数として利用しているが、self.kは引数に取っている
            - 本来は編集対象とするheapも引数とするべき？
            - そこまで一般化しないのなら、self.kも引数から外してしまっていい気がした

解答1
```Python3
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_heap = nums
        heapq.heapify(self.top_k_heap)

        while len(self.top_k_heap) > self.k:
            heapq.heappop(self.top_k_heap)

    def add(self, val: int) -> int:
        heapq.heappush(self.top_k_heap, val)

        while len(self.top_k_heap) > self.k:
            heapq.heappop(self.top_k_heap)
        
        return self.top_k_heap[0]
```

解答2 (補助関数を使った場合)
```Python3
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_heap = []

        for num in nums:
            self._push_and_trim(num)

    def add(self, val: int) -> int:
        self._push_and_trim(val)
        return self.top_k_heap[0]

    def _push_and_trim(self, val):
        heapq.heappush(self.top_k_heap, val)
        while len(self.top_k_heap) > self.k:
            heapq.heappop(self.top_k_heap)
```

# Step3
- 解答1, 解答2で最終確認

解答1
```Python3
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_heap = nums
        heapq.heapify(self.top_k_heap)

        while len(self.top_k_heap) > self.k:
            heapq.heappop(self.top_k_heap)

    def add(self, val: int) -> int:
        heapq.heappush(self.top_k_heap, val)

        while len(self.top_k_heap) > self.k:
            heapq.heappop(self.top_k_heap)
        
        return self.top_k_heap[0]
```

解答2 (補助関数を使った場合)
```Python3
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_heap = []

        for num in nums:
            self._push_and_trim(num)

    def add(self, val: int) -> int:
        self._push_and_trim(val)
        return self.top_k_heap[0]

    def _push_and_trim(self, val):
        heapq.heappush(self.top_k_heap, val)
        while len(self.top_k_heap) > self.k:
            heapq.heappop(self.top_k_heap)
```