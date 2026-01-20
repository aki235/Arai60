# Link
https://leetcode.com/problems/two-sum/


# Step1
- [Find K Pairs with Smallest Sums](https://github.com/aki235/Arai60/pull/10)と同じ方法で解けそうだと思って書いてみるが、Memory Limit Exceededになってしまった
    - Find K Pairsの方は、空間計算量がO(k)(k ~ 10^4)で済むが、こちらはO(N^2) (N ~ 10^4)なので、良くなさそう
        - よく考えたら、"合計値がk番目に大きいペア"より、"合計値がちょうどtargetと等しいペア"の方が条件が弱め(リスト全体を見なくても単体で判定できる)だから、ヒープを使うのはやりすぎだと思った
    - あとコードが割とややこしくなってしまった
- hashmapによる公式解法を見る
    - 空間計算量がO(N)で済む
    - これを提出して、AC

解答1 (ハッシュマップ)
```Python3
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hashmap = {}
        for i in range(len(nums)):
            hashmap[nums[i]] = i
        for i in range(len(nums)):
            complement = target - nums[i]
            if complement in hashmap and hashmap[complement] != i:
                return [i, hashmap[complement]]
        
        return []
```


# Step2

## 読んだコード
- https://github.com/olsen-blue/Arai60/pull/11
- https://github.com/hayashi-ay/leetcode/pull/14
- https://github.com/Fuminiton/LeetCode/pull/11
- https://github.com/ryosuketc/leetcode_arai60/pull/11
- https://github.com/potrue/leetcode/pull/11

## 感想
- ブルートフォースでも解ける
    - 最初に入力の大きさをチェックしていなかった。前提を確認するのは大事だなと思った
    - 実装の容易さから、計算量が最小でない方法を使った方がいいこともあるかもしれない
    - ただ、この問題のNは最大10^4だが、[Pythonが1秒間に10^6~10^7回計算できる]ことを踏まえると、O(N^2)のアルゴリズムは怪しいのでは？と思った
- ハッシュテーブルを使う方法でも、ハッシュテーブルの作成+判定を同時に行う方法(one-pass)と、ハッシュテーブルを一度作ってから、判定を改めて行う方法(two-pass)が考えられる [参考](https://leetcode.com/problems/two-sum/solutions/127810/two-sum-by-leetcode-kwuq/)
    - one-pass、two-passというのは、専門用語ではなく、leetcodeの用語らしい
        - https://stackoverflow.com/questions/65085114/what-is-2-pass-and-1-pass-hash-table
    - この問題ならone-passの方がメモリが少なく済むので良さそう。ロジックが複雑な時はTwo-passの方が安全かも
- 入力が不正な場合における、Exceptionの出力について触れている人が多かった
    - https://github.com/olsen-blue/Arai60/blob/olsen-blue-patch-11/1.%20Two%20Sum.md
    - 公式ドキュメント: https://docs.python.org/3/library/exceptions.html#bltin-exceptions
    - この場合はValueError(型には問題がないが、値に問題がある場合)に該当しそう。
- ソート+Two Pointersによる解法
    - numsをソートしてから、両端にポインタを置く
        - 2つのポインタの合計値がtargetより大きいとき: 左のポインタを右に1つ移動
        - 〃　　　　　　　　　　 targetより大きいとき: 右のポインタを左に1つ移動
    - これを繰り返せば、ポインタの間隔が狭まっていって必ず正解の値に辿り着くし、正解を通り越してしまうということもない(片方が正解に乗った時点で、targetとの大小関係からもう片方しか動かないから)
    - 時間計算量はO(N logN)、空間計算量はO(1)
        - ただ、結局新しくソートされた配列を作ると、空間計算量はO(N)かかる
- 変数名
    - complement, num_to_indexが良いと思った

解答1 (ハッシュマップ、修正版)
```Python3
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}

        for i in range(len(nums)):
            complement = target - nums[i]
            if complement in num_to_index:
                return [num_to_index[complement], i]
            num_to_index[nums[i]] = i
        
        raise ValueError("No solution exists for the given input.")
```

解答2 (ブルートフォース)
```Python3
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for i in range(0, len(nums) - 1):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
        
        return []
```

解答3 (ソート+Two Pointers)
```Python3
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        value_index_pairs = sorted([(v, i) for i, v in enumerate(nums)], key=lambda x: x[0])

        left, right = 0, len(value_index_pairs) - 1
        while left < right:
            s = value_index_pairs[left][0] + value_index_pairs[right][0]
            if s == target:
                return [value_index_pairs[left][1], value_index_pairs[right][1]]
            if s < target:
                left += 1
            else:
                right -= 1
        
        return []
```

# Step3
解答1で最終確認

解答1 (ハッシュマップ、修正版)
```Python3
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}

        for i in range(len(nums)):
            complement = target - nums[i]
            if complement in num_to_index:
                return [num_to_index[complement], i]
            num_to_index[nums[i]] = i
        
        raise ValueError("No solution exists for the given input.")
```