# リンク


# Step1
- Arai60の分類によると、スタックを使う問題っぽいので、各文字をスタックに入れていって、なんらかのタイミングでpopしていく方向になりそう。
- 文字列が有効であるかを判定するには、かっこの右側(")", "]", "}")だけを見ていれば良さそう
    - これらが現れたときに、スタックで直前にある文字が、同種のかっこ左側になっていればok(必要十分条件)
        - ただし、ペアができ次第、かっこはスタックから削除されていくものとする
            - ペアを消していって、最後にスタックが空になれば文字列は有効
- かっこのペアを辞書にしておけば、if文を何個も書かなくて良さそう
    - 3種類程度なら必要ないかもしれないけれど、一般化しやすいので、こちらの方がいいかなと思った

- 上記を元に実装して、AC

```
class Solution:
    def isValid(self, s: str) -> bool:
        q = []

        parentheses_dict = {
            ")": "(",
            "}": "{",
            "]": "["
        }

        for i in range(len(s)):
            if s[i] == "(" or s[i] == "[" or s[i] == "{":
                q.append(s[i])
            elif s[i] == ")" or s[i] == "]" or s[i] == "}":
                if q == []:
                    return False
                pair = q.pop()
                if pair != parentheses_dict[s[i]]:
                    return False
        if q == []:
            return True
        return False
```


# Step2
- プッシュダウンオートマトンについて[Discordの投稿](https://discord.com/channels/1084280443945353267/1201211204547383386/1202541275115425822)を見る
    - [チョムスキー階層について](https://ja.wikipedia.org/wiki/%E3%83%81%E3%83%A7%E3%83%A0%E3%82%B9%E3%82%AD%E3%83%BC%E9%9A%8E%E5%B1%A4)
        - タイプ2が文脈自由文法で、ほとんどのプログラミング言語はこれ。プッシュダウンオートマトンで認識できる
        - タイプ3は正規文法で、正規表現として書かれる。有限オートマトンで認識できる
    - 有限オートマトンは有限のメモリしか持てないが、プッシュダウンオートマトンはスタックとして無限のメモリを持てる
        - したがって、この問題はプッシュダウンオートマトンを用いないと書けない、と理解した

- キューではなくスタックなので、qという名前はよくなさそう。また、名前の情報量もないので変更
    - [「キュー」と「スタック」の違い](https://wa3.i-3-i.info/diff462data.html)
    - open_bracketsという命名がよさそう(参考: [hayashi-ay/leetcode](https://github.com/hayashi-ay/leetcode/tree/hayashi-ay-patch-5))
        - 実際のところ、ペアになっていないカッコを溜めていくのがこのスタックなので、意味が通っている

- [olsen-blue/Arai60](https://github.com/olsen-blue/Arai60/tree/olsen-blue-patch-6)を参考にする
    - [意図しない入力への対応](https://github.com/olsen-blue/Arai60/pull/6#discussion_r1904043914)を行なった
    - 自分のコードでは、for文をインデックスiを使って回しているが、コード内でiを使うことはないから、`for char in s`などの方が良いと思った
        - charよりbracketの方が分かりやすいかも （参考: [ryosuketc/leetcode_arai60](https://github.com/ryosuketc/leetcode_arai60/blob/main/20_valid_parentheses/memo.md))

- かっこを辞書で扱うなら、`if s[i] == "(" or s[i] == "[" or s[i] == "{"`と`elif s[i] == ")" or s[i] == "]" or s[i] == "}"`の部分も全パターン書かなくていいので、修正

上記を踏まえた修正版
``` Python3
class Solution:
    def isValid(self, s: str) -> bool:
        open_brackets = []
        bracket_dict = {
            ")": "(",
            "}": "{",
            "]": "["
        }

        for bracket in s:
            if bracket in bracket_dict.values():
                open_brackets.append(bracket)
            elif bracket in bracket_dict.keys():
                if open_brackets == []:
                    return False
                if open_brackets.pop() != bracket_dict[bracket]:
                    return False
            else:
                return False
        
        if open_brackets == []:
            return True
        return False
```

# Step3
Step2のコードを元に最終確認を行なった

```Python3
class Solution:
    def isValid(self, s: str) -> bool:
        open_brackets = []
        bracket_dict = {
            ")": "(",
            "}": "{",
            "]": "["
        }

        for bracket in s:
            if bracket in bracket_dict.values():
                open_brackets.append(bracket)
            elif bracket in bracket_dict.keys():
                if open_brackets == []:
                    return False
                if open_brackets.pop() != bracket_dict[bracket]:
                    return False
            else:
                return False
        
        if open_brackets == []:
            return True
        return False
```