# Link
https://leetcode.com/problems/top-k-frequent-elements/

# Step1
- 辞書で数字と出現回数を管理したい
    - リストを前から見ていって、辞書にあれば出現回数を+1して、なければ値を1として新たに要素を作る
    - 辞書を出現回数の降順でソートして、前からk個の数字(キー)を取り出す
- 値で辞書をソートする方法を覚えていなかったので調べた
    - `num_frequency = sorted(num_frequency.items(), key=lambda item: item[1], reverse=True)`
        - sortedの第一引数はイテラブルなオブジェクト、keyは関数を与える

- 上記をもとに書き、AC

解答1
```Python3
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_frequency = {}

        for num in nums:
            if num in num_frequency:
                num_frequency[num] += 1
            else:
                num_frequency[num] = 1

        num_frequency = sorted(num_frequency.items(), key=lambda item: item[1], reverse=True)

        res = []
        for i in range(k):
            res.append(num_frequency[i][0])
        
        return res
```

# Step2
## 読んだコード
- https://github.com/Fuminiton/LeetCode/pull/9
- https://github.com/TORUS0818/leetcode/pull/11
- https://github.com/Mike0121/LeetCode/pull/3
    - defaultdictの実装
- https://github.com/olsen-blue/Arai60/pull/9

# 感想
- ちゃんと公式ドキュメントを見た方がいいなと思った
    - defaultdict: https://docs.python.org/ja/3.10/library/collections.html#collections.defaultdict
    - sorted: https://docs.python.org/ja/3/howto/sorting.html
- ソート以外の方法も多くあった。一通り見てみる。
    - heapq ([参考コード](https://github.com/TORUS0818/leetcode/blob/347/medium/347/answer.md))
        - 一応、sortedの方が速いらしい。https://github.com/olsen-blue/Arai60/pull/9#discussion_r1905335335
            - 理由が気になったので、ChatGPTに聞いてみた。
                - 計算量としては、ユニーク数をmとすると、heapはO(m logk)、ソートはO(m logm)で、kとmによる
                - ただし、Pythonでは、sortedはかなり最適化されている(Timsort)が、heapqはPythonレベルのループが多いため、sortedの方が速いらしい。
        - [これは初手で候補に入っているべきだなと思った 上からk番目〜みたいなケースではheapを想起した方がいい](https://github.com/TORUS0818/leetcode/blob/347/medium/347/answer.md)というコメントがあった。確かにと思った。
    - Counter ([参考コード](https://github.com/TORUS0818/leetcode/blob/347/medium/347/answer.md))
    - QuickSelect ([参考コード](https://github.com/TORUS0818/leetcode/blob/347/medium/347/answer.md))
        - 平均計算量は、(うまくpivotがばらけていた場合)n + n/2 + n/4 +... = 2nとなるので、O(N)と速い
        - ただ、sortedの方が、QuickSelectより(計算量は大きいが)基本的には早いらしい。 [参考コメント](https://github.com/syoshida20/leetcode/pull/14#discussion_r2100950875)
        - 何も見ずに1回は書けるようにした
- `.get`について
    - sortedは、lambda関数を使っていたが、`sorted(num_frequency.items(), key=num_frequency.get, reverse=True)`のような書き方もできるらしい。[参考コメント](https://github.com/olsen-blue/Arai60/pull/9#discussion_r1907145089)
- defaultdictについて
    - こちらの方がコードが簡潔になるが、自分で実装してほしいと言われたら、できた方がいいと思う。
    - [自分で実装された方のコード](https://github.com/olsen-blue/Arai60/pull/9#discussion_r1907145089)
- 解答1を修正
    - res変数は使わなくてもよく、リスト内包表記で書いた方がシンプルになりそう
        - また、resを使うとしても、略称ではなく、resultという名前の方がいい
    - defaultdictを使った方が実務に近い？と思ったので、修正

解答1 (ソート、修正版)
```Python3
from collections import defaultdict

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_frequency = defaultdict(int)
        for num in nums:
            num_frequency[num] += 1
        
        num_frequency = sorted(num_frequency.items(), key=lambda item: item[1], reverse=True)

        return [num for num, _ in num_frequency[:k]]
```


解答2 (ヒープ)
```Python3
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_frequency = defaultdict(int)
        for num in nums:
            num_frequency[num] += 1
        
        nums_sorted_by_frequency = []
        for num, frequency in num_frequency.items():
            heapq.heappush(nums_sorted_by_frequency, (frequency, num))
            while len(nums_sorted_by_frequency) > k:
                heapq.heappop(nums_sorted_by_frequency)
        
        result = []
        while nums_sorted_by_frequency:
            result.append(heapq.heappop(nums_sorted_by_frequency)[1])
        return result
```

解答3 (Counter)
```Python3
from collections import Counter

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_counter = Counter(nums)
        return [num for num, _ in num_counter.most_common(k)]
```

# Step3
- ソートを使った方法(解答1)で最終確認

解答1 (ソート、修正版)
```Python3
from collections import defaultdict

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_frequency = defaultdict(int)
        for num in nums:
            num_frequency[num] += 1
        
        num_frequency = sorted(num_frequency.items(), key=lambda item: item[1], reverse=True)

        return [num for num, _ in num_frequency[:k]]
```