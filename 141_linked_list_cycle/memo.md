## Link
https://leetcode.com/problems/linked-list-cycle/description/


## How to work on each step
- Step 1: 答えを見ずに5分以内に解く。わからなかったら答えを見て、開始から答えを見ないで5分以内に正解になるところまで行う。
- Step 2: 本協会メンバーやLeetCodeの過去解答を参考にしつつ、コードを見やすくする形で整える。
- Step 3: 全部消して、10分以内にエラーを一度も出さずに正解するのを3回続けて行う。
- Step 4: いただいたレビューをもとに、コードを整える。


## step1
前から順番にノードを確認し、visitedリストに訪れたノードを保存していく。
1) ループが途切れている場合: 次のノードが存在しない
2) ループを一巡した場合: visitedに既にノードが存在する
のいずれかが該当するので、そこで答えを返せばよさそう。

ただ、returnがどちらもif文の下にあるので、入力が問題の制約から外れた場合無限ループが起きるかもしれない。

## step2
- LeetCodeで[他の解法](https://leetcode.com/problems/linked-list-cycle/solutions/6086375/video-using-two-pointers-by-niits-yqeh/)を確認した。
    - fastとslowという2つのノードを移動させていく。一度にfastは2個、slowは1個前に進む。
    - ループがない場合、fastが終端の一個前(fast.nextがNone)か終端(fastがNone)に着いた時点でwhile文が終了してFalseが返される。
    - ループがある場合、fastとslowの距離の差はループ一周分で、fastがslowに1ずつ追いつくので必ずどこかでfast==slowになって、Trueが返される。
- step1のコードと比べ、時間はループ1周分なので大きく変わらなそうだし、visitedを使わないためメモリを消費しないのでこちらの方が良さそう。
    - 入力がNoneの場合や、ノードが一つの場合もwhile文の条件式で対応できる。

## step3




