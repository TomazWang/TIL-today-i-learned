# 來吧，和我一起做聊天機器人

> __NOTE:__ 
> 
> 這篇並不是教學文章，比較是我自己在做的學習筆記和踩雷紀錄
> 如果有什麼錯誤的地方請不吝指教～
> 

來吧，和我一做一個聊天機器人
目前是這樣規劃，利用 Line 提供的 Message API 與以 Python + Flask 為底的後台串接，來達成一個聊天機器人

所以在機器人之前，我們需要一個 Flask 後台

## 建立一個 Python 專案
聽起來好像很專業，但其實就是一個資料夾
資料夾中要包含兩個重點
1. 專案本身 `stinky-tofu`：(這裡我用 stinky-tofu ，因為我預計叫我的機器人臭豆腐機器人)
2. 模擬環境 `venv`：這個資料夾可以不用自己建

### venv 虛擬環境
``` bash
# 創建虛擬環境
python3 -m venv /path/to/new/virtual/environment
```

創建虛擬環境的目的之一是讓我們可以把這個專案需要的環境與我們系統使用的環境獨立開
另一個優點是我們可以利用 pip 把這個環境所安裝的套件清單備份，未來要架上伺服器會必較方便

建立好環境後，還要啟動環境
``` bash
# on Mac, (I'm using zsh)
source  ./venv/bin/activate
```

### Basic Flask site
[Flask][flask-doc] 是一個 Python web framwork， 這次的 line-bot 就要使用它來作為後台處理資料的接口

```bash
# 使用 pip 安裝 Flask
./venv/bin/pip install Flask
```
回到 `stinky-tofu` 這個資料夾中，創建一個資料夾 `main`，裡面放一個python 檔案 `app.py`
接下來就來按程式碼吧～

``` python
# app.py
from flask import Flask, abort, request

# 新增一個 Flask app，就叫做 app
app = Flask(__name__)


# 建立你的第一個頁面
app.route('/')
def home():
    return 'Hello tomazium bot.'


# 別忘了運行
if __name__ == "__main__":
    app.run()
```

## 讓網頁 show 出來吧
```bash
./venv/bin/python3 app.py
```

使用模擬器的環境（這裡才有 Flask ) 來運行剛剛寫的小小  Flask 網頁
就可以在 localhost (127.0.0.1) 看到囉



[flask-doc]:http://flask.pocoo.org/docs/0.12/