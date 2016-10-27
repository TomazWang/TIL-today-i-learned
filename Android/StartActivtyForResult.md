# Start an Activity for result in a Fragment

> See Also:
> - [Android Developers/ Activity](https://developer.android.com/reference/android/app/Activity.html)
> - [Android Developers/ Fragment](https://developer.android.com/reference/android/app/Fragment.html)

[ThisToThat]:http://i.imgur.com/vZcZCtq.jpg

### Start a Activity for result

When you start an Activity, usually you don't expect any callback from it. However, sometimes you have to get some result from an Activty you just launched. That's when `startActivtyForResult()` comes in. This method launchs an Activty and call `onActivityResult()` after the it finished.

__Using startActivtyForResult__

Let's say we wish to show ThatActivity from ThisActivity and capture user's input from ThatActivity.
![ThisToThat][ThisToThat]

*ThisActivity.java*
```java
  ...
  // create an Intent.
  Intent intent = new Intent();
  intent.setClass(ThisActivity.this, ThatActivity.class);

  // start actviity
  startActivityForResult(intent,REQUEST_CODE);
  ...
```
In the code `REQUEST_CODE` is a constant which will let you know what you've tell the second Activity do.

And here's how you tell the results to ThisActivity in ThatActivity.

*ThatActivity.java*
```java
  ...
  // after  you get result
  Intent resultDataIntent = new Intent();

  Bundle args = new Bundle();
  args.putString(KEY_RESULT_STRING,"some string");

  intent.putExtras(args);

  // THIS LINE IS HOW YOU TELL LAST ACTIVITY YOUR RESULTS ARE
  setResult(RESULT_OK, resultDataIntent);
  ...
```

After ThatActivity is finished and we're back to ThisActivity. `onActivityResult()` is called.

*ThisActivity.java*
```java
  ...

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {

        Log.d(TAG, "onActivityResult: requestCode =" + requestCode + ", resultCode =" + resultCode);

        if (requestCode == REQUEST_CODE) {
            switch (resultCode) {
                case RESULT_OK:
                    Bundle bundle = data.getExtras();

                    if (bundle != null) {
                        String resultString = bundle.getString(ThatActivity.KEY_RESULT_STRING);

                    } else {
                        Log.w(TAG, "onActivityResult: argument bundle == null");
                    }
            }
        }


    }

```

And that's it. We can pass data back to the Activty which launchs current Activty easily. But how about Fragments?

### In Fragments

There's two ways to invoke `startActvitiy()` in a Fragment.

1. Simply call `startActvitiy()`.
2. use `getActivity().startActvitiy()`.

There's a `startActvitiy()` inside Fragment class. Similarly, there's a `startActivityForResult()` method inside Fragment clas. However, that's the tricky part.

__Using startActivtyForResult in Fragment__

If you call `startActivtyForResult()` directly. You have to override `onActivityResult()` inside your Fragment. Otherwise, if you wish to use the same `onActivityResult()` in the Activity. You have to use `getActivity().startActivtyForResult()` instead.


### That's it!

And that's it. A quick tip with this method.

---

This post is also post on my [Logdown Blog](http://tomazwang.logdown.com/posts/1045617/android-startactivityforresult). Please check that out.
