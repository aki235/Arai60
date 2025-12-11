# Link
https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/

# Step1
- [83. Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)と同じく、隣接するノードを見ていって、重複を確認するという方法で良さそう
    - ただ、重複地帯の1個前のノードのnextを、重複地帯の一個後のノードにリンクする必要がある
        - 今いるノードの(重複を除いた)一個前を保存した方がいいかも
- 不要なノードを削除してしまうので、例外処理がめんどくさい。
    - 以前、先頭にダミーでノードを作る方法を見た気がするのでやってみるが、うまくコードがまとまらないので[他の方の解答](https://github.com/olsen-blue/Arai60/blob/olsen-blue-patch-4/82.%20Remove%20Duplicates%20from%20Sorted%20List%20II.md)を見る(コードは下記。step4より抜粋)
        - 4行目のlast_fixed_node = dummyが意味として自然でいいなと思った
        - 答えを見る前にもう少し考えたいとは思ったけれど(悔しいので、普段はわかるまで粘っていそう)、[5分考えてわからなかったら答えを見る](https://docs.google.com/document/d/1bjbOSs-Ac0G_cjVzJ2Qd8URoU_0BNirZ8utS3CUAeLE/)というルールを試す
    - ダミーのノードは、番兵ノード(sentinel node)というらしい

```Python3
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(next=head)
        last_fixed_node = dummy
        node = head

        while node and node.next:
            if node.next is not None and node.val == node.next.val:
                while node.next is not None and node.val == node.next.val:
                    node = node.next
                last_fixed_node.next = node.next
            else :
                last_fixed_node = last_fixed_node.next
            node = node.next
        return dummy.next
```

# Step2
- while内の1行目、```if node.next is not None```はwhileの条件式で保証されているのでいらないのではと思ったので消した
- ```is not None```を消した
    - 好みでよさそう (参照: [None 判定](https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.45ot5ov2xt6j))
- ~~11行目```last_fixed_node.next = node.next```と書いているけれど、厳密に言えばnode.nextがfixedであるとは限らないのでは？~~
    - ~~```node.next.val == node.next.next.val```の場合など~~
- →別に```last_fixed_node = node.next```としているわけでは無いから間違っていない
    - while文内のelse(=nodeが重複なしと判断された場合)のみ、```last_fixed_node = last_fixed_node.next```(13行目)としているから、むしろ正しい
    - ifが成り立つ時はlast_fixed_nodeのnextの付け替えだけして、elseに入った時に確定させていると言える


```Python3
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(next=head)
        last_fixed_node = dummy
        node = head

        while node and node.next:
            if node.val == node.next.val:
                while node.next and node.val == node.next.val:
                    node = node.next
                last_fixed_node.next = node.next
            else:
                last_fixed_node = last_fixed_node.next
            node = node.next
        
        return dummy.next
```


# Step3
step2のコードで最終確認

```Python3
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(next=head)
        last_fixed_node = dummy
        node = head

        while node and node.next:
            if node.val == node.next.val:
                while node.next and node.val == node.next.val:
                    node = node.next
                last_fixed_node.next = node.next
            else:
                last_fixed_node = last_fixed_node.next
            node = node.next
        
        return dummy.next
```
