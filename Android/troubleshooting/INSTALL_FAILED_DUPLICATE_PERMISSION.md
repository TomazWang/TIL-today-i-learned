# INSTALL_FAILED_DUPLICATE_PERMISSION

## Cause:

- This error happends when using differen key on building debug and releas version(which you should done)

## Solution:
- Solution 1: (the simple way)
    + Simplely uninstall the app with key different from what you try to build.
- Solution 2: (Use different applicationId and key between debug and release version)
    1. add applicationId suffix in debug 
    2. use dynamic application name while requiring permission

```Grovvy
buildTypes {
    ...
    debug {
        applicationIdSuffix ".debug"
       ...
    }
}
```


```xml
<uses-permission android:name="${applicationId}.permission.C2D_MESSAGE"/>
    <permission
            android:name="${applicationId}.permission.C2D_MESSAGE"
            android:protectionLevel="signature"/>
```



## Reference:

> - http://stackoverflow.com/questions/27043933/install-failed-duplicate-permission-c2d-message/36992939#36992939



