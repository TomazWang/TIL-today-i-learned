# Android Studio New Features

## Hints
- Parameter Hints: 傳參數給 method 時，提示該欄位意義
- Semantic highlighting: 每個區域變數都會有不同顏色（對，我的Editor 更花了）

## UI
- Fonts: 配合 Android O 和新的 support library, Android Studio Layout Editor 可以加入字型
- 新的 Forground/Background/Mask Icon 設計工具


## Lang
- Kotlin 支援
- Java 8 支援 （支援所有 android API level)

## Tools 
- Android Profiler: 
	- 監控連接的裝置 CPU、 網路、記憶體的狀態
	- 標註特殊操作（輸入、返回、旋轉螢幕...）
	- Detail View 可以看到更細的狀態（每個 Theard, method stack, GC 時間）
	- Network Tool 可以直接看到 request 和 response (gradle 2.4 or higher, 支援 volley, okHttp)

- 就算不是build 出來的 apk 也可以使用 Android Studio 3.0 Debug Tool 來 debug

