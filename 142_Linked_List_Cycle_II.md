# Link
https://leetcode.com/problems/linked-list-cycle-ii/

# Step1
- 前に解いた記憶があるので、解法を思い出してみる。確か[141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/description/)と同じで、二つのノード(fastとslow)を動かしていく感じで解けた気がする
    - fastとslowを合流させてから何らかの操作をした気がするが、それ以上はよく思い出せないので考えてみる
- fastをheadからスタートして2個ずつ進む点、slowをheadからスタートして1個ずつスタートする点と定義して、これらが合流する点について考えてみる。ループが存在する場合は必ずどこかで合流するはず
    - この点について何か言えることはあるか考えてみる
        - fastはslowの2倍の距離を進んでいる
        - fastはslowよりちょうどループn周分多く進んでいる
        - 合流地点はループの開始地点(=答えとして求めたい地点)より必ず後ろ
    - 簡単なケースで考えたいので、fastがslowよりちょうど1周多く進んでいる場合を考えてみる
        - haad〜合流地点までの距離と、ループ一周の距離は同じ(2個ずつ進む点であるfastが、head~合流地点+ループ1周進んでいるから)
            - haad〜合流地点までの距離＝ループ一周の距離、の両辺から、ループの開始地点〜合流地点の距離を引く
                - すると、head〜ループの開始地点の距離＝合流地点〜ループの開始地点の距離、が成り立つ
            - →1個ずつ進む点を2つ作って、片方を合流地点、もう片方をheadから進めれば、ループの開始地点で合流できる！
    - これはfastがslowよりn周多く回っている時も成り立つ？
        - とりあえず考える前にコードを書いて送ってみる→AC
            - ということは、n周の時も同じ性質は成り立ちそう
        - haad〜合流地点までの距離＝ループn周の距離の両辺から、ループの開始地点〜合流地点の距離を引いたら、head〜ループの開始地点の距離＝合流地点〜ループの開始地点の距離＋ループn-1周の距離になるから、n周でも変わらず成り立つのでok

```Python3

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast = head
        slow = head

        is_loop = False

        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next

            if fast == slow:
                is_loop = True
                break
        
        if not is_loop:
            return None
        
        slow = head
        while True:
            if fast == slow:
                return fast

            fast = fast.next
            slow = slow.next
```

# Step2
- [他の方の回答](https://github.com/olsen-blue/Arai60/blob/olsen-blue-patch-2/142_LinkedListCycleII.md)を見てみる。
    - (解法1)と考え方は同じっぽい。step4の最終版を参考に自分のコードを修正してみる
        - fastを使い続けるよりも、(originのように)変数名を新しく作った方が良いと気付いた。
        - whileの後にelseを使えるのを知らなかった。
        - 2つ目のループは、while Trueで回すより、while origin!=slowで回せば、中身が簡潔に書ける
    - (解法2)だと、リストが長くなるとvisitedがかさんでしまう問題はある気がするけれど、やっていることがとてもわかりやすくていいと思った。
        - O(n)の空間計算量がかかるが、実際何KBくらいなのかわからないのでChatGPTに聞いてみた。
            - オブジェクト参照は8byteだが、setがハッシュテーブルであることを考えると、1要素あたり50~100byteくらいらしい。
            - 1要素50byteとすると、ノード数が1000個で約50KB、100万個で50MBくらい
                - 100万までくると大分違うなと思った

## 解法1
```Python3
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast = head
        slow = head

        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if fast == slow:
                break
        else:
            return None
        
        origin = head
        while origin != slow:
            origin = origin.next
            slow = slow.next
        return origin
```

## 解法2
```Python3
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited = set()
        ptr = head

        while ptr:
            if ptr in visited:
                return ptr
            visited.add(ptr)
            ptr = ptr.next
        return None
```





# Step3
- 解法1で最終確認にトライ

```Python3
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast = head
        slow = head

        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next

            if fast == slow:
                break
        else:
            return None

        origin = head
        while origin != slow:
            origin = origin.next
            slow = slow.next
        return origin
```
