# Vertical ViewPager and Vertical Tabs

I have to achieve a view with ViewPager style swiping effect but in vertical. Also, it has to be synchronized with  tabs also in vertical form. Lucky enough, in my project, it doesn't have to deal with lots of dynamic data. And here's how I do it(with the solution from Stackoverflow).

> See also:
> - [Android Developer/ ViewPager.PageTransformer][PageTransformer]
> - [Stackoverflow/ Android: Vertical ViewPager][stack_vertical_viewpager]

> Check the [Demo Code][demo_code] on github.

[PageTransformer]:https://developer.android.com/reference/android/support/v4/view/ViewPager.PageTransformer.html
[stack_vertical_viewpager]:http://stackoverflow.com/questions/13477820/android-vertical-viewpager
[img_default_view_pager]:http://i.imgur.com/f9wF2Kh.gif
[img_only_transformer]:http://i.imgur.com/Hefd4Oy.gif
[img_vertical_pager]:http://i.imgur.com/o91LG42.gif
[img_done]:http://i.imgur.com/lkgSUxt.gif
[demo_code]:https://github.com/TomazWang/VerticalViewPager
[blog]:http://tomazwang.logdown.com/posts/1062498/android-vertical-viewpager-and-tabs


__Final result__
![Final][img_done]

## First: create a custom ViewPager

Create a custom view and extends ViewPager. Then, use it as a normal ViewPager in my activity.

*VerticalViewPager.java*
```java
public class VerticalViewPager extends ViewPager {

    public VerticalViewPager(Context context) {
        super(context);
        init();
    }

    public VerticalViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    private void init() {
        // for futher
    }

}
```
__Add custom view in layout__

After created a custom VerticalViewPager, use it in the layout file.

*activity_main.xml*
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="me.tomazwang.project.verticalviewpager.MainActivity">


    <me.tomazwang.project.verticalviewpager.view.VerticalViewPager
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/vertical_viewPager"/>


</RelativeLayout>
```

__Create a simple ViewPagerAdpater__

Then, create a simple ViewPagerAdapter for your data.( I use FragmentPagerAdapter in case I have to do complex layout handling in the future).

*PageAdapter*
```java
public class PageAdapter extends FragmentPagerAdapter {

    String[] data;

    public PageAdapter(FragmentManager fm, String[] data) {
        super(fm);
        this.data = data;
    }

    @Override
    public Fragment getItem(int position) {
        return PagerFragmet.newInstance(data[position], position);
    }

    @Override
    public int getCount() {
        return data.length;
    }
}
```

__Put them together__

*MainActivity.java*
```java
public class MainActivity extends AppCompatActivity{

    private String[] data;
    ViewPager viewPager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);

        viewPager = (ViewPager) findViewById(R.id.vertical_viewPager);

        initData();
        initView();
    }

    private void initData() {
        this.data = new String[]{
                "Page 1",
                "Page 2",
                "Page 3",
                "Page 4",
                "Page 5",
                "Page 6"
        };
    }


    private void initView() {
        PageAdapter viewPageAdapter = new PageAdapter(getSupportFragmentManager(), data);
        viewPager.setAdapter(viewPageAdapter);
    }

}
```

![Basic ViewPager][img_default_view_pager]

Now, we got a horizontal ViewPager just like what a ViewPager should be. Let's turn it into vertical!

## Make VerticalViewPager scrolls in vertical

The simplest way to make a ViewPager scrolls the way you wish is to implement your own PageTransformer. By doing so, you can overwrite the default behavior of ViewPager.

__Set a PageTransformer__

In our `init()` method in VerticalViewPager, give this ViewPager a custom PageTransformer. Also, we disable the over-scroll shadow here.

```java
...

private void init() {

        // use PageTransformer to implement vertical viewpager
        setPageTransformer(true, new VerticalViewPager.PageTransformer());

        // disable over scroll shadow
        setOverScrollMode(OVER_SCROLL_NEVER);
    }
...
```

Implement PageTransformer as an inner-class of VerticalViewPager. This PageTransformer disable the horizontal scrolling of ViewPager and add Vertical scrolls to it. According to the "position" parameter.

```java
public class PageTransformer implements ViewPager.PageTransformer {

       @Override
       public void transformPage(View page, float position) {

           if (position < -1) {
               // [-infinity, -1], view page is off-screen to the left

               // hide the page.
               page.setVisibility(View.INVISIBLE);

           } else if (position <= 1) {
               // [-1, 1], page is on screen

               // show the page
               page.setVisibility(View.VISIBLE);

               // get page back to the center of screen since it will get swipe horizontally by default.
               page.setTranslationX(page.getWidth() * -position);

               // set Y position to swipe in vertical direction.
               float y = position * page.getHeight();
               page.setTranslationY(y);

           } else {
               // [1, +infinity], page is off-screen to the right

               // hide the page.
               page.setVisibility(View.INVISIBLE);
           }
       }

   }
```
![Only with transformer][img_only_transformer]

However, the "position" parameter in method `transformPage(View page, float position)` is the x translation. Therefore, we have a ViewPager scrolls in vertical( which is what we want) but only when you swipe it horizontal.


## Handling touch event

Here's how we deal with this problem. We capture the touch event before we switch the x and y. Trick the ViewPager to believe that our vertical swipe is actually horizontal swipe, which makes the view scrolls vertically since it uses our custom PageTransformer.

*VerticalViewPager.java*
```java
...
    // swap x and y
    private MotionEvent swapXY(MotionEvent ev) {
        float width = getWidth();
        float height = getHeight();

        float newX = (ev.getY() / height) * width;
        float newY = (ev.getX() / width) * height;

        ev.setLocation(newX, newY);

        return ev;
    }
...
```

To capture touch event, we override `onTouchEvent()` and `onInterceptTouchEvent()` in VerticalViewPager.
```java
...
@Override
  public boolean onInterceptTouchEvent(MotionEvent ev) {

      boolean interceped = super.onInterceptTouchEvent(swapXY(ev));
      swapXY(ev); // swap x,y back for other touch events.
      return interceped;
  }


  @Override
  public boolean onTouchEvent(MotionEvent ev) {
      return super.onTouchEvent(swapXY(ev));
  }


  // swap x and y
  private MotionEvent swapXY(MotionEvent ev) {
      float width = getWidth();
      float height = getHeight();

      float newX = (ev.getY() / height) * width;
      float newY = (ev.getX() / width) * height;

      ev.setLocation(newX, newY);

      return ev;
  }
...
```

And we got a ViewPager slides in vertical.

![Vertical ViewPager][img_vertical_pager]

## How about the vertical tabs?
About the tabs, I use ListView( which is not the best solution, but it works).

__Add a ListView in layout__

*activity_main.xml*
```xml
...
    <ListView
        android:id="@+id/lv_tabs"
        android:layout_width="100dp"
        android:layout_height="match_parent"/>

    <me.tomazwang.project.verticalviewpager.view.VerticalViewPager
        android:layout_toRightOf="@id/lv_tabs"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/vertical_viewPager"/>
...
```

__Make the ListView looks like tabs__

To make the ListView act like tabs, I implement the adapter with select functions. And use liseners to synchronize it and ViewPager.

```java
public class TabAdapter extends BaseAdapter implements AdapterView.OnItemClickListener{
    private final String[] data;
    private final ListView listView;
    private OnItemClickListener listener;

    private int currentSelected = 0;

    public TabAdapter(String[] data, ListView listView, OnItemClickListener listener){
        this.data = data;
        this.listView = listView;
        this.listener = listener;

        listView.setOnItemClickListener(this);
    }

    ...
    // Override other Adpter method here.
    ...


    @Override
    public View getView(int i, View view, ViewGroup viewGroup) {
        // Currently not using viewHolder pattern cause there aren't too many tabs in the demo project.

        if(view == null){
            view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.item_tab, viewGroup, false);
        }

        TextView tabTitle = (TextView)view.findViewById(R.id.txt_tab_title);
        tabTitle.setText((String)getItem(i));

        if(i == currentSelected){
            // change the appearance
        }else{
            // change the appearance
        }

        return view;
    }


    /**
     * Return item view at the given position or null if position is not visible.
     */
    public View getViewByPosition(int pos) {
        if(listView == null){
            return  null;
        }
        final int firstListItemPosition = listView.getFirstVisiblePosition();
        final int lastListItemPosition = firstListItemPosition + listView.getChildCount() - 1;

        if (pos < firstListItemPosition || pos > lastListItemPosition ) {
            return null;
        } else {
            final int childIndex = pos - firstListItemPosition;
            return listView.getChildAt(childIndex);
        }
    }


    private void select(int position){
        if(currentSelected >= 0){
            deselect(currentSelected);
        }

        View targetView = getViewByPosition(position);
        if(targetView != null) {
            // change the appearance
        }

        if(listener != null){
            listener.selectItem(position);
        }

        currentSelected = position;

    }

    private void deselect(int position) {
        if(getViewByPosition(position) != null){
            View targetView = getViewByPosition(position);
            if(targetView != null) {
                // change the appearance
            }
        }

        currentSelected = -1;
    }


    // OnClick Events

    @Override
    public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
        select(i);
    }

    public void OnItemClickListener(TabAdapter.OnItemClickListener listener){
        this.listener = listener;
    }

    public void setCurrentSelected(int i) {
        select(i);
    }

    public interface OnItemClickListener{
        void selectItem(int position);
    }

}
```

__Syncronize VerticalViewPager and TabAdapter__

Use `OnItemClickListener` from TabAdapter and `OnPageChangeListener` from ViewPager, we can notify one when the other is changing.

```java
// Implements TabAdapter.OnItemClickListener
@Override
public void selectItem(int position) {
   viewPager.setCurrentItem(position, true);
}
```


```java
// Implements ViewPager.OnPageChangeListener
@Override
public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {}

@Override
public void onPageScrollStateChanged(int state) {}

@Override
public void onPageSelected(int position) {
   if(tabAdapter != null){
       tabAdapter.setCurrentSelected(position);
   }
}
```

And we hava a ViewPager with tabs in vertical.

![Final][img_done]

---
This article is also post on my [Logdown Blog][blog].
