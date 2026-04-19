# 第9章 オブジェクト指向入門

## この章のゴール
- クラスの考え方を理解する
- RPGのキャラクターを表現できる
- 「状態」と「行動」をひとまとめにできる
- 第10章のRPG完成にそのままつなげる

### 9-1. クラスとは何か

クラスは、**設計図**のようなものです。設計図そのものはキャラクターではありませんが、設計図から「勇者」や「スライム」の実体を作れます。

RPGでは、名前、HP、攻撃力、防御力のような「データ」と、攻撃する、回復する、倒れるといった「ふるまい」を一緒に持たせたくなります。そこでクラスが役に立ちます。

```python
class Dog:
    def __init__(self, name):
        self.name = name

    def bark(self):
        print(self.name + "はワンと鳴いた")


dog = Dog("ポチ")
dog.bark()
```

#### このコードの見方

- `class Dog:` でクラスを作ります。
- `__init__` は、オブジェクトを作るときに最初に呼ばれる関数です。
- `self.name = name` で、その犬自身の名前を覚えます。
- `bark()` は、その犬が持つ行動です。

### 9-2. インスタンスは「実体」

クラスが設計図なら、インスタンスはそこから作られた実物です。

```python
class Character:
    def __init__(self, name, hp, attack):
        self.name = name
        self.hp = hp
        self.attack = attack

    def show(self):
        print(self.name, "HP:", self.hp, "攻撃:", self.attack)


hero = Character("勇者", 30, 8)
hero.show()
```

`hero` は `Character` クラスから作られた1体のキャラクターです。クラスを使うと、同じ形のキャラクターを何体でも作れます。

### 9-3. メソッドで行動を表す

RPGでは、HPを減らす、回復する、生きているか確かめる、という処理を何度も使います。これらはメソッドにすると見通しがよくなります。

```python
class Character:
    def __init__(self, name, hp, attack):
        self.name = name
        self.hp = hp
        self.attack = attack

    def take_damage(self, damage):
        self.hp = max(0, self.hp - damage)

    def heal(self, amount):
        self.hp += amount

    def is_alive(self):
        return self.hp > 0
```

#### このコードの見方

- `take_damage()` はダメージを受ける処理です。
- `heal()` は回復する処理です。
- `is_alive()` は生きているかどうかを返します。

このように、「何をしたいか」が名前で伝わると、あとで読み返したときに理解しやすくなります。

### 9-4. 継承は「似たものをまとめる」道具

勇者も敵も、どちらも「キャラクター」です。ただし、勇者はレベルや持ち物を持ち、敵は経験値やお金を持つことが多いです。共通部分は `Character` にまとめ、違う部分だけを追加すると楽になります。

```python
class Enemy(Character):
    def __init__(self, name, hp, attack):
        super().__init__(name, hp, attack)
```

継承は便利ですが、最初から無理に使う必要はありません。まずは「共通の形を1つにまとめると、コードが読みやすくなる」と感じられれば十分です。

### 9-5. RPGに向けた考え方

RPGでは、次のようなものをクラスで表せます。

- `Player`：プレイヤー
- `Enemy`：敵
- `Item`：アイテム
- `Weapon`：武器
- `Town`：村や町

この本では、まず `Player` と `Enemy` をしっかり作ります。ここができると、第10章のRPGがぐっと作りやすくなります。

> 章末ミニゲーム
> **キャラクター表示ツール** を作りましょう。名前とHPを入力すると、そのキャラクターの情報を表示するだけの小さなプログラムです。

```python
class Character:
    def __init__(self, name, hp):
        self.name = name
        self.hp = hp

    def show(self):
        print(f"{self.name} / HP {self.hp}")


name = input("キャラクター名を入力してください: ")
hp = int(input("HPを入力してください: "))
hero = Character(name, hp)
hero.show()
```

> 改造してみよう
> 
> - `HP` だけでなく `攻撃力` も表示する
> - `show()` の表示を「勇者: HP 30 / 攻撃 8」の形にする
> - `Character` を2体作って、並べて表示する

