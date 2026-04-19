# 第3章 条件分岐と繰り返し

## この章のゴール
- 条件で動きを変えられる
- 同じ処理をくり返せる
- ゲームっぽい分岐を作れる

### 3-1. if文

```python
age = int(input("年齢を入力してください: "))

if age >= 20:
    print("大人です")
else:
    print("未成年です")
```
`if` は「もし〜なら」です。条件が満たされたときだけ処理を実行します。

### 3-2. 比較と組み合わせ

```python
score = 72

if score >= 80:
    print("すごい")
elif score >= 60:
    print("よくできました")
else:
    print("もう少し")
```
```python
age = 25
money = 1000

if age >= 20 and money >= 500:
    print("入店できます")
```
### 3-3. for文とwhile文

```python
for i in range(5):
    print(i)
```
```python
count = 0
while count < 3:
    print("count =", count)
    count += 1
```
`for` は回数が決まっているときに向いています。`while` は条件が続く間くり返したいときに使います。

### 3-4. break と continue

```python
for i in range(10):
    if i == 5:
        break
    print(i)
```
```python
for i in range(5):
    if i == 2:
        continue
    print(i)
```
- `break` はループをやめる
- `continue` は今の回だけ飛ばして次へ進む

> 章末ミニゲーム  
> **じゃんけん判定** を作りましょう。

```python
hand = input("グー・チョキ・パーのどれかを入力: ")

if hand == "グー":
    print("あなたはグー")
elif hand == "チョキ":
    print("あなたはチョキ")
elif hand == "パー":
    print("あなたはパー")
else:
    print("入力がちがいます")
```
