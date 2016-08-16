# PrefremceFragment
2016.08.17


use PrefremceFragment when you only got one page of settings to show and you're currently supporting Andorid 3.0+.

See also:
> https://developer.android.com/guide/topics/ui/settings.html#Fragment

---
1. Create a Fragment extends PrefremceFragment

  ```java
  public static class SettingsFragment extends PreferenceFragment {
      @Override
      public void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);

          // Load the preferences from an XML resource
          addPreferencesFromResource(R.xml.preferences);
      }
      ...
  }

  ```

2. Start this Fragment from any Activity or build a SettingActivty for this.

3. If you want to digging more about Settings, see following links:

  - PreferenceScreen : https://developer.android.com/reference/android/preference/PreferenceScreen.html
  - PreferenceFragment: https://developer.android.com/reference/android/preference/PreferenceFragment.html
