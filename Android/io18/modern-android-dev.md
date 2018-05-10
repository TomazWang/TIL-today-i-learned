# Android in #IO18 - Modern Android development 
> 現代 Android 開發方法

Android 今年 10 歲了，開發社群在這 10 年來也發展出了各種寫法
就連 Google 自己也很常對同一個議題提出了很多次不同的作法

今天 Google 的人站出來，一次解決所有問題
一些常見、有分歧的 Android 開發問題，Google 統一給予他們認為最 "現代" 的做法

https://www.youtube.com/watch?v=IrMw7MEgADk

## 簡單統整

### 工具

- 用 Android Studio (廢話)
- 用 Profiler 觀察 CPU, memory, network

### ART （Android Runtime)

- 可以，而且歡迎使用 enum （以前都說要用 @IntDef)
- 歡迎使用 Integer, Boolean 這些基本型態的物件型態
- 但是還是要注意寫法，雖然 runtime 進步了，仍然要注意記憶體和 CPU 使用

### 歡迎嘗試 Kotlin

- 因為可以互通，寫 Java 的時候可以考量到 Kotlin 方呼叫時的方便性，反之亦然
- 別忘了看 Kotlin style guild. 

### Layout 

- AbsoluteLayout .... 別用了！！
- LinearLayout .....  可以用，但是簡單的狀況，不要有巢狀結構
- FrameLayout .... 簡單情況可以用
- GridLayout .... 除非你有 GUI 工具，不然不要用（可是Google 忘記開發那個工具了）
- RelativeLayout .... 用 ConstraintLayout 取代
- ConstraintLayout ....  就是你了！

### Adaptive View (列表、表格、畫面會重複的)

- ListView, GridView, Gallery ... 都別用了
- 改用 RecyclerView .... 要直、要橫、要格子狀都能做到

### Fragment / Activity

- 統一都用 support.v4 的 Fragment
- 未來會有更好的導覽工具(Navigation, 隸屬 Android JetPack)，會密切與 Fragment 互動
- 盡量使用 single Activity 的設計。

### 結構

- 歡迎參考 Android Architecture Components
- 歡迎使用 Repository 模式

### 圖像處理

- 別用 9-patch 了，改用 VectorDrawable
- 歡迎使用 Glide, Picasso, Lottie 來處理圖片

### 其他

> 因為這幾項都是要配合 Android Architecture Components 才能達成，所以列在其他

- Activity, Fragment 的生命週期，改用 LifecycleOwner 處理
  - 使用 support.v4.Fragment / AppCompactActivty
- Sqlite 歡迎改用 Room
- View 獨立於流程，透過 LiveData 通知資料
- Paging, 可以取代 CursorLoader

## Final Thought

大部分的技術都持續有在關注，尤其是 Android Architecture Components。Android Arch 除了奠定了官方期望的結構外，同時也提供了很多工具，讓資料處理、流程處理脫離 View。以往 Android 的 Unit Test 往往會受限 Android framework. 透過這些工具的協助，可以將元件拆散，更方便做測試。

在畫面上，以前每個畫面可能都需要有不同的作法，如今幾乎全部都能靠 ConstraintLayout + RecyclerView 完成。而一直被形容為 Android framework 中的異型的 Fragment 又再度被拉回來，而 core API 中的 Fragment 也算是正式被 deprecate 了。

流程處理上，雖然有 Android Runtime 的協助，但手機的記憶體資源、CPU 資源以及電池都是開發者應該負責好好管理的。思考如何更好的管理記憶體、運算時間，這是以前到現在，唯一一個不變的準則。

