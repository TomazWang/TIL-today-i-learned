# 不再做兩次工 - Android Data Binding 簡介

findViewById、setText、setVisibility、setOnClickListener... 一個Project中需要寫多少次重複的code只是為了把簡單的畫面lay出來呢?

Databinding把layout的工作還給xml，不要再讓排版的code霸占應該寫邏輯的java.

> See Also:
> - [Data Binding Library][dbl]
> - [realm/Android Data Binding][realm]

## 回到基本的Android

Android 的開發上常會需要像這樣個code，用findViewById拿很多view來，在依照資料一項一項設定

[(before_binding)MainActivity.java][before-java]
```java
TextView title = (TextView)findViewById(R.id.txt_title);
View functionBtns = findViewById(R.id.layout_functions);
Button funcBtnA = (Button)findViewById(R.id.btn_function_a);
Button funcBtnB = (Button)findViewById(R.id.btn_function_b);

title.setText(funcInfo.title);

if(funcInfo.hasFuncs) {
   functionBtns.setVisibility(View.VISIBLE);
}else{
   functionBtns.setVisibility(View.GONE);
}

funcBtnA.setText(funcInfo.funcA);
funcBtnB.setText(funcInfo.funcB);

funcBtnA.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            funcA(view);
        }
});

funcBtnB.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        funcB(view);
    }
});


```

## 能不能更簡單?

寫久了之後會發現findViewById寫很多很麻煩，還要cast成各種不同的View。OnClickListener也是需要每次都寫很長一段。當然也有許多方法可以縮減這些常見的冗長寫法

__Java 8__

用Java 8的lambda可以讓OnClickListener更加簡化

```java
funcBtnA.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            funcA(view);
        }
});

// 可以改成

funcBtnA.setOnClickListener(v -> funcA(v))

```
> __NOTE__: Android 使用Java 8 前要先做一些[環境設定][android-8]
> 更多Java 8 lambda 可以參考[這裡][java-8-lambda]

__ButterKnife__

[Butter Knife][bk]是一個老字號的View Injection工具，他可以讓你省去繁複的findViewById，也可以直接對View加入onClick、onLongClick之類的功能。

上面那幾行 findViewById 可以簡化成
```java
...
    @BindView(R.id.txt_title) TextView title;
    @BindView(R.id.btn_function_a) Button funcBtnA;
    @BindView(R.id.btn_function_b) Button funcBtnB;
...
```

OnClickListener的setting也不需要事先拿到View，直接在method上註明這是OnClick後要調用的方法:

```java
@OnClick
public void onClick() {
  // TODO do something!
}
```


## DataBinding!

如果要放入layout的資料不複雜，但又沒有靜態到可以直接放進resource中，可以使用data Binding。DataBinding可以把UI的相關資料、簡單邏輯都放回到layout檔中，大幅度降低，甚至省去findView和存取View的麻煩。


### First: 環境設置

Android DataBinding 可以向下相容到API 7(Android 2.1)，在使用Databinding前，要在gradle檔中開啟這項功能:

```gradle
android {
    ....
    dataBinding {
        enabled = true
    }
}
```

### 改變你的layout.xml

layout.xml中需要做些改動，首先在最底層ViewGroup外加上`<layout>` tag。在開始佈局layout之前，用`<data>`標籤註明這次layout中需要綁定的資料、用`<variable >`註明要綁定的類別。

[(after_binding)activity_main.xml][after-xml]
```xml
<?xml version="1.0" encoding="utf-8"?>

<layout>
    <data>
        <variable
            name="info"
            type="me.tomazwang.project.databinding.FunctionInfo" />

        <variable
            name="callback"
            type="me.tomazwang.project.databinding.MainActivity"/>

        <import
            name="view"
            type="android.view.View"/>
    </data>


    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/activity_main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="me.tomazwang.project.databinding.MainActivity">

        <TextView
            android:id="@+id/txt_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{info.title}"
            android:textSize="16sp" />

        <LinearLayout
            android:visibility="@{info.hasFuncs ? View.VISIBLE : View.GONE}"
            android:id="@+id/layout_functions"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true"
            android:orientation="horizontal">

            <Button
                android:text="@{info.funcA}"
                android:id="@+id/btn_function_a"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="@{callback::funcA}"/>


            <Button
                android:text="@{info.funcB}"
                android:id="@+id/btn_function_b"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="@{callback::funcB}"/>

        </LinearLayout>
    </RelativeLayout>

</layout>
```

首先，最前面的`<variable>` 導入這個layout所需要的資料，`<import>`導入在簡單邏輯中會用到的class。 原本的setText可以在text attribute中直接作設定，與法為`@{viewModel.value}`。若要配置到layout上的不是純粹的資料值，而是一個method，與法為`@{class::method}`。這邊通常Android Studio會跟你抗議`::`這樣不合XML與法，但是仍然可以build的，這是Android　DataBinding的bug。layout_functions中，我們希望判斷一個boolean值來決定是否顯示，這個簡單的工作可以在這邊以三元運算子的方式達成。

值得一提的是，Android DataBinding支援一些原生java沒有的運算子，例如判斷是否為`null`的`??`，用法上可以取代 `@{info.title == null ? info.title : @string/default_title }`為較簡單的`@{info.title ?? @string/default_title}`


接下來，在原本java code中，只需要把資料丟給DataBinding處理就好了，不需要再去做介面的處理

[(after_binding)MainActivity.java][after-java]
```java
    ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
    binding.setInfo(funcInfo);
    binding.setCallback(this);
```

所有的setText、setOnClickListener等等都被省去，也不用做ButterKnife的binding，也不用令一大堆全域變數來存取每一個會被更動的view。是不是很方便呢? 當然，以上講到的只是DataBinding的初探，還有許多有趣且方便的方法可以解決更多繁雜但又必須的介面處理，讓你的Android Project越乾淨，也越趨近MVVM模式。


完整的code都在[這裡][gist]，


[after-java]:https://gist.github.com/TomazWang/87b9d97e3fd92cdb92dcba6a99d3bd99#file-after_binding-mainactivity-java
[after-xml]:https://gist.github.com/TomazWang/87b9d97e3fd92cdb92dcba6a99d3bd99#file-after_binding-activity_main-xml
[before-java]:https://gist.github.com/TomazWang/87b9d97e3fd92cdb92dcba6a99d3bd99#file-before_binding-mainactivity-java
[gist]:https://gist.github.com/TomazWang/87b9d97e3fd92cdb92dcba6a99d3bd99
[android-8]:https://developer.android.com/guide/platform/j8-jack.html
[realm]:https://realm.io/news/data-binding-android-boyar-mount/
[dbl]:https://developer.android.com/topic/libraries/data-binding/index.html
[java-8-lambda]:https://magiclen.org/java-8-lambda/
[bk]:http://jakewharton.github.io/butterknife/
