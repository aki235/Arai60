# Link
https://leetcode.com/problems/add-two-numbers/

# Step1
- 2つの整数を筆算で足すような問題
    - 数字が逆順に連結リストに入っているので、繰り上がりを保持しつつ、下の桁から足していくだけで問題なくできそう
- whileでl1とl2を進めながら各桁を足してみる
    - 普通に書くと、末尾に余分なノードが入ったりしてしまう。答えを見る→番兵ノードを使えば解決すると思い出す
        - [前の問題](https://github.com/aki235/Arai60/pull/4)でもやったけれど、忘れてしまっていた
    - 番兵ノードを追加してAC

``` Python3
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummyHead = ListNode(next = None)
        node = dummyHead
        carry = 0

        while l1 or l2 or carry != 0:
            new_node = ListNode()
            node.next = new_node
            l1_val = l1.val if l1 else 0
            l2_val = l2.val if l2 else 0

            new_node.val = (l1_val + l2_val + carry) % 10
            carry = (l1_val + l2_val + carry) // 10

            node = new_node
            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None
        
        return dummyHead.next
```

# Step2
- [他の方の解答](https://github.com/olsen-blue/Arai60/pull/5)を見る
    - こちらの解答を元に、while文内のnode作成、計算周りを整理
        - 自分の解答だと、new_nodeを作ってから```new_node.val = (l1_val + l2_val + carry) % 10```と代入しているが、digitとcarryを計算してから、```new_node = ListNode(digit, None)```とした方がきれいに書けると思った
    - sumという変数を作らずとも、```digit = (l1_val + l2_val + carry) % 10
            carry = (l1_val + l2_val + carry) // 10```と書いた方が簡潔だと思ったけど、どうなのだろう
- ```while l1 or l2 or carry != 0```の部分で、carryだけ!=0としているのがおかしいと思ったので、```while l1 or l2 or carry```に修正
    - [三項演算子について](https://github.com/olsen-blue/Arai60/pull/5#discussion_r1901274167)を目にする
        - 三項演算子を使わずに書き直してみたパターン(解答2)
            - この問題の場合は冗長になりそう

解答1 (step1を書き直したもの)
``` Python3
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummyHead = ListNode()
        node = dummyHead
        carry = 0

        while l1 or l2 or carry:
            l1_val = l1.val if l1 else 0
            l2_val = l2.val if l2 else 0

            digit = (l1_val + l2_val + carry) % 10
            carry = (l1_val + l2_val + carry) // 10

            new_node = ListNode(digit)
            node.next = new_node
            node = new_node

            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None
        
        return dummyHead.next
```

解答2 (三項演算子を用いない場合)
``` Python3
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummyHead = ListNode()
        node = dummyHead
        carry = 0

        while l1 or l2:
            l1_val = l1.val if l1 else 0
            l2_val = l2.val if l2 else 0

            digit = (l1_val + l2_val + carry) % 10
            carry = (l1_val + l2_val + carry) // 10

            new_node = ListNode(digit)
            node.next = new_node
            node = new_node

            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None
        if carry:
            new_node = ListNode(carry, next=None)
            node.next = new_node
        
        return dummyHead.next
```
            

# Step3
解答1をもとに最終確認
```Python3
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummyHead = ListNode(next=None)
        node = dummyHead
        carry = 0

        while l1 or l2 or carry:
            l1_val = l1.val if l1 else 0
            l2_val = l2.val if l2 else 0

            digit = (l1_val + l2_val + carry) % 10
            carry = (l1_val + l2_val + carry) // 10

            new_node = ListNode(digit)
            node.next = new_node
            node = new_node

            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None
        
        return dummyHead.next
```
