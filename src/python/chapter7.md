# 第7章 エラーと上手につきあう

## この章のゴール
- エラーメッセージを怖がらない
- try-except を使える
- 入力ミスに強いコードを書く

### 7-1. エラーの種類

よく出るのは次の2つです。

- 構文エラー: 書き方がまちがっている
- 実行時エラー: 書き方は正しいが、動かしたときに問題が起きる

### 7-2. エラーメッセージの読み方

大事なのは、全部を読むことではありません。まずは"一番下の行"を見て、何が起きたかを確認しましょう。

### 7-3. try-except

```python
try:
    x = int(input("数字を入力してください: "))
    print("2倍は", x * 2)
except ValueError:
    print("数字を入力してください")
```
### 7-4. 入力に強い数あて

```python
import random

answer = random.randint(1, 5)

try:
    guess = int(input("1〜5の数字を当ててください: "))
    if guess == answer:
        print("正解!")
    else:
        print("はずれ。答えは", answer)
except ValueError:
    print("数字を入力してください")
```
> 章末ミニゲーム  
> **入力ミスに強いクイズ** を作りましょう。

```python
try:
    age = int(input("年齢を入力してください: "))
    if age >= 20:
        print("大人です")
    else:
        print("まだ20歳未満です")
except ValueError:
    print("数字で入力してください")
```
