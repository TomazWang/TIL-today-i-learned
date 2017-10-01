# 來吧，和我一起做聊天機器人 Day 2 - Depoly on Heroku

來吧，和我一起做聊天機器人～

**今天目標是在 Heroku 上部署一個可供我們呼叫的後台**
雖然現在還沒有開始`聊天`也還沒有`機器人`的部分
但一個可以在雲端供大家呼叫的後台是必備的前置作業 


> __NOTE:__ 
> 
> 這篇並不是教學文章，比較是我自己在做的學習筆記和踩雷紀錄
> 如果有什麼錯誤的地方請不吝指教～
> 
> ---
> 前情提要：[來吧，和我一起做聊天機器人 Day 1][day_1]


## 讓大家看到我的網頁吧！

雖然 Day 1 做的網頁只有一行短短的文字
但我們更在意的是這個網頁只能在 localhost 看到
我們要把這個 app 丟上網路，為了讓 Line 未來能與我們的 app 連線

我們這次用的平台是 [Heroku]，只需要簡單的註冊，就可以有免費 5 個 app 在上面運行

進到 Heroku 中 Personal apps  的頁面，選擇右上角 __New__ > __Create New App__

### 建立新的 Heroku app
App Name 這邊填入你 app 的名稱，同時也會是你的網址 `{app-name}.herokuapp.comm`
因為同時是網址，所以有些限制，例如原則上只能使用英數和dash`-`，也不能和別人一樣

成功建立後，就可以到 heroku app 的控制台 ( dashborad ) 了

### 部署 app

接下來就是要在 heroku 上建立我們的環境，來運行 Day 1 中所建立的 app.py
還記得 Day 1 中提到的`模擬環境` 嗎？ 現在就派上用場了

我們只需要把模擬環境的設置備份下來，請 Heroku 幫我們建構一個類似的環境
我們的 app 就能輕鬆地在Heroku 上運行了

## 整理環境

### Gunicorn (Green Unicron)
在備份環境前，我們得先多安裝一個套件 [Gunicorn]，他是一個 wsgi http server，我們要使用他來幫我們運行我們寫的 wsgi 程式（Flask 程式）
> 這段我沒有很搞得清楚，希望我的解釋沒有錯誤

```bash
./venv/bin/pip install gunicorn
```

### Pip requirements
接下來，我們來看看我們用 `pip`  安裝了多少東西。

```bash
> ./venv/bin/pip freeze

# output
click==6.7
Flask==0.12.2
itsdangerous==0.24
Jinja2==2.9.6
MarkupSafe==1.0
Werkzeug==0.12.2
```

`pip freeze` 指令可以幫助我們查看“當下”到底有多少東西安裝在環境中，我們只要把這些資料放到一個叫 `requirements.txt`的檔案中就可以了

當然不是複製貼上，那外行了

```bash
# save requirements as a file.
> ../venv/bin/pip freeze > requirements.txt
```

### Procfile
我們要把 App 放上 Heroku, 我們就該教 Heroku 怎麼運行我們的 app, 這就是  Procfile 的功能
Procfile 是一個叫做 `Procfile`的檔案，沒有任何副檔名

我們一樣使用terminal 來生

```bash
# create Procfile
> echo web: gunicorn main.app:app --log-file=- > Procfile
```
這段的意思是，把 `web: gunicorn.... ` 這段加到一個檔案叫 `Procfile`
未來上傳後，Heroku 讀這個檔案就可以知道我們要 run 一個 web app
利用 gunicorn 跑 main.app 這個檔（module) 中的 app 這個 Flask app

```python
# 我們的 main/app.py 中
app = Flask(__name__)
```

> 有一些教學會寫 `web: gunicorn app:app ... ` ，根本不知道哪個 app  是哪個啊～ＱＱ


### runtime file
進到 main 資料夾，我們要告訴 Heroku 在運行這個資料夾時用什麼版本
目前Heroku 提供的[版本][Heroku Runtime] 有兩種，我這邊選用 python 3.6
```bash
> echo python-3.6.2 > runtime.txt
```

### Deploy
首先我們來確定一下我們的資料都有放在對的地方
```bash
> tree
.
├── Procfile
├── main
│   ├── app.py
│   └── runtime.txt
└── requirements.txt

```

Heroku 的 deploy 其實很單純，就把 Heroku 當成一個 [git remote][git_remote]
push 上去 heroku/master 就可以了

所以我們需要先建立 git
```bash
# cd to caht-bot app

# initialize git 
> git init

# add all to git stage
> git add .

# commit stage
> git commit -m 'init commit'
```
然後下載 [Heroku toolbelt][heroku_cli] (現在叫做 [Heroku CLI][heroku_cli])

```bash
# I use mac and homebrew is f'cking awsome!
> brew install heroku
```

接下來繼續把我們的 app 送上 heroku
```bash
# creating a Heroku remote
> heroku create

# deploying
> git push heroku master
```


> 其實Heroku 也支援 GitHub 的自動 deploy，push 到 GitHub 的某個branch，Heroku 會自動做 deploy
>
> 我自己是比較愛用 Github 連動的做法，這樣 code 可以同步放上 GitHub
> 開發階段也不用太擔心即時 deploy 會影響到誰ＸＤ

## 瞧瞧你的 app 吧

連上 Heroku ，登入你的 app 的 dashboard
右上角的 Open app  就可以看到你的一行網頁囉～




> __See also:__
> - [Heroku]
> - [Getting Started on Heroku with Python]
> - [Gunicorn]
> - [Heroku Runtime]


[day_1]:http://tomazwang.logdown.com/posts/2075477/chat-bot-come-and-join-me-in-the-chat-robot
[Heroku]:https://www.heroku.com/
[Getting Started on Heroku with Python]:https://devcenter.heroku.com/articles/getting-started-with-python#introduction
[Gunicorn]:http://gunicorn.org/
[Heroku Runtime]:https://devcenter.heroku.com/articles/python-runtimes
[git_remote]:https://git-scm.com/book/zh-tw/v1/Git-%E5%9F%BA%E7%A4%8E-%E8%88%87%E9%81%A0%E7%AB%AF%E5%8D%94%E5%90%8C%E5%B7%A5%E4%BD%9C
[heroku_cli]:https://devcenter.heroku.com/articles/heroku-cli