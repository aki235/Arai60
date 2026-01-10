# Link


# Step1
- 単純な解放としては、addが実行されるごとに、numsをソートして、k番目を返せば良いが、それだと不要な計算が多いので、問題の意図とは違いそう
    - k番目に大きい数さえ求めればいいので、numsは長さkまで切り詰められそう
    - また、1回のadd呼び出しでは、1個しか数を追加しないので、ソートもする必要がなく、valとリストの要素を先頭から大小比較を行い、適切な位置に追加してあげればいい

- ただ、例外処理も考えた方がいいが、どういう順番で処理を書くと、コードが簡潔になるか考える。
    - 与えられたリストがkより小さかった場合の処理(※leetcodeではこのようなケースはないが、考慮するべきだと思ったため)

- 順番としては、以下を考えた。
    - numsを新規作成/編集するごとに、常に降順に並んでおり、かつ長さがk以下になるように維持する
    - __init__関数では、numsをソートして、k個以下になるように切り詰める
    - add関数では、
        - まず、valをリストの大小関係を保つ位置に追加する。
        - 次に、k以下になるように、リストの切り詰めを行う。
        - 値を返す
            - リストの長さがkより小さかった場合、Noneを返す
            - それ以外の場合、リストの最後の要素を返す


```Python3
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums_sorted = sorted(nums, reverse=True)

        if len(self.nums_sorted) > k:
            self.nums_sorted = [self.nums_sorted[i] for i in range(k)]
        return None

    def add(self, val: int) -> int:
        for i in range(len(self.nums_sorted)):
            if val > self.nums_sorted[i]:
                self.nums_sorted.insert(i, val)
                break
        if val <= self.nums_sorted[-1]:
            self.nums_sorted.append(val)

        print(self.nums_sorted)
        
        while len(self.nums_sorted) > self.k:
            self.nums_sorted.pop()
        
        if len(self.nums_sorted) < self.k:
            return None
        
        return self.nums_sorted[-1]
```


# Step2





# Step3