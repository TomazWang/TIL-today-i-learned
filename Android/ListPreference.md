# ListPreference
2016.08.17

Use ListPreference to create a single-select setting.

![ListPreference Sample](/Android/img/list_pref.PNG)

### Steps

1. Add some strings you might use in **values/strings.xml**

  in strings.xml, remeber to declare string-array for items you want to show in list.

  ```xml
  <!-- pref_unit -->
    <string name="pref_unit_label">Unit</string>
    <string name="pref_unit_key" translatable="false">unit</string>
    <string name="pref_unit_metric_label">Celsius</string>
    <string name="pref_unit_imperial_label">Fahrenheit</string>

    <string name="pref_unit_metric_value" translatable="false">metric</string>
    <string name="pref_unit_imperial_value" translatable="false">imperial</string>

    <!-- NOTICE HERE -->
    <string-array name="pref_unit_type">
        <item>@string/pref_unit_metric_label</item>
        <item>@string/pref_unit_imperial_label</item>
    </string-array>

    <string-array name="pref_unit_type_values">
        <item>@string/pref_unit_metric_value</item>
        <item>@string/pref_unit_imperial_value</item>
    </string-array>
    ```
2. In xml files for PrefernceScreen, use **ListPreference** element.

  ```xml
  <PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
          android:layout_width="match_parent"
          android:layout_height="match_parent">

          <ListPreference
              android:title="@string/pref_unit_label"
              android:key="@string/pref_unit_key"
              android:defaultValue="@string/pref_unit_metric_value"
              android:entries="@array/pref_unit_type"
              android:entryValues="@array/pref_unit_type_values">
          </ListPreference>

    </PreferenceScreen>
  ```
