# Link
https://leetcode.com/problems/intersection-of-two-arrays/

# Step1
- nums1, nums2をそれぞれ辞書化して、nums1の辞書のキーが、nums2の辞書に含まれているか調べていけば、O(N)の定数倍でできそう。
    - 出現回数も考慮すると思っていたが、そうではなかった。それなら、setの方が簡潔に書けそう。
        - setも辞書と同様、キーの検索をO(1)でできたはず。
- 上記を元にAC

解答1 (set)
```Python3
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        intersection = []
        nums1_set = set(nums1)
        nums2_set = set(nums2)

        for num in nums1_set:
            if num in nums2_set:
                intersection.append(num)
        return intersection
```

# Step2
## 読んだコード
https://github.com/olsen-blue/Arai60/pull/13
https://github.com/katataku/leetcode/pull/12
https://github.com/Fuminiton/LeetCode/pull/13
https://github.com/potrue/leetcode/pull/13
https://github.com/fhiyo/leetcode/pull/16


## 感想
- 解答1について
    - set同士の積集合は`&`で求められるので、より簡潔に書ける
- 他の人の解答を見ると、問題の条件を変えて取り組んでいるものが多かった
    - 確かにsetの解法だけだと単純すぎる。。
    - https://github.com/katataku/leetcode/pull/12#discussion_r1894613102
- 条件A) nums1がソート済みの長いリストで、nums2がソートされていない短いリストの場合
    - nums2の各要素を、nums1から二分探索で探していく方法が良さそう(解答2)
    - 計算量 (nums1の長さM、nums2の長さNとする)
        - 時間計算量はO(N log M + N^2)
            - forループ一回ごとに二分探索でlogM、`if num in nums2`の部分でN^2かかる
                - Nが小さいと仮定しているのでこのようなコードにしたが、N^2が気になるならintersectionをsetで管理するといいかも
        - 空間計算量はO(1) + 出力分
    - 二分探索をよく理解できていないと思ったので調べた。
        - 解答2におけるleft、rightの初期設定は、[このページ](https://komiyam.hatenadiary.org/entry/20120809/1344438215)の"整数の二分探索"と同じっぽい。
        - 本当にこれでうまくいくのか、無限ループや、取りこぼしが起きないかが気になったので自分の中で整理。
            - 探索対象の値がリストに存在する場合:
                - 探索対象値のインデックスをtarget_indexとする
                - left < target_index, target_index < rightであることは、操作中常に保証される。また、right - leftは操作ごとに必ず減少する。
                - middle = target_indexとなる前にwhileループを抜けてしまうとまずいが、ループを抜ける条件がrightとleftが隣接することなので、(left < target_index < rightより)発生しない。
            - 探索対象の値がリストに存在しない場合:
                - right - leftは操作ごとに必ず減少するため、どこかで必ずrightとleftは隣接するので、ループを抜けられる。
- 条件B) nums1、nums2ともにソート済みの長いリストの場合
    - マージソートの要領で行うのが良い(解答3)
        - 先頭から要素を比較していき、小さい方をインクリメントしていく
    - 計算量　(nums1の長さM、nums2の長さNとする)
        - 時間計算量はO(M + N)
        - 空間計算量はO(1) + 出力分
    - 最初は重複排除をsetで行っていたが、[コメント](https://github.com/Fuminiton/LeetCode/pull/13#discussion_r1975727400)を見てwhileでやってみた
        - こちらの方がsetに丸投げしていなくて、丁寧な感じはする。
        - メモリに乗らない場合も対応できる(intersection全部がメモリに乗らなくても、whileなら1つずつ出力するように書き換えられるということ？)

解答1 (set、修正版)
```Python3
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        nums1_set = set(nums1)
        nums2_set = set(nums2)
        return list(nums1_set & nums2_set)
```

解答2 (二分探索)
```Python3
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # nums1は長いソート済みのリスト、nums2は短い未ソートのリストを想定
        nums1 = sorted(nums1) # 想定に合わせるためソート

        intersection = []
        for num in nums2:
            if num in intersection:
                continue

            left = -1
            right = len(nums1)

            while right - left > 1:
                middle = (right + left) // 2
                if num < nums1[middle]:
                    right = middle
                elif num > nums1[middle]:
                    left = middle
                else:
                    intersection.append(num)
                    break
        
        return intersection
```

解答3 (マージソート)
```Python3
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # nums1、num2ともに長いソート済みのリストを想定
        nums1 = sorted(nums1) # 想定に合わせるためソート
        nums2 = sorted(nums2) # 同上

        intersection = []
        i = 0
        j = 0
        while i < len(nums1) and j < len(nums2):
            if nums1[i] == nums2[j]:
                intersection.append(nums1[i])
                num_to_skip = nums1[i]
                while i < len(nums1) and nums1[i] == num_to_skip:
                    i += 1
                while j < len(nums2) and nums2[j] == num_to_skip:
                    j += 1
            elif nums1[i] > nums2[j]:
                j += 1
            else:
                i += 1
        return list(intersection)
```


# Step3
- 解答1、解答2で最終確認

解答1 (set, 修正版)
```Python3
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        nums1_set = set(nums1)
        nums2_set = set(nums2)
        return list(nums1_set & nums2_set)
```


解答2 (二分探索)
```Python3
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        nums1 = sorted(nums1)

        intersection = []
        for num in nums2:
            if num in intersection:
                continue

            left = -1
            right = len(nums1)

            while right - left > 1:
                middle = (right + left) // 2
                if num < nums1[middle]:
                    right = middle
                elif num > nums1[middle]:
                    left = middle
                else:
                    intersection.append(num)
                    break
        
        return intersection
```