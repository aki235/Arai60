# Link
https://leetcode.com/problems/group-anagrams/

# Step1
- まず、2つの単語同士がアナグラムであるかを調べる方法を検討した
    - 単語の長さが最大10^4なので、全文字の並び替えをするのは無理そう
    - 各単語を辞書に変換し、各アルファベットの出現頻度で同一か判断しようと思った
- 辞書にしなくても、単語内の文字をabc順で並び替えたら、アナグラムにおける同一の単語を判断できることに気付く。その方が簡単そう。
    - 文字列を並び替えて文字列にする方法がわからなかったので調べた。sortedとjoinを併用すればいけそう。
- これを元にコードを書き、AC

解答1
```Python3
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagrams = defaultdict(list)
        for str_ in strs:
            str_ordered = "".join(sorted(str_))
            anagrams[str_ordered].append(str_)

        grouped_anagram = []
        for _, anagram_list in anagrams.items():
            grouped_anagram.append(anagram_list)
        return grouped_anagram
```

# Step2

## 読んだコード
- https://github.com/olsen-blue/Arai60/pull/12
- https://github.com/TORUS0818/leetcode/pull/14
- https://github.com/fhiyo/leetcode/pull/15
- https://github.com/TORUS0818/leetcode/pull/14
- https://github.com/potrue/leetcode/pull/12

## 感想
- 辞書のキーはいろいろ選択肢がある
    - キーはハッシュ可能である必要がある
        - https://docs.python.org/3/glossary.html#term-hashable
            - ひとまず、ハッシュ可能≒immutableと理解(厳密には違うっぽい)。
        - 辞書は辞書のキーにできない。文字の度数分布をキーにするなら、tupleなどに変換する必要がある
        - ハッシュ可能かは、hash関数を通すことでも確認できそう
            - https://github.com/olsen-blue/Arai60/pull/12#discussion_r1971404184
    - tuple
    - 文字列("".joinしたもの)
    - collections.Counter
    - frozenset
        - https://docs.python.org/ja/3.13/library/stdtypes.html#frozenset
        - setはmutableだが、frozensetはimmutableなので、辞書のキーにできる
        - ただし、frozensetはsetでは使えていた一部の機能が使えない
    - 度数分布
        - https://github.com/fhiyo/leetcode/blob/49_group-anagrams/49_group-anagrams.md

- 答えのdictからlistへの変換
    - for文を使って展開していたが、.values()を使う方が簡潔に書ける
        - `for _, anagram_list in anagrams.items():
            grouped_anagram.append(anagram_list)
        return grouped_anagram`から`return list(ordered_word_to_words.values())`に変更

- 変数名を修正
    - anagrams -> ordered_word_to_words
    - str_ -> word
    - str_ordered -> ordered_word

- 計算量
    - 単語の数をn、単語の長さをmとする
    - Python標準のソート (解答1)
        - 時間計算量: O(n * m log(m))
        - 空間計算量: O(nm)
    - カウンティングソート (解答2)
        - 時間計算量: O(n * m log(m))
        - 空間計算量: O(nm)
    - leetcodeの実測では、前者が9ms、後者が18ms程度
        - 単語の長さが最大100なので、log(m)が大して効かないのと、Python標準のソートが速いため
            - 標準ソート(C言語)はPythonより50~100倍程度速いと思ったが、2倍しか違わない？？
            - →環境などもあるから、ネイティブコードであるかを厳密に考える必要はないかも。[参考](https://github.com/potrue/leetcode/pull/12#discussion_r2092509837)

解答1 (文字列をソートする方法、修正版)
```Python3
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        ordered_word_to_words = defaultdict(list)
        for word in strs:
            ordered_word = "".join(sorted(word))
            ordered_word_to_words[ordered_word].append(word)

        return list(ordered_word_to_words.values())
```

解答2 (度数分布をキーにする方法)
```Python3
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        histogram_to_words = defaultdict(list)

        for word in strs:
            histogram = [0] * 26
            for character in word:
                histogram[ord(character) - ord('a')] += 1
            histogram_to_words[tuple(histogram)].append(word)
        
        return list(histogram_to_words.values())
```

# Step3
解答1で最終確認

解答1 (文字列をソートする方法、修正版)
```Python3
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        ordered_word_to_words = defaultdict(list)

        for word in strs:
            ordered_word = "".join(sorted(word))
            ordered_word_to_words[ordered_word].append(word)
        
        return list(ordered_word_to_words.values())
```
