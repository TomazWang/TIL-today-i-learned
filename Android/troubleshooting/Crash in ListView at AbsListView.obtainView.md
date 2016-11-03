# Problem

```
java.lang.NullPointerException
    at android.widget.AbsListView.obtainView(AbsListView.java:2277)
    at android.widget.ListView.measureHeightOfChildren(ListView.java:1263)
    at android.widget.ListView.onMeasure(ListView.java:1175)
    at android.view.View.measure(View.java:16505)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    at android.widget.LinearLayout.measureChildBeforeLayout(LinearLayout.java:1404)
    at android.widget.LinearLayout.measureVertical(LinearLayout.java:695)
    at android.widget.LinearLayout.onMeasure(LinearLayout.java:588)
    at android.view.View.measure(View.java:16505)
```

# Solution

It happens when Adapter.getView() return null.
