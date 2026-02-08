# Link
https://leetcode.com/problems/first-unique-character-in-a-string/description/

# Step1
- アルファベットと最初に現れたインデックスを辞書で管理すればできそう。重複がある場合は値を-1にする。
    - その後、辞書の値を確認し、-1ではないものの中から最小値を探せばよい
- 時間計算量はO(N)、空間計算量はO(1)

解答1
```Python3
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_index = {}

        for i in range(len(s)):
            if s[i] not in char_to_index:
                char_to_index[s[i]] = i
            else:
                char_to_index[s[i]] = -1

        min_index = -1
        for index in char_to_index.values():
            if index != -1:
                if min_index == -1:
                    min_index = index
                    continue
                min_index = min(index, min_index)
        
        return min_index
```


# Step2
## 読んだコード
- https://github.com/olsen-blue/Arai60/pull/15
- https://github.com/ryosuketc/leetcode_arai60/pull/15
- https://github.com/potrue/leetcode/pull/15
- https://github.com/tokuhirat/LeetCode/pull/15
- https://github.com/Fuminiton/LeetCode/pull/15

## 感想
- LinkedHashMapについて
    - HashMapとLinkedListを対応づけたものと理解。 https://discord.com/channels/1084280443945353267/1227073733844406343/1231268645628416020
        - LinkedListの部分は、出現頻度などソート順の場合
        - (脱線) 平衡木 + HashMapにすれば、[347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)をストリーミングアルゴリズムとして解ける(追加/削除はO(logN))
    - PythonではOrderedDictが追加順を保持できる
        - LinkedList + Dictionaryで実装([CPythonの実装](https://github.com/python/cpython/blob/main/Lib/collections/__init__.py))。dictのサブクラス
        - LinkedListのため、追加や削除はO(1)
        - ただし、Python 3.7以降では通常のdictも追加順を保持するようになったとのこと
            - dictで書くのは仕様依存で怖い気もする。とはいえそれが一般的なら問題ないのだろうか。
    - メリットとしては、ワンパスで書けるため、ストリームでの入力に対応できる点がある？
    - 一回、自分で書けるようにしておく

- Counterについて
    - イテラブルなデータを引数として渡すと、キーが要素、値が出現回数のdictを返してくれる
        - 文字列、リスト、タプルなどを渡せる
    - [CPythonの実装](https://docs.python.org/ja/3/library/collections.html#collections.Counter)
        - (余力がないため、次にCounterが出てきた時に確認)

- 各文字を表す変数名
    - charはC言語などの型名と被るのでまずい。c、characterなどが思い浮かんだ。
    - cはスコープが短い(for文の中のみなど)なら許容とのこと。characterは少し冗長な気もする(し、あまり使われていない？)

解答2 (OrderedDict)
```Python3
from collections import OrderedDict

class Solution:
    def firstUniqChar(self, s: str) -> int:
        duplicated_char = set()
        char_to_index = OrderedDict()

        for i, c in enumerate(s):
            if c in duplicated_char:
                continue
            if c in char_to_index:
                del char_to_index[c]
                duplicated_char.add(c)
            else:
                char_to_index[c] = i
        
        return next(iter(char_to_index.values()), -1)
```
時間計算量: O(N)
空間計算量: O(1)

解答3 (Counter)
```Python3
from collections import Counter

class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_frequency = Counter(s)
        for i, c in enumerate(s):
            if char_to_frequency[c] == 1:
                return i
        return -1
```
時間計算量: O(N)
空間計算量: O(1)


# Step3
解答2、解答3でそれぞれ最終確認

解答2 (OrderedDict)
```Python3
class Solution:
    def firstUniqChar(self, s: str) -> int:
        duplicated_char = set()
        char_to_index = OrderedDict()

        for i, c in enumerate(s):
            if c in duplicated_char:
                continue
            if c in char_to_index:
                del char_to_index[c]
                duplicated_char.add(c)
            else:
                char_to_index[c] = i
        
        return next(iter(char_to_index.values()), -1)
```

解答3 (Counter)
```Python3
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_frequency = Counter(s)
        for i, c in enumerate(s):
            if char_to_frequency[c] == 1:
                return i
        return -1

```