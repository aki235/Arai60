# Link


# Step1
- スタックと再帰を使う方法でそれぞれ書いた
- 気になった点
    - スタックは、どれくらいメモリを使うのか
        - Pythonでは10byte * ノード数くらい
        - 今回のケース(ノード5000個)で考えてみると、50kBくらい
    - 再帰はこれで通ったものの、あまり理解できている感じがしない
        - また、補助の関数を使わなくても書けるかどうかも気になる

解答1 (スタック)
```Python3
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        stack = []
        node = head

        while node:
            stack.append(node.val)
            node = node.next
        
        sentinel = ListNode(next=None)
        node = sentinel

        while stack:
            new_node = ListNode(val = stack.pop())
            node.next = new_node
            node = new_node

        return sentinel.next
```

解答2 (再帰)
```Python3
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        return self.recursion(None, head)

    def recursion(self, prev, current):
        if not current:
            return prev

        next_node = current.next
        current.next = prev
        return self.recursion(current, next_node)
```

# Step2
- 各コードについて、変数名やコードの書き方を修正する

- 解答1(スタック)
    - node
        - 二つの用途で同じ名前の変数を使うのはちょっとまずそう
        - [他のコード](https://github.com/olsen-blue/Arai60/blob/olsen-blue-patch-7/206.%20Reverse%20Linked%20List.md)を見ると、二つ目をlast_fixed_nodeなどにしている
            - 参考コメント: https://github.com/tarinaihitori/leetcode/pull/6/commits/7ae6c941f485e4fd52fab64cf17584150ea87d03#r1811921288
    - stack
        - 何をやっているかわかった方がいいと思った
        - vals_stackに変更
            - stackであることはパッと見でわからないから(型としてはlistだし)、stackという名前は残しておいた

- 解答2(再帰)
    - 再帰を理解する上での[参考コメント](https://github.com/goto-untrapped/Arai60/pull/27/files/14646ec0859dd9411e6983bf6c63e6f15a1f9f32#r1638693522)
        - 自分の解答2だと、5番目の人は、
            - 4-5番目のノードを逆につなぐ
            - 6番目の人に対して、5,6番目のノードを渡す
                - 6番目以降のノードを全部逆に繋ぎかえておいて、と依頼する
                - 繋ぎかえが終わったら、先頭のノードだけを返してもらう
            - 先頭のノードをreturnする(4番目の人に渡す)、と理解
    - recursion関数
        - 再帰を使っている以外の情報量がないし、一般的すぎる名前だと組み込み関数と被りそうで怖い(一応Pythonでは被っていなかった)
        - helper([参考コメント](https://github.com/hayashi-ay/leetcode/blob/hayashi-ay-patch-2/206.%20Reverse%20Linked%20List.md))
        - reverseListHelper([参考コメント](https://discord.com/channels/1084280443945353267/1231966485610758196/1239417493211320382))
    - prev, current
        - prev_node, current_nodeのように、ListNode型のnodeであることがパッとわかった方がいいかも
    - 補助関数を使わない方法([参考コメント](https://github.com/hayashi-ay/leetcode/blob/hayashi-ay-patch-2/206.%20Reverse%20Linked%20List.md))

- スタックも再帰も使わない方法(連結の付け替え)で書いてみる(解答3)([参考コメント](https://github.com/potrue/leetcode/blob/206/206/206.md))
    - 一個前のノードを保存しておき、前から順に連結を付け替えていく
    - 簡潔だし、空間計算量がO(N)のスタックと違い、O(1)で済むので、(Arai60での分類とは異なるが)この問題ではこれがベストな気がした
        - [こちらのコメント](https://github.com/Fuminiton/LeetCode/pull/7#discussion_r1957331905)によると、スタックよりも、付け替えが想定っぽい
    - tmpという変数は微妙かも
        - ただ、next_node_to_seeなどを考えたが、変数の目的としては、ノードの付け替え時に一旦逃がすことなので、次に見るノードという意味を与えない方がいい気がした


解答1 (スタック_修正版)
```Python3
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        vals_stack = []
        node = head
        while node:
            vals_stack.append(node.val)
            node = node.next

        sentinel = ListNode(next=None)
        last_fixed_node = sentinel
        while vals_stack:
            new_node = ListNode(val = vals_stack.pop())
            last_fixed_node.next = new_node
            last_fixed_node = new_node

        return sentinel.next
```

解答2 (再帰_修正版)
```Python3
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        return self.reverseListHelper(None, head)

    def reverseListHelper(self, last_fixed_node, current):
        if not current:
            return last_fixed_node

        next_node = current.next
        current.next = last_fixed_node
        return self.reverseListHelper(current, next_node)
```

解答3 (連結の付け替え)
```Python3
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        last_fixed_node = None
        while node:
            tmp = node.next
            node.next = last_fixed_node
            last_fixed_node = node
            node = tmp

        return last_fixed_node
```


# Step3
- 解答2と解答3で最終確認
    - 解答3が想定っぽいのと、再帰を練習したかったため

解答2 (再帰_修正版)
```Python3
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        return self.reverseListHelper(None, head)

    def reverseListHelper(self, last_fixed_node, current):
        if not current:
            return last_fixed_node

        next_node = current.next
        current.next = last_fixed_node
        return self.reverseListHelper(current, next_node)
```

解答3 (連結の付け替え)
```Python3
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        last_fixed_node = None
        while node:
            tmp = node.next
            node.next = last_fixed_node
            last_fixed_node = node
            node = tmp
        
        return last_fixed_node
```
