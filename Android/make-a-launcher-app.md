# Make a launcher App

You can make a launcher app simply by adding some code. As a launcher, your app can't be escape by home button, recent task button, and it even can't be uninstall.

### The code

In *AndroidManifest.xml*, add these lines in your MainActivity( or any activity you start with).

```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
	<category android:name="android.intent.category.LAUNCHER" />
	<category android:name="android.intent.category.HOME" />
    <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```
