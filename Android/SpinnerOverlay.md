# All About Spinner On The Toolbar of Android

[spinnerOverlay]:http://i.imgur.com/tQlmrS9.png
[spinnerNotOverlay]:http://i.imgur.com/8exDbF5.png

I want to add a spinner on Toolbar in my App the other day. But I ran into a problem that the popup list of spinner will cover the button.

*Popup menu overlay*
!["Spinner overlay"][spinnerOverlay]

After some google time, I found that this behavior of spinner is changed after Android 5.0. And there's a second way to add a spinner on Toolbar, which can also work with other view. And it can also solve my problem which can make a spinner like this.
!["Spinner not overlay"][spinnerNotOverlay]

So, today I learned **There's two way to add spinner in Toolbar**

> See Also:
>

### Use `menu.xml` to add a spinner(or any other view) in Toolbar.

Fist of all, let's create our layout.

*layout.xml*
```xml
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?attr/colorPrimary"
        android:minHeight="?actionBarSize" />
```

Than, create an xml file in res/menu/ folder and add menu items. Use the __actionViewClass__ attribute to add a view other than icon or String.

*drawer_menu.xml*
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/miSpinner"
        android:title="This"
        app:actionViewClass="android.widget.Spinner"
        app:showAsAction="always" />

    <!-- Other items -->

</menu>
```

In the java activity file, overrid `onCreateOptionMenu()` method. Use `getMenuInflater()` to inflate menu with the xml file. Than get Spinner Object and set an adpter to it.

```java
@Override
    public boolean onCreateOptionsMenu(Menu menu) {

        getMenuInflater().inflate(R.menu.menu_drawer_activity, menu);

        // get Spinner

        MenuItem spinnerMenuItem = menu.findItem(R.id.miSpinner);
        final Spinner spinner = (Spinner) MenuItemCompat.getActionView(spinnerMenuItem);

        // set Spinner Adapter

        final ArrayAdapter<CharSequence> spinnerAdapter = ArrayAdapter.createFromResource(this, R.array.drawer_drop_down, android.R.layout.simple_spinner_dropdown_item);
        spinnerAdapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        spinner.setAdapter(spinnerAdapter);

        // do other things

        return true;

    }
```

### Manually add Spinner in Toolbar

The other way to add views in Toolbar is simply add the view in layout file.
Notice that I add a line to avoid the popup menu overlay issue: `android:overlapAnchor="false"`.

*layout.xml*
```xml
<android.support.v7.widget.Toolbar
         android:id="@+id/toolbar"
         android:layout_width="match_parent"
         android:layout_height="wrap_content"
         android:background="?attr/colorPrimary"
         android:minHeight="?actionBarSize">

         <Spinner
             android:id="@+id/spinner_toolBar"
             android:layout_width="wrap_content"
             android:overlapAnchor="false"
             android:layout_height="wrap_content">

         </Spinner>

     </android.support.v7.widget.Toolbar>
     ```


With this layout, you cant simply use `findViewById()` to get spinner Object and setAdapter as a regular spinner view.
