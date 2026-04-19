# 第10章 RPG完成プロジェクト

## この章のゴール
- 小さなRPGを完成させる
- 戦闘、成長、回復、探索、セーブをひと通り入れる
- そのまま改造して、オリジナル作品の土台にする

この章は、本書の最終目標です。ここまでで学んだ変数、条件分岐、ループ、リスト、辞書、関数、ファイル、クラスを総動員して、**遊べる形のテキストRPG** を作ります。

ここで大切なのは、最初から大作を目指さないことです。完成しない大きなゲームより、**小さいけれど最後まで動くゲーム** のほうが、学習としてずっと価値があります。

---

## 10-1. 完成形を先に決める

まず、完成品の形を小さく決めます。今回は次の機能が入っていれば十分です。

- プレイヤーの名前、HP、攻撃力、防御力
- 敵との戦闘
- 攻撃、回復、逃げるの選択
- 経験値とレベルアップ
- ポーションなどのアイテム
- 村で回復と買い物
- セーブとロード
- ボス戦
- クリア表示

この規模なら、初心者でも「作り切る」ことができます。しかも、作ったあとに改造しやすいです。

### 10-1-1. いきなり完成形を見ない理由

最初に完成コードだけを見ても、途中で何を学んでいるのかが見えにくくなります。そこでこの章では、次の順番で作ります。

1. キャラクターをクラスにする
2. 戦闘だけを作る
3. 敵を増やす
4. 村を作る
5. セーブとロードを入れる
6. 最後に1本のRPGとしてまとめる

この順番だと、壊れたときにどこで壊れたかも追いやすくなります。

---

## 10-2. まずは戦闘だけを作る

RPGの中心は戦闘です。最初は、たった1回の戦闘が動けばOKです。

```python
def calc_damage(attack, defense):
    return max(1, attack - defense)


player_attack = 8
enemy_defense = 1
print(calc_damage(player_attack, enemy_defense))
```

#### このコードの見方

- `calc_damage()` はダメージ計算の関数です。
- `max(1, ...)` にしているのは、ダメージが0で止まらないようにするためです。
- どんなに防御が高くても、最低1は削れるようにしています。

RPGでは「攻撃しても何も起きない」状態が続くと、遊んでいてつまらなくなります。だから初心者向けのRPGでは、最低ダメージを1にしておくと安定します。

### 改造してみよう

- 最低ダメージを `0` にしてみる
- その代わりに、強い敵には別の演出を入れる
- クリティカルヒットを追加する

```python
import random

def calc_damage(attack, defense):
    base = max(1, attack - defense)
    if random.random() < 0.1:
        print("クリティカル!")
        return base * 2
    return base
```

---

## 10-3. キャラクターをクラスで整理する

次は、プレイヤーと敵をクラスで整理します。ここが第9章の本番です。

```python
class Character:
    def __init__(self, name, max_hp, attack, defense):
        self.name = name
        self.max_hp = max_hp
        self.hp = max_hp
        self.attack = attack
        self.defense = defense

    def is_alive(self):
        return self.hp > 0

    def take_damage(self, damage):
        self.hp = max(0, self.hp - damage)

    def heal(self, amount):
        self.hp = min(self.max_hp, self.hp + amount)

    def show(self):
        print(f"{self.name} HP:{self.hp}/{self.max_hp} ATK:{self.attack} DEF:{self.defense}")
```

#### このコードの見方

- `max_hp` は最大HPです。
- `hp` は現在HPです。
- `take_damage()` はHPを減らします。
- `heal()` はHPを回復します。
- `show()` は状態表示です。

このクラスがあると、勇者も敵も同じ形で扱えます。戦闘のコードがかなり読みやすくなります。

```python
class Player(Character):
    def __init__(self, name):
        super().__init__(name, 30, 8, 3)
        self.level = 1
        self.exp = 0
        self.gold = 0
        self.items = {"ポーション": 2}

    def gain_exp(self, amount):
        self.exp += amount
        while self.exp >= 10:
            self.exp -= 10
            self.level += 1
            self.max_hp += 5
            self.attack += 2
            self.defense += 1
            self.hp = self.max_hp
            print("レベルアップ!")
```

#### このコードの見方

- `Player` は `Character` を受け継ぎます。
- `level`、`exp`、`gold`、`items` はプレイヤーだけが持つ情報です。
- `gain_exp()` の中で `while` を使っているのは、一度にたくさん経験値をもらったときに複数回レベルアップできるようにするためです。

```python
class Enemy(Character):
    def __init__(self, name, max_hp, attack, defense, exp, gold):
        super().__init__(name, max_hp, attack, defense)
        self.exp = exp
        self.gold = gold
```

#### このコードの見方

- 敵はキャラクターの一種です。
- 敵だけが `exp` と `gold` を持ちます。
- 同じ仕組みを使いつつ、必要な情報だけ足しています。

### 改造してみよう

- `Player` の初期HPを 40 にしてみる
- `Enemy` に `drop_item` を追加する
- `gain_exp()` で上がる能力値を変えてみる

---

## 10-4. 戦闘ループを作る

戦闘は、プレイヤーが行動を選び、敵が反撃し、どちらかが倒れるまで続くループです。

```python
def battle(player, enemy):
    print(f"
{enemy.name} があらわれた!")
    while player.is_alive() and enemy.is_alive():
        print("
--- 状態 ---")
        player.show()
        enemy.show()
        print("1: 攻撃  2: ポーション  3: にげる")
        choice = input("行動: ")

        if choice == "1":
            damage = calc_damage(player.attack, enemy.defense)
            enemy.take_damage(damage)
            print(f"{player.name} の攻撃! {enemy.name} に {damage} のダメージ")
        elif choice == "2":
            if player.items.get("ポーション", 0) > 0:
                player.items["ポーション"] -= 1
                player.heal(10)
                print("ポーションを使った。HPが少し回復した。")
            else:
                print("ポーションがない。")
                continue
        elif choice == "3":
            if random.random() < 0.5:
                print("うまく逃げ切った!")
                return
            else:
                print("逃げられなかった!")
        else:
            print("入力がちがいます")
            continue

        if enemy.is_alive():
            damage = calc_damage(enemy.attack, player.defense)
            player.take_damage(damage)
            print(f"{enemy.name} の攻撃! {player.name} に {damage} のダメージ")

    if player.is_alive():
        print(f"{enemy.name} を倒した!")
        player.gold += enemy.gold
        player.gain_exp(enemy.exp)
        print(f"{enemy.gold}G と {enemy.exp}EXP を手に入れた")
    else:
        print("あなたは倒れた...")
```

#### このコードの見方

- `while player.is_alive() and enemy.is_alive():` で戦闘を続けます。
- `choice == "1"` は攻撃です。
- `choice == "2"` は回復です。
- `choice == "3"` は逃走です。
- `continue` を使うと、そのターンの残りを飛ばして入力待ちに戻れます。

戦闘ループの本質は「状態を見せる → 行動を選ぶ → 結果を反映する」の繰り返しです。RPGでは、この流れが何度も出てきます。

### 改造してみよう

- 逃げる成功率を 50% から 70% に変える
- ポーションの回復量を 10 から 20 にする
- 敵がたまに会心攻撃をするようにする

---

## 10-5. 敵の種類を増やして探索を面白くする

敵が1種類だけだと、すぐに飽きます。そこで、複数の敵からランダムに出すようにします。

```python
def make_enemy(player_level):
    enemies = [
        Enemy("スライム", 12, 4, 1, 5, 3),
        Enemy("コウモリ", 15, 5, 1, 6, 4),
        Enemy("オオカミ", 18, 6, 2, 8, 6),
    ]
    if player_level >= 3:
        enemies.append(Enemy("ゴブリン", 22, 7, 2, 10, 8))
    return random.choice(enemies)
```

#### このコードの見方

- `enemies` に複数の敵を入れます。
- `random.choice()` でランダムに1体選びます。
- レベルが上がると、少し強い敵も出るようにしています。

### 改造してみよう

- `ゴブリン` の代わりに別のモンスターを追加する
- レベル5以上でさらに強い敵を出す
- 地域ごとに敵の一覧を変える

---

## 10-6. 村を作る

RPGには、戦うだけでなく休める場所が必要です。村では回復したり、ポーションを買ったりできます。

```python
def town(player):
    while True:
        print("
=== 村 ===")
        print("1: 回復  2: ポーション購入  3: 状態を見る  4: 出る")
        choice = input("行動: ")

        if choice == "1":
            player.hp = player.max_hp
            print("HPが全回復した")
        elif choice == "2":
            if player.gold >= 5:
                player.gold -= 5
                player.items["ポーション"] = player.items.get("ポーション", 0) + 1
                print("ポーションを買った")
            else:
                print("お金が足りない")
        elif choice == "3":
            player.show()
            print("LV", player.level, "EXP", player.exp, "G", player.gold)
            print("持ち物:", player.items)
        elif choice == "4":
            break
        else:
            print("入力がちがいます")
```

#### このコードの見方

- 村の中でも `while True` を使っています。
- 村から出るときだけ `break` でループを抜けます。
- `player.items.get("ポーション", 0)` は、持っていなかったときの安全策です。

### 改造してみよう

- 町の回復料金を作る
- 武器屋を追加する
- 宿屋に「レベル上げが少し有利になる」効果をつける

---

## 10-7. セーブとロードを入れる

せっかく育てたキャラクターは、保存できると嬉しいです。ここでは JSON を使います。

```python
import json
from pathlib import Path

SAVE_FILE = Path("rpg_save.json")
```

```python
def save_game(player):
    data = {
        "name": player.name,
        "max_hp": player.max_hp,
        "hp": player.hp,
        "attack": player.attack,
        "defense": player.defense,
        "level": player.level,
        "exp": player.exp,
        "gold": player.gold,
        "items": player.items,
    }
    SAVE_FILE.write_text(json.dumps(data, ensure_ascii=False, indent=2), encoding="utf-8")
    print("セーブしました")
```

```python
def load_game():
    if not SAVE_FILE.exists():
        return None
    data = json.loads(SAVE_FILE.read_text(encoding="utf-8"))
    player = Player(data["name"])
    player.max_hp = data["max_hp"]
    player.hp = data["hp"]
    player.attack = data["attack"]
    player.defense = data["defense"]
    player.level = data["level"]
    player.exp = data["exp"]
    player.gold = data["gold"]
    player.items = data["items"]
    return player
```

#### このコードの見方

- `save_game()` は今の状態を書き出します。
- `load_game()` は保存した内容を読み戻します。
- `Path` を使うと、ファイル操作が少し読みやすくなります。

RPGは「次回も続きから遊べる」だけで、ぐっとゲームらしくなります。

### 改造してみよう

- セーブデータを複数保存できるようにする
- ロード失敗時にメッセージをもっと親切にする
- `items` に武器や防具も追加する

---

## 10-8. 完成版コード

ここからは、ここまでの部品をひとつにまとめます。まずはそのまま動かして、あとで少しずつ改造するのがいちばん学びやすいです。

```python
import random
import json
from pathlib import Path

SAVE_FILE = Path("rpg_save.json")

class Character:
    def __init__(self, name, max_hp, attack, defense):
        self.name = name
        self.max_hp = max_hp
        self.hp = max_hp
        self.attack = attack
        self.defense = defense

    def is_alive(self):
        return self.hp > 0

    def take_damage(self, damage):
        self.hp = max(0, self.hp - damage)

    def heal(self, amount):
        self.hp = min(self.max_hp, self.hp + amount)

    def show(self):
        print(f"{self.name} HP:{self.hp}/{self.max_hp} ATK:{self.attack} DEF:{self.defense}")

class Player(Character):
    def __init__(self, name):
        super().__init__(name, 30, 8, 3)
        self.level = 1
        self.exp = 0
        self.gold = 0
        self.items = {"ポーション": 2}

    def gain_exp(self, amount):
        self.exp += amount
        while self.exp >= 10:
            self.exp -= 10
            self.level += 1
            self.max_hp += 5
            self.attack += 2
            self.defense += 1
            self.hp = self.max_hp
            print("レベルアップ!")

class Enemy(Character):
    def __init__(self, name, max_hp, attack, defense, exp, gold):
        super().__init__(name, max_hp, attack, defense)
        self.exp = exp
        self.gold = gold

def calc_damage(attack, defense):
    return max(1, attack - defense)

def make_enemy(player_level):
    enemies = [
        Enemy("スライム", 12, 4, 1, 5, 3),
        Enemy("コウモリ", 15, 5, 1, 6, 4),
        Enemy("オオカミ", 18, 6, 2, 8, 6),
    ]
    if player_level >= 3:
        enemies.append(Enemy("ゴブリン", 22, 7, 2, 10, 8))
    return random.choice(enemies)

def battle(player, enemy):
    print(f"
{enemy.name} があらわれた!")
    while player.is_alive() and enemy.is_alive():
        print("
--- 状態 ---")
        player.show()
        enemy.show()
        print("1: 攻撃  2: ポーション  3: にげる")
        choice = input("行動: ")

        if choice == "1":
            damage = calc_damage(player.attack, enemy.defense)
            enemy.take_damage(damage)
            print(f"{player.name} の攻撃! {enemy.name} に {damage} のダメージ")
        elif choice == "2":
            if player.items.get("ポーション", 0) > 0:
                player.items["ポーション"] -= 1
                player.heal(10)
                print("ポーションを使った。HPが少し回復した。")
            else:
                print("ポーションがない。")
                continue
        elif choice == "3":
            if random.random() < 0.5:
                print("うまく逃げ切った!")
                return
            else:
                print("逃げられなかった!")
        else:
            print("入力がちがいます")
            continue

        if enemy.is_alive():
            damage = calc_damage(enemy.attack, player.defense)
            player.take_damage(damage)
            print(f"{enemy.name} の攻撃! {player.name} に {damage} のダメージ")

    if player.is_alive():
        print(f"{enemy.name} を倒した!")
        player.gold += enemy.gold
        player.gain_exp(enemy.exp)
        print(f"{enemy.gold}G と {enemy.exp}EXP を手に入れた")
    else:
        print("あなたは倒れた...")

def town(player):
    while True:
        print("
=== 村 ===")
        print("1: 回復  2: ポーション購入  3: 状態を見る  4: 出る")
        choice = input("行動: ")

        if choice == "1":
            player.hp = player.max_hp
            print("HPが全回復した")
        elif choice == "2":
            if player.gold >= 5:
                player.gold -= 5
                player.items["ポーション"] = player.items.get("ポーション", 0) + 1
                print("ポーションを買った")
            else:
                print("お金が足りない")
        elif choice == "3":
            player.show()
            print("LV", player.level, "EXP", player.exp, "G", player.gold)
            print("持ち物:", player.items)
        elif choice == "4":
            break
        else:
            print("入力がちがいます")

def save_game(player):
    data = {
        "name": player.name,
        "max_hp": player.max_hp,
        "hp": player.hp,
        "attack": player.attack,
        "defense": player.defense,
        "level": player.level,
        "exp": player.exp,
        "gold": player.gold,
        "items": player.items,
    }
    SAVE_FILE.write_text(json.dumps(data, ensure_ascii=False, indent=2), encoding="utf-8")
    print("セーブしました")

def load_game():
    if not SAVE_FILE.exists():
        return None
    data = json.loads(SAVE_FILE.read_text(encoding="utf-8"))
    player = Player(data["name"])
    player.max_hp = data["max_hp"]
    player.hp = data["hp"]
    player.attack = data["attack"]
    player.defense = data["defense"]
    player.level = data["level"]
    player.exp = data["exp"]
    player.gold = data["gold"]
    player.items = data["items"]
    return player

def main():
    print("=== 小さなRPG ===")
    choice = input("1: 新しく始める  2: 続きから始める: ")

    if choice == "2":
        player = load_game()
        if player is None:
            print("セーブデータがありません。新規開始します。")
            name = input("名前を入力してください: ")
            player = Player(name)
    else:
        name = input("名前を入力してください: ")
        player = Player(name)

    boss_defeated = False

    while player.is_alive() and not boss_defeated:
        print("
=== メニュー ===")
        print("1: 旅に出る  2: 村へ行く  3: セーブ  4: やめる")
        choice = input("行動: ")

        if choice == "1":
            enemy = make_enemy(player.level)
            battle(player, enemy)

            if not player.is_alive():
                break

            if player.level >= 3 and random.random() < 0.3 and not boss_defeated:
                boss = Enemy("魔王の手下", 25, 8, 3, 12, 15)
                battle(player, boss)

            if player.level >= 5 and player.is_alive() and not boss_defeated:
                boss = Enemy("魔王", 40, 10, 4, 20, 50)
                battle(player, boss)
                if player.is_alive():
                    boss_defeated = True
                    print("世界に平和が戻った!")
        elif choice == "2":
            town(player)
        elif choice == "3":
            save_game(player)
        elif choice == "4":
            print("ゲームを終了します")
            break
        else:
            print("入力がちがいます")

    if not player.is_alive():
        print("ゲームオーバー")
    elif boss_defeated:
        print("クリア!")

if __name__ == "__main__":
    main()
```

### この完成版のポイント

- `Character` で共通部分をまとめている
- `Player` だけにレベルや持ち物を持たせている
- `make_enemy()` で敵を増やしやすくしている
- `town()` で休憩と買い物ができる
- `save_game()` と `load_game()` で続きから遊べる
- `main()` がゲーム全体の流れをまとめている

### 10-9. どこを直せば、もっと面白くなるか

完成したあとに改造すると、理解が一気に深まります。次の順番でいじると安全です。

#### 1. 難しさを変える

- 敵のHPを増やす
- ポーションの回復量を下げる
- 逃走成功率を変える

#### 2. 敵を増やす

`make_enemy()` のリストに新しい敵を足すだけで、世界が広がります。

```python
enemies.append(Enemy("ゾンビ", 28, 7, 3, 12, 9))
```

#### 3. 武器や防具を追加する

`items` の中に武器名を入れ、攻撃力や防御力を変えるようにすると、かなりRPGらしくなります。

#### 4. 街を増やす

村だけでなく、森、洞窟、城下町などを追加すると、冒険の流れがはっきりします。

#### 5. ファイルを分ける

慣れてきたら、`battle.py`、`town.py`、`main.py` のように分けると、さらに読みやすくなります。

### 10-10. ここまで来たら次にできること

このRPGは完成品のゴールであると同時に、改造の出発点でもあります。次のように広げると、学習が一気に進みます。

- 敵の種類を増やす
- アイテムを増やす
- 装備を追加する
- 街を複数にする
- マップ移動を入れる
- 必殺技を入れる
- イベントを入れる
- エンディング分岐を作る
- 別ファイルに分けて整理する

> 章末ミニゲーム
> **自作RPGの第一歩** として、まずは「戦う」「回復する」「村で休む」の3機能だけを動かしてみましょう。完成の近道は、最初から全部を作ることではなく、動く部分を1つずつ増やすことです。

### 10-11. 仕上げのチェック

- ゲームは最後まで動くか
- セーブとロードはできるか
- レベルアップで強くなっているか
- 村が戦闘の休憩場所になっているか
- 敵を増やしたとき、コードが壊れにくいか

