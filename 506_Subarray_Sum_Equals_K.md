# Link


# Step1
- 下記のコードを提出したがTLEになった
    - 時間計算量はO(N^2)。nums.length <= 2 * 10^4なので、ギリギリ間に合うかと思ったが、ダメだった
    - Pythonは10^6 ~ 10^7回/秒程度の計算回数とのこと。[参考コメント](https://github.com/Yuto729/LeetCode_arai60/pull/16#discussion_r2602118324)
        - ちなみに、C++に変換したところ、2700msでACできた

TLEになったコード
```Python3
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        equals_to_k_count = 0

        for i in range(0, len(nums)):
            sum_ = 0
            for j in range(i, len(nums)):
                sum_ += nums[j]
                if sum_ == k:
                    equals_to_k_count += 1
        
        return equals_to_k_count
```

- よい方法が思いつかなかったので、[Leetcodeの解答](https://leetcode.com/problems/subarray-sum-equals-k/solutions/6156695/adding-number-of-current-total-k-by-niit-i97a/)を見る
    - なるほど。。。任意のsubarrayは、先頭から続くsubarray二つの差を用いて表せるということか。。
        - それで、辞書を使うとO(1)で和を探してこれるから相性がいい。
    - 時間計算量O(N)、空間計算量O(N)。
- 自分で一度書いて確認したのち提出

```Python3
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        sum_to_frequency = defaultdict(int)
        sum_to_frequency[0] = 1

        count = 0
        sum_ = 0
        for num in nums:
            sum_ += num
            count += sum_to_frequency[sum_ - k]
            sum_to_frequency[sum_] += 1

        return count
```


# Step2
## 読んだコード
- https://github.com/olsen-blue/Arai60/pull/16
- https://github.com/ryosuketc/leetcode_arai60/pull/16
- https://github.com/potrue/leetcode/pull/16 *
- https://github.com/Fuminiton/LeetCode/pull/16
- https://github.com/tokuhirat/LeetCode/pull/16


## 感想
- O(N)のアルゴリズムを思いつく部分がこの問題の一番の山場だと思った。逆に、アルゴリズムの幅はそこまでなさそう？
    - 個人的には、この説明が手順がイメージしやすくて一番わかりやすかった。
        - https://github.com/ryosuketc/leetcode_arai60/pull/16#discussion_r2109771699
- defaultdictについて
    - このような[コメント](https://github.com/Fuminiton/LeetCode/pull/16#discussion_r1980996304)があった。dict、defaultdictについて、ドキュメントを読んだことがなかった気がしたので、目を通した。
- 変数名について
    - 累積和は、accumulated_sum、prefix_sumなどが適切と思われるので、修正。
    [参考コメント](https://github.com/tokuhirat/LeetCode/pull/16#discussion_r2086783958) / [Wikipedia: 累積和](https://ja.wikipedia.org/wiki/%E7%B4%AF%E7%A9%8D%E5%92%8C)
- 練習の目的についての[コメント](https://github.com/plushn/SWE-Arai60/pull/15#discussion_r2060646138)を読んで、大事だと思ったのでメモ
    - > 「連想する内容を増やし視野を広げる」ことを目的とした練習なので、視野が広がったことに報酬を感じるのが一番素直なわけです...

修正を行なったコード
```Python3
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum_to_frequency = defaultdict(int)
        prefix_sum_to_frequency[0] = 1

        subarray_count = 0
        prefix_sum = 0
        for num in nums:
            prefix_sum += num
            subarray_count += prefix_sum_to_frequency[prefix_sum - k]
            prefix_sum_to_frequency[prefix_sum] += 1

        return subarray_count
```

# Step3
下記のコードで最終確認

```Python3
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum_to_frequency = defaultdict(int)
        prefix_sum_to_frequency[0] = 1

        subarray_count = 0
        prefix_sum = 0
        for num in nums:
            prefix_sum += num
            subarray_count += prefix_sum_to_frequency[prefix_sum - k]
            prefix_sum_to_frequency[prefix_sum] += 1

        return subarray_count
```

