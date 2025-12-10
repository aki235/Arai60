# Link



# Step1
- ノードの重複を判定するときは、複数を同時に確認するより隣接する二つを確認していった方が扱いやすそう
    - 同じ値のノードが3つ以上連続している場合は、nextを付け替えればよい
- 先読みする方法と、前のノードを振り返る方法があるが、後者は前のノードを保存しなければいけないためやや面倒になりそう？
    - 先読みする方法で実装

## 解法1(先読み)
```Python3
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        
        while node:
            if node.next:
                if node.val == node.next.val:
                    node.next = node.next.next
                else:
                    node = node.next
            else:
                break
        
        return head
```

# Step2
- 前のノードを振り返る方法も書いてみる
    - 先読みとそこまで長さは変わらなかった

## 解法2(振り返り)
```Python3
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        prev = None
        
        while node:
            if prev and prev.val == node.val:
                prev.next = node.next
            else:
                prev = node
            node = node.next

        return head
```

- 解法1のif node.nextの部分は、whileの中にまとめてしまってもいいなと思った https://github.com/olsen-blue/Arai60/blob/olsen-blue-patch-3/83_RemoveDuplicatesfromSortedList.md
    - 挙動が変わるのはnode!=Noneかつnode.nextの時(末尾のノードにいる時)だけど、この場合はもうwhileループを抜けてしまって構わないため

## 解法1(先読み, 修正版)
```Python3
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        
        while node and node.next:
            if node.val == node.next.val:
                node.next = node.next.next
            else:
                node = node.next

        return head
```

- 変数名のチェック
    - 略語は避けた方がよい
        - https://github.com/aki235/Arai60/pull/2#discussion_r2599395795
        - https://google.github.io/styleguide/pyguide.html#316-naming
    - prev
        - prevは微妙なところかも
            - https://github.com/fv17/coding-practice/pull/5#discussion_r2597394494
        - [GitHubのコード検索](https://github.com/resumit30minutes/leetcode-arai60-practice/pull/2#discussion_r2462812309)だとprev_(1.7M)>previous_(778K)で同じくらい
            - ちなみに、[上のコメント](https://github.com/fv17/coding-practice/pull/5#discussion_r2597394494)で挙げられているmax_(15.7M)>maximum_(350K)は大差がついているから、やはりprevは若干怪しいかも

# Step3
解法1で最終確認にトライ
```Python3
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head

        while node and node.next:
            if node.val == node.next.val:
                node.next = node.next.next
            else:
                node = node.next
        
        return head
```
