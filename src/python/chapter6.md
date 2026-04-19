# 第6章 モジュールとライブラリ

## この章のゴール
- 便利な機能を使える
- 天気の情報を取って表示できる
- 外部ライブラリの意味がわかる

### 6-1. import とは

```python
import random
print(random.randint(1, 6))
```
### 6-2. 標準ライブラリ

```python
import math
import random
from datetime import datetime

print(math.sqrt(9))
print(random.choice(["A", "B", "C"]))
print(datetime.now())
```
### 6-3. Open-Meteo を使った天気表示

この本では、天気取得に Open-Meteo を使います。APIキーなしで使えるので、学習用としても扱いやすいです。

```python
import requests

lat = 35.6812
lon = 139.7671

url = "https://api.open-meteo.com/v1/forecast"
params = {
    "latitude": lat,
    "longitude": lon,
    "current": "temperature_2m,wind_speed_10m",
    "timezone": "Asia/Tokyo"
}

response = requests.get(url, params=params, timeout=10)
data = response.json()

current = data["current"]
print("現在の気温:", current["temperature_2m"])
print("現在の風速:", current["wind_speed_10m"])
```
### 6-4. 天気ミニゲーム

```python
temp = float(input("気温を入力してください: "))

if temp >= 25:
    print("暑いです。水分補給を忘れずに。")
elif temp >= 15:
    print("ちょうどよい気候です。")
else:
    print("寒いです。上着があると安心です。")
```
> 章末ミニゲーム  
> **天気メモアプリ** を作りましょう。現在の気温を取ってきて、感想を1行表示します。

```python
import requests

lat = 35.6812
lon = 139.7671

url = "https://api.open-meteo.com/v1/forecast"
params = {
    "latitude": lat,
    "longitude": lon,
    "current": "temperature_2m",
    "timezone": "Asia/Tokyo"
}

data = requests.get(url, params=params, timeout=10).json()
temp = data["current"]["temperature_2m"]

if temp >= 30:
    print(f"気温は {temp} 度。かなり暑いです。")
elif temp >= 20:
    print(f"気温は {temp} 度。過ごしやすいです。")
else:
    print(f"気温は {temp} 度。少し寒いかもしれません。")
```
