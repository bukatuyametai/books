# 第5章 関数で処理をまとめる

## この章のゴール
- 同じ処理を何度も書かない
- 自分で「部品」を作れる

### 5-1. 関数とは

関数は、よく使う処理をまとめたものです。"入力を受け取って、決まった仕事をして、結果を返す" 部品だと考えるとわかりやすいです。

```python
print(len("hello"))
print(len([1, 2, 3]))
```
### 5-2. 自分で関数を作る

```python
def greet():
    print("こんにちは")

greet()
greet()
```
### 5-3. 引数を受け取る

```python
def greet(name):
    print("こんにちは、" + name + "さん")

greet("Taro")
greet("Hanako")
```
### 5-4. 戻り値を返す

```python
def add(a, b):
    return a + b

result = add(3, 4)
print(result)
```
### 5-5. RPGで使う関数の考え方

```python
def show_status(name, hp, mp):
    print("名前:", name)
    print("HP:", hp)
    print("MP:", mp)

show_status("勇者", 30, 10)
```
関数を使うと、同じ見た目の処理を何度も書かなくて済みます。RPGでは、ステータス表示、ダメージ計算、回復処理などを関数にまとめると読みやすくなります。

> 章末ミニゲーム  
> **ダメージ計算機** を作りましょう。

```python
def damage(attack, defense):
    return max(1, attack - defense)

player_attack = 10
enemy_defense = 3

print("ダメージ:", damage(player_attack, enemy_defense))
```
