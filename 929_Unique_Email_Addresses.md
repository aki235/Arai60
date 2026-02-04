# Link
https://leetcode.com/problems/unique-email-addresses/description/


# Step1
- メールアドレスを正規化して、setを使ってユニークな個数を求める
- `@`を基準にドメイン名とローカル名に分割したい。うまく分割できなかった時はValueErrorが適切か
- ローカル名の処理
    - `+`でsplitしてから0番目の要素を取り、その後`.`をreplaceで取り除くのがよさそう
        - splitとreplaceの書き方を調べた。この辺よく忘れてしまう。
            - "文字列".(関数名(引数))の形で、各関数はstrクラスのメソッドとして定義されているからこのように書けると理解すると覚えやすい。 https://docs.python.org/3/library/stdtypes.html#text-and-binary-sequence-type-methods-summary
- 時間計算量はO(N * M) (N: メールアドレスの数、M: メールアドレスの長さ)、空間計算量はO(N * M)
    - N, M <= 100なので全然大丈夫そう
- 入力が不正な場合にエラーが発生しないかチェック
    - `@`が一個でない場合は、`if len(email.split("@")) != 2:`の部分で弾けている (※leetcode上のみでは考えなくてもよい)
        - 判定のため1回余分にsplitしているため、O(N)だが定数倍時間がかかる。ここでの入力サイズだと問題なさそうだけど、速くしたい場合は`parts = email.split("@")`など変数に代入して使い回すのがよさそう
    - `normalized_local_name = (local_name.replace(".", "")).split("+")[0]`でも、IndexErrorは特に発生しなさそう

解答1
```Python3
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        normalized_emails = set()

        for email in emails:
            if len(email.split("@")) != 2:
                raise ValueError("An email address should contain exactly one '@'.")
            local_name, domain_name = email.split("@")

            normalized_local_name = (local_name.replace(".", "")).split("+")[0]
            normalized_emails.add(normalized_local_name + "@" + domain_name)
        return len(normalized_emails)
```

# Step2
## 読んだコード
- https://github.com/olsen-blue/Arai60/pull/14
- https://github.com/potrue/leetcode/pull/14
- https://github.com/tokuhirat/LeetCode/pull/14
- https://github.com/SuperHotDogCat/coding-interview/pull/30
- https://github.com/hayashi-ay/leetcode/pull/25


## 感想
- 正規表現やfor文を使った方法もある
    - 正規表現
        - [olsen-blue/Arai60](https://github.com/olsen-blue/Arai60/blob/olsen-blue-patch-14/929.%20Unique%20Email%20Addresses.md)
    - for文
        - [hayashi-ay/leetcode](https://github.com/hayashi-ay/leetcode/blob/hayashi-ay-patch-14/929.%20Unique%20Email%20Addresses.md)

- `@`が1個ではない時の処理について
    - RFC5322のメールアドレスの規格では、`@`は2個以上含まれる可能性がある
        - https://datatracker.ietf.org/doc/html/rfc5322#section-3.4.1
            - local-partに`@`が含まれうる
    - `@`が0個の場合はエラー、2個以上の場合は最後の`@`を基準に分割する形で実装してみた


- Pythonでは文字列はイミュータブルなので追記には再構築が走る([参照](https://github.com/hayashi-ay/leetcode/blob/hayashi-ay-patch-14/929.%20Unique%20Email%20Addresses.md))
    - 追記を何度も行うような場合は、リストに追加しておいてjoinが良い
        - 特に、for文による正規化手法のように、追記を繰り返している場合など
    - f-stringは推奨
        - Googleのスタイルガイドでは`+`による結合より推奨されている
            - https://google.github.io/styleguide/pyguide.html#310-strings
            - https://github.com/yas-2023/leetcode_arai60/pull/20#discussion_r2445615710
            - ただし、最適化が走ることもあるくらいで環境依存
        - "A single join with + is okay but do not format with +."と記載があったので、f-stringに修正する

解答1 (split、修正版)
```Python3
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        normalized_emails = set()

        for email in emails:
            if not "@" in email:
                raise ValueError("An email address must contain '@'.")
            local_name, domain_name = email.rsplit("@", 1)

            normalized_local_name = (local_name.replace(".", "")).split("+")[0]
            normalized_emails.add(f"{normalized_local_name}@{domain_name}")
        return len(normalized_emails)
```

解答2 (正規表現)
```Python3
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        normalized_emails = set()

        for email in emails:
            if not "@" in email:
                raise ValueError("An email address must contain '@'.")
            local_name, domain_name = email.rsplit("@", 1)
            plus_ignored_local_name = re.sub(r"\+.*", "", local_name)
            dot_removed_local_name = re.sub(r"\.", "", plus_ignored_local_name)

            normalized_emails.add(f"{dot_removed_local_name}@{domain_name}")
        return len(normalized_emails)
```

# Step3
解答1、解答2で最終確認

解答1 (split、修正版)
```Python3
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        normalized_emails = set()

        for email in emails:
            if not "@" in email:
                raise ValueError("An email address must contain '@'.")
            local_name, domain_name = email.rsplit("@", 1)

            normalized_local_name = (local_name.replace(".", "")).split("+")[0]
            normalized_emails.add(f"{normalized_local_name}@{domain_name}")
        return len(normalized_emails)
```

解答2 (正規表現)
```Python3
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        normalized_emails = set()

        for email in emails:
            if not "@" in email:
                raise ValueError("An email must contain '@'.")
            local_name, domain_name = email.rsplit("@", 1)

            plus_ignored_local_name = re.sub(r"\+.*", "", local_name)
            dot_removed_local_name = re.sub(r"\.", "", plus_ignored_local_name)

            normalized_emails.add(f"{dot_removed_local_name}@{domain_name}")
        
        return len(normalized_emails)
```