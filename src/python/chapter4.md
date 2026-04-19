# 第4章 リスト・辞書・セット・タプル

## この章のゴール
- 複数のデータをまとめて扱える
- RPGの持ち物やキャラクター情報を表せる

### 4-1. リスト

```python
fruits = ["apple", "banana", "orange"]
print(fruits[0])
fruits.append("grape")
print(fruits)
```
リストは順番のあるデータの集まりです。ゲームでは、アイテム一覧や敵の候補に使います。

### 4-2. 辞書

```python
user = {"name": "Taro", "age": 20}
print(user["name"])
user["age"] = 21
print(user)
```
辞書は、名前と値を対応させて覚えるのが得意です。RPGでは、キャラクターのHP、攻撃力、防御力などをまとめるのに向いています。

### 4-3. セットとタプル

```python
items = {"potion", "key", "map"}
position = (3, 5)
```
- セット: 重複を自動で避けたいとき
- タプル: 変えない値をまとめたいとき

### 4-4. ループとの組み合わせ

```python
for fruit in fruits:
    print("好きな果物:", fruit)
```
> 章末ミニゲーム  
> **アイテム袋** を作りましょう。

```python
bag = ["ポーション", "鍵", "地図"]

print("持ち物:")
for item in bag:
    print("-", item)
```
