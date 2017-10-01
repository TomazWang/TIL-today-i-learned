# SOLID Principle in Android

> References: 
> - [Android Development: the SOLID Principles][ref_1]
> - [Learning Path: SOLID Principles for Android][ref_2]
> - [SOLID (object-oriented design) wiki page][ref_3]
> - [物件導向程式設計基本原則 - SOLID][ref_4]

[ref_1]:https://android.jlelse.eu/android-development-the-solid-principles-3b5779b105d2
[ref_2]:https://academy.realm.io/posts/learning-path-solid-principles-for-android/
[ref_3]:https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)
[ref_4]:https://skyyen999.gitbooks.io/-study-design-pattern-in-java/content/oodPrinciple.html

SOLID 是五個物件導向的五個大原則，可以協助設計師建立好維護、易擴充的程式
- __*S*__ingle Responsibility Principle (SRP) 單一職責
- __*O*__pen-Closed Principle (OCP) 開放/封閉原則
- __*L*__iskov Substitution Principle (LSP)  Liskov替換
- __*I*__nterface Segregation Principle (ISP) 介面隔離
- __*D*__ependency Inversion Principle (DIP) 依賴反轉

---

## Single Responsibility Principle (SRP) 單一職責
> A class should have one, and only one, reason to change.
> 
> 一個 Class 只做一件事。每個 Class 都該有一個（也只該有一個）理由修改

以 RecyclerView 的 Adapter 為例，下面是一個普通的 Adpater 範例

```java
// 違反 SRP 的作法
public class MovieRecyclerAdapter extends RecyclerView.Adapter<MovieRecyclerAdapter.ViewHolder> {
 
    private List<Movie> movies;
    private int itemLayout;
 
    public MovieRecyclerAdapter(List<Movies> movies, int itemLayout) {
        this.movies = movies;
        this.itemLayout = itemLayout;
    }
 
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext())
                .inflate(itemLayout, parent, false);
        return new ViewHolder(v);
    }
 
 
    // ---- >>> 請注意這裡
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        final Movie movie = movies.get(position);
 
        holder.itemView.setTag(movie);
        holder.title.setText(movie.getTitle());
        holder.rating.setText(movie.getRating());
 
        // 顯示電影的類型
        String genreStr = "";
        for (String str : movie.getGenre()) {
            genreStr += str + ", ";
        }
        genreStr = genreStr.length() > 0 ?
                genreStr.substring(0, genreStr.length() - 2) : genreStr;
        holder.genre.setText(genreStr);
 
        holder.releaseYear.setText(movie.getYear());
        Glide.with(holder.thumbNail.getContext())
                .load(movies.get(position)
                        .getThumbnailUrl())
                .into(holder.thumbNail);
    }
    // <<<< ----
 
    @Override
    public int getItemCount() {
        return movies.size();
    }
 
    public static class ViewHolder extends RecyclerView.ViewHolder {
        @Bind(R.id.title)
        TextView title;
        @Bind(R.id.rating)
        TextView rating;
        @Bind(R.id.genre)
        TextView genre;
        @Bind(R.id.releaseYear)
        TextView releaseYear;
        @Bind(R.id.thumbnail)
        ImageView thumbNail;
 
        public ViewHolder(View itemView) {
            super(itemView);
            ButterKnife.bind(this, itemView);
        }
    }
}
```

要理解為什麼這個 Adapter 違反了 SRP，要先理解一個 Adapter 應該做什麼？

一個 RecyclerView 的 Adapter 應該負責“將資料物件，以清單的方式擺在畫面上”

而這支 Adpater，注意 `onBindView` 的實作，同時“將資料擺在畫面上”，又做了“組合要顯示的字串”的動作

這樣這支 Adapter 就同時擔負了“處理資料”以及“組合畫面”兩個責任，這是 SRP 所不樂見的


#### 在Adapter 中組合資料，那又怎樣？

這個資料字串的組合將會與這個Adpater 綁再一起

未來要在別的頁面（例如電影的資訊內頁）顯示電影類型，又得在組一次

未來如果組合的方式需要修改，就必須得修改多個地方，少改一個就會有不一致的狀況

如此 `onBindViewHolder` 做的不只是把資料和 ViewHolder binding ，而多做了其他的邏輯


#### 那該怎麼修改呢？
```java
// 修正後，符合 SRP 的 onBindViewHolder
 
public void onBindViewHolder(ViewHolder holder, int position) {
        final Movie movie = movies.get(position);
        holder.itemView.setTag(movie);
        holder.title.setText(movie.getTitle());
        holder.rating.setText(movie.getRating());
        holder.genre.setText(
                ArraysUtil.convertArrayListToString(movie.getGenre()));
        holder.releaseYear.setText(movie.getYear());
        Glide.with(holder.thumbNail.getContext())
                .load(movies.get(position)
                        .getThumbnailUrl())
                .into(holder.thumbNail);
    }    
```

將“組合資料”這個動作轉交給其他人，例如一個 Util<這樣這段組合的 Code 將可以在其他地方重複利用

同樣的，也可以進一步將  `onBindViewHolder` 中的 code 寫進 `ViewHolder` 本身

```java
public void onBindViewHolder(ViewHolder holder, int position) {
        Movie movie = movies.get(position);
        holder.bind(movie);
        // 其他的都在 ViewHolder 中進行
    }
```

如此，Adapter Class  的責任就只剩“安排資料在畫面上的順序“

將”每個資料在畫面上的顯示“ 這個責任交給 ViewHolder 進行

---


## Open-Closed Principle (OCP) 開放/封閉原則

> Software entities (classes, modules, functions, etc…) should be open for extension, but closed for modification.
>
> 軟體應該對擴充開放、對修改封閉

講白話一點，OCP 原則是在有新的需求時，以不更動原本程式的前提下，進行擴充。來看看以下範例：

```java
// Rectangle.java
public class Rectangle {
    private double length;
    private double height;
    // getters/setters ...
}
```

```java
// AreaManager.java

// 違反 OCP 的作法
 
public class AreaManager {
    // 加總所有圖形的面積
    public double calculateArea(ArrayList<Rectangle>... shapes) {
        double area = 0;
        for (Rectangle rect : shapes) {
            area += (rect.getLength() * rect.getHeight());
        }
        return area;
    }
}
```

AreaManager 可以順利的計算 Rectangle 的面積，直到有一個新的圖形出現...

```java
// Circle.java
public class Circle {
    private double radius;
    // getters/setters ...
}
```

為了計算 Circle 的面積，我們必須把 AreaManager 改成這樣

```java
// AreaManager.java

// 違反 OCP 的後果...
public class AreaManager {
    // 加總所有圖形的面積
    public double calculateArea(ArrayList<Object>... shapes) {
        double area = 0;
        for (Object shape : shapes) {
            if (shape instanceof Rectangle) {
                Rectangle rect = (Rectangle)shape;
                area += (rect.getLength() * rect.getHeight());               
            } else if (shape instanceof Circle) {
                Circle circle = (Circle)shape;
                area += (circle.getRadius() * cirlce.getRadius() * Math.PI;
            } else {
                throw new RuntimeException("Shape not supported");
            }           
        }
        return area;
    }
}
```

很明顯這就不是一個長久的作法，未來若有更多的形狀加入，AreaManager 就必須得一直做更新，增加原先使用了 `calculateArea()` 這個 method 的其他元件產生錯誤的風險

比較適合的作法是使用抽象層，透過繼承關係來整合 `Rectangle` , `Circle` 這些類似的 Class。

```java
// Shape.java
public interface Shape {
    double getArea();
}
 
 
// Rectangle.java
public class Rectangle implements Shape {
    private double length;
    private double height;
    // getters/setters ...
 
    @Override
    public double getArea() {
        return (length * height);
    }
}
 
// Circle.java
public class Circle implements Shape {
    private double radius;
    // getters/setters ...
 
    @Override
    public double getArea() {
        return (radius * radius * Math.PI);
    }
}
```

```java
// AreaManager.java

public class AreaManager {
    public double calculateArea(ArrayList<Shape> shapes) {
        double area = 0;
        for (Shape shape : shapes) {
            area += shape.getArea();
        }
        return area;
    }
}
```

如此，若有需要新增形狀，只需要繼承 `Shape`，不需要去修改既有的 `AreaManager`

---

## Liskov Substitution Principle (LSP)  Liskov替換

> Child classes should never break the parent class’ type definitions.
> 
> 子類別應該能替換掉父類別，卻不影響程式運行


Liskov替換原則非常直覺，就是子類別在複寫任何元件時，對外（Class 的使用者）應該要能達到預期效果

依照慣例，先來看個錯誤示範：

```java
// 不符合 LSP 的作法
 
// Car.java
public interface Car {
    public void startEngine();
}
 
// Ferrari.java
public Ferrari implements Car{
    @Override
    public double startEngine(){
        //logic ...
    }
}
 
// Tesla.java
public Tesla implements Car{
    @Override
    public double startEngine(){
        if(!IsCharged){
            return;
        }
        //logic ...
    }
}
 
 
// 發動汽車
public void letStartEngine(Car car){
    car.startEngine();
}
```

`letStartEngine` 在使用 `Car` 這個 interface 的時候，預期 `startEngine` 這個 method 可以發動引擎
殊不知特斯拉就是比較潮，在電池沒啟動的情況不能發動引擎
如此，`letStartEngine` 就不能每次都成功發動車子的引擎。

這樣的設計就像如果有一個 `TextView` 的子類別，在 `setText()` 的時候字沒有顯示出來。
爬了文件才發現他需要 "`setText()`" 然後再 "`display()`"
遇到這種元件，一定會 debug 到火大。因為他不符合父類別給我們的預期。

回到車子，其實解決方法很簡單，就是讓 `startEngine()` 照預期運作

```java
// 修改後符合 LSP 的作法
 
// Tesla.java
public Tesla implements Car{
    @Override
    public double startEngine(){
        if (!IsCharged){
            TurnOnCar();
        }
        //logic ...
    }
}
```

這樣 letStartEngine 在呼叫 car.startEngine() 時才能得到預期的效果

---

## Interface Segregation Principle (ISP) 介面隔離
> No client should be forced to depend on methods it does not use.
> 
> 使用介面的元件，不應該被迫依賴他不需要的 method

用 Android 來說明 ISP 最好的例子就是 `OnClickListener`

點選畫面元件時，需要建立一個`OnClickListener`的實作、長按時又要實作一個 `OnLongClickListener`，若要處理按下、放開的動作，又要時做`OnTouchListener`。

明明都是類似的功能，為什麼不能寫成這樣呢？

```java
public interface OnClickListener {
    void onClick(View v);
    void onLongClick(View v);
    void onTouch(View v, MotionEvent event);
}
```

若寫成這樣，這其實會造成只需要`onClick`的元件必須得實作`onLongClick`和`onTouch`
這樣的空實作不僅是一種浪費，同時也增加了實作者未來需要更新的機率。

假設今天`onTouch`修改成會多傳入一個值，或是需要回傳值，所有實作`OnClickListener`的地方都被迫修改，即便他們只是空實作。

因此，Android  才有各種不同單一功能 的元件需要個別實作。

---

## Dependency Inversion Principle (DIP) 依賴反轉
>1. High-level modules should not depend on low-level modules. Both should depend on abstractions.
> 2. Abstractions should not depend upon details. Details should depend upon abstractions.
> 
> 1. 高階元件不應該依賴低階元件，應該藉由抽象層溝通
> 2. 抽象層不該依賴實作層，實作層“應該”依賴抽象層

DIP 大概 SOLID 中最抽象的一個原則，但抽象也是他的重點。簡單來說就是要寫"抽象的程式"
什麼叫做抽象呢？
想像你要寫一個“拿杯子喝水的程式”，如果寫成 伸手拿 杯子，靠近嘴巴，把 水 倒進嘴巴 
這樣就是話說得太死，不夠抽象的例子。如果未來要喝飲料呢？如果未來要拿瓶子呢？
想像現在程式改成 伸手拿 那個容器，靠近嘴巴，把 那種液體 倒進嘴巴 
這樣未來要拿什麼裝，要喝什麼都可以。因為現在這行程式依賴的是抽象層，而不是實作層

OK 來看程式範例：