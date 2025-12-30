# リンク


# Step1
- Arai60の説明によると、スタックを使う問題っぽいので、各文字をスタックに入れていって、なんらかのタイミングでpopしていく方向になりそう。
- 文字列が有効であるかを判定するには、かっこの右側(")", "]", "}")だけを見ていれば良さそう
    - これらが現れたときに、スタックで直前にある文字が、同種のかっこ左側になっていればok(必要十分条件)
        - ただし、ペアができ次第、かっこはスタックから削除されていくものとする
            - ペアを消していって、最後にスタックが空になれば文字列は有効
- かっこのペアを辞書にすればif文を何個も書かなくて良さそう

- これらを元に実装

```
class Solution:
    def validParentheses(self, s):
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
                pair = q.pop()
                if pair != parentheses_dict[s[i]]:
                    return False
        if q == []:
            return True
        return False
```



# Step2


# Step3