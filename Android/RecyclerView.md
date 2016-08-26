# RecyclerView

A better way to display list (or grid and cards).

See also:

- [RecyclerView Documnet](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
- [Creating Lists and Cards](https://developer.android.com/training/material/lists-cards.html)
- [Example Project](https://developer.android.com/samples/RecyclerView/project.html)

---
## The Basic

Before we get our hands dirty and build something, we have to learn what is RecyclerView and why we use it.

### Why RecyclerView?
The RecyclerView is more flexible and more adavanced version of ListView.

> Use the RecyclerView widget when you have data collections whose elements change at runtime based on user action or network events. - [Creating Lists and Cards](https://developer.android.com/training/material/lists-cards.html)


### LayoutManager
RecyclerView provides built-in layout manager:
- `LinearLayoutManager` shows items in a vertical or horizontal scrolling list.
- `GridLayoutManager` shows items in a grid.
- `StaggeredGridLayoutManager` shows items in a staggered grid.

`LayoutManager` put into RecyclerView and determines when to reuse these views.

> See Also: [RecyclerView.LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html)


### Animations
RecyclerView also has built-in animations for adding and removing items, which is very useful when you want to build a better UX app.

To customize the animation, extends the [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html) class and use the ```RecyclerView.setItemAnimator()``` method.

## Example

1. First we place a RecyclerView widget in the layout.
  ```xml
  ...
  <android.support.v7.widget.RecyclerView
      android:id="@+id/my_recycler_view"
      android:layout_width="match_parent"
      android:layout_height="match_parent" />
  ...
  ```

2. Create a item layout.
  ```xml
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      android:orientation="vertical" android:layout_width="match_parent"
      android:layout_height="match_parent">  

      <TextView
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:textSize="16sp"
          android:id="@+id/txt_item"
          android:gravity="center"/>

  </LinearLayout>
  ```

3. Handle RecyclerView just like ListView. Don't forget to set LayoutManager.

  ```java
  public class MyActivity extends Activity {
    private RecyclerView mRecyclerView;
    private MyAdapter mAdapter;
    private RecyclerView.LayoutManager mLayoutManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.my_activity);
        mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);

        // use a linear layout manager
        mLayoutManager = new LinearLayoutManager(this);
        mRecyclerView.setLayoutManager(mLayoutManager);

        // specify an adapter (see also next example)
        mAdapter = new MyAdapter(myDataset);
        mRecyclerView.setAdapter(mAdapter);
    }
    ...
  }
  ```

4. If you want to put a RecyclerView in Fragment, try this.

  ```java
  public class MainFragment extends Fragment {
      protected RecyclerView mRecyclerView;
      protected RecyclerView.LayoutManager mLayoutManager;
      protected MyAdapter mAdapter;
      private ArrayList<String> mDataSet = new ArrayList<>();

      @Override
      public void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          initData();
      }

      @Override
      public View onCreateView(LayoutInflater inflater, ViewGroup container,
                                    Bundle savedInstanceState) {

          ...
          View rootView = inflater.inflate(R.layout.fragment_main, container, false);
          mRecyclerView = (RecyclerView) rootView.findViewById(R.id.my_recycler_view);

          // init LayoutManager
          setRecyclerViewLayoutManager(mCurrentLayoutManagerType);

          mAdapter = new MyAdapter(mDataSet);
          mRecyclerView.setAdapter(mAdapter);

            return rootView;
        }

        private void setRecyclerViewLayoutManager(@LayoutManagerType int type) {
            int scrollPosition = 0;

            // if layout manager has already been set, get current scroll position.
            if(mRecyclerView.getLayoutManager() != null){

                // GridLayoutManager extends LinearLayoutManager.
                scrollPosition = ((LinearLayoutManager)mRecyclerView.getLayoutManager())
                        .findLastVisibleItemPosition();
            }

            mLayoutManager = new LinearLayoutManager(getActivity());
            mCurrentLayoutManagerType = LINER_LAYOUT;

            mRecyclerView.setLayoutManager(mLayoutManager);
            mRecyclerView.scrollToPosition(scrollPosition);

        }

        private void initData() {

            // TODO: replace these fake data here!
            for(int i = 0; i<= 80 ;i++){
                mDataSet.add("Item "+i);
            }
        }

    }
  ```  

5. For the Adapter, you must extends RecyclerView.Adapter, which let you to implements a ViewHolder inside Adpater.

  ```java
  public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder>{
      private ArrayList<String> mDataSet = new ArrayList<>();
      public class ViewHolder extends  RecyclerView.ViewHolder{

          private final TextView txt_item;

          public ViewHolder(View itemView) {
              super(itemView);

              txt_item = (TextView) itemView.findViewById(R.id.txt_item);

          }

          public TextView getTxt_name() {
              return txt_name;
          }
      }
      @Override
      public void onBindViewHolder(ViewHolder holder, int position) {

          holder.getTxt_name().setText(mDataSet.get(position));
      }

      @Override
      public int getItemCount() {
          return mDataSet.size();
      }

      public MyAdapter(ArrayList<String> mDataSet) {
          this.mDataSet = mDataSet;
      }
  }
  ```

### Switch between different layouts

```java
...
public static final int LINER_LAYOUT = 0;
public static final int GRID_LAYOUT = 1;
private static final String KEY_LAYOOUT_MABAGER_TYPE = "layoutManagerType";

@IntDef({LINER_LAYOUT, GRID_LAYOUT})
public @interface LayoutManagerType {}

@LayoutManagerType
protected int mCurrentLayoutManagerType = LINER_LAYOUT;

...

@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
   ...
  // after find RecyclerView in rootView.
  // get previous layout type
   if (savedInstanceState != null) {

       switch (savedInstanceState.getInt(KEY_LAYOOUT_MABAGER_TYPE)) {

           case GRID_LAYOUT:
               mCurrentLayoutManagerType = GRID_LAYOUT;
               break;

           case LINER_LAYOUT:
           default:
               mCurrentLayoutManagerType = LINER_LAYOUT;
               break;
       }
   }

   // init LayoutManager
   setRecyclerViewLayoutManager(mCurrentLayoutManagerType);

...
}


private void setRecyclerViewLayoutManager(@LayoutManagerType int type) {
        int scrollPosition = 0;

        // if layout manager has already been set, get current scroll position.
        if(mRecyclerView.getLayoutManager() != null){

            // GridLayoutManager extends LinearLayoutManager.
            scrollPosition = ((LinearLayoutManager)mRecyclerView.getLayoutManager()).findLastVisibleItemPosition();
        }


        switch (type){
            case GRID_LAYOUT:
                mLayoutManager = new GridLayoutManager(getActivity(),mSpanCount);
                mCurrentLayoutManagerType = GRID_LAYOUT;
                break;

            case LINER_LAYOUT:
            default:
                mLayoutManager = new LinearLayoutManager(getActivity());
                mCurrentLayoutManagerType = LINER_LAYOUT;
                break;
        }

        mRecyclerView.setLayoutManager(mLayoutManager);
        mRecyclerView.scrollToPosition(scrollPosition);

    }
    ...
```
