# Open Database from BroadcastReceiver

## Q:
How to open and add data when BroadcastReceiver receives some data?

## A:

```java

  @Override
  public void onReceive(Context context, Intent intent) {
      // use Context.openOrCreateDatabase()
      SQLiteDatabase db = context.openOrCreateDatabase(db_name, MODE_PRIVATE, null);
      // more
  }

```


## Q:
How do delete SQLiteDatabase?

## A:

```java

  context.deleteDatabase(DATABASE_NAME);
```
