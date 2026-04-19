# 第8章 ファイル操作

## この章のゴール
- データを保存できる
- 読み込みと書き込みができる
- RPGのセーブの考え方を持てる

### 8-1. 書き込み

```python
with open("test.txt", "w", encoding="utf-8") as f:
    f.write("こんにちは\n")
    f.write("Python\n")
```
### 8-2. 読み込み

```python
with open("test.txt", "r", encoding="utf-8") as f:
    text = f.read()
    print(text)
```
### 8-3. セーブデータの考え方

RPGでは、プレイヤーの名前、HP、所持アイテム、進行状況などを保存したくなります。最初は難しく考えず、"文字として書いて、また読み込む" と覚えれば十分です。

```python
save_text = "name=勇者,hp=30,mp=10"

with open("save.txt", "w", encoding="utf-8") as f:
    f.write(save_text)
```
> 章末ミニゲーム  
> **日記アプリ** を作りましょう。

```python
text = input("今日のひとこと: ")

with open("diary.txt", "a", encoding="utf-8") as f:
    f.write(text + "\n")

print("保存しました")
```

