# Save Bitmap as Image File

把一個Custom View 中Canvas上畫的所有東西存到空間中。

> See Also:
> - [Android Developer/ Saving Files](https://developer.android.com/training/basics/data-storage/files.html)
> - [Android Developer/ MediaScannerConnection](https://developer.android.com/reference/android/media/MediaScannerConnection.html )
> - [Stackoverflow/ How to save a bitmap on internal storage](http://stackoverflow.com/questions/15662258/how-to-save-a-bitmap-on-internal-storage)

### Step 1: 拿到要存的Bitmap.

這邊在我的一個Custom View 中拿取 `Canvas` 上畫的全部東西。
```java
public Bitmap getBitmap(){
	this.setDrawingCacheEnabled(ture);
	return getDrawingCache();
}
```
> See Also:
> - [Stackoverflow/ converting a canvas into bitmap image in android](http://stackoverflow.com/questions/4013725/converting-a-canvas-into-bitmap-image-in-android)
> - [解決getDrawingCache方法回傳null](https://magiclen.org/android-drawingcache/)

### Step 2: 建立一個 `AsyncTask` 來存檔以免影響到 `UIThread`

> More about AsyncTask:
> - [Youtube/ Understanding Android Threading.](https://www.youtube.com/watch?v=0Z5MZ0jL2BM)
> - [Youtube/ Good AsyncTask Hunting. ](https://www.youtube.com/watch?v=jtlRNNhane0)
> - [Android Developer/ AsyncTask](https://developer.android.com/reference/android/os/AsyncTask.html)
> - [AsyncTask - Thread 外的另一選擇](http://oldgrayduck.blogspot.tw/2013/01/androidasynctask-thread.html)

```java

public class SaveImageTask extends AsyncTask<Bitmap, Void, File>{
  	...
	private final Context mContext;

  	// listener when save complete
    private final Callback mCallback;

    public SaveImageTask(Context context, Callback callback){
        this.mContext = context;
        this.mCallback = callback;
    }

	@Override
    protected File doInBackground(Bitmap... bitmaps) {
        Bitmap image = bitmaps[0];
        return saveBitmapToFile(mContext, image);
    }

    @Override
    protected void onPostExecute(File file) {
        super.onPostExecute(file);
        mCallback.onSaveComplete(file);
    }

    public interface Callback{
        void onSaveComplete (File filePath);
    }

	public static File saveBitmapToFile(Context ctx, Bitmap bitmap){
       // Save Bitmap here
	}

    ...
}
```

### Step 3: 建立方法 `getOutputMediaFile()`來得到存檔的路徑(return `File` ).

__加入permissions__

在要進入External Storage前，要先向系統取得permissions

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```
> See Also:
> - [System Permissions](https://developer.android.com/guide/topics/security/permissions.html)
> - [Android 6.0的Permission權限設計](http://litotom.com/2016/05/15/android-6-permission/)

__決定存檔的位置__

- [存在內部空間(Internal Storage)](https://developer.android.com/training/basics/data-storage/files.html#WriteInternalStorage)

    - 拿到app的目錄
        ```java
        File file = new File(context.getFilesDir(), filename);
        ```
    - 拿到暫存目錄
        ```java
        File file = new File(context.getCacheDir(), filename);
        ```

- [存在外部空間(External Storage)](https://developer.android.com/training/basics/data-storage/files.html#WriteExternalStorage)

    使用[Environment](https://developer.android.com/reference/android/os/Environment.html)

    1. 判斷空間是否連接(`getExternalStorageState()`)

    ```java
    public boolean isExternalStorageWritable() {
        String state = Environment.getExternalStorageState();
        if (Environment.MEDIA_MOUNTED.equals(state)) {
            // 已連接，可以使用
            return true;
        }
        return false;
    ```

    2. 存檔時，可以把檔案存成公開或是私人的
    	- 公開檔案：可以讓使用者自行使用，移除App後依然存在。
        - 私人檔案：只有App可以使用，移除App後一併移除。

    3. 使用Enviroment中的[常數](https://developer.android.com/reference/android/os/Environment.html#DIRECTORY_ALARMS)拿取資料夾路徑


__完整Code__



```java

public static File getOutputMediaFile(Context context){

    String state = Environment.getExternalStorageState();
    if (! Environment.MEDIA_MOUNTED.equals(state)) {
    	Log.w(TAG, "getOutputMediaFile: Environment storage not writable");
    	return null;
    }

    File mediaStorageDir = new File(
        Environment
            .getExternalStoragePublicDirectory
                    (Environment.DIRECTORY_PICTURES), APP_NAME);


	// Create the storage directory if it does not exist
    if (! mediaStorageDir.exists()){
    	if (! mediaStorageDir.mkdirs()){
      	     return null;
        }
    }

	// Create a media file name
	String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	File mediaFile;

    String mImageName = timeStamp +".jpg";
	mediaFile = new File(mediaStorageDir.getPath() + File.separator + mImageName);
	return mediaFile;

}

```

### Step 4: 在 `saveBitmapToFile()` 中，實際將 `Bitmap` 存成檔案.

使用 `Bitmap.compress()` 來把Bitmap 壓縮成圖片檔。

```java
public static File saveBitmapToFile(Context ctx, Bitmap bitmap){

    File pictureFile = getOutputMediaFile(ctx);
    if (pictureFile == null) {
        Log.d(TAG,
            "Error creating media file, check storage permissions: ");
        return null;
    }

    try {
        FileOutputStream fos = new FileOutputStream(pictureFile);
        bitmap.compress(Bitmap.CompressFormat.JPEG, 90, fos);
        fos.close();
    } catch (FileNotFoundException e) {
        Log.d(TAG, "File not found: " + e.getMessage());
    } catch (IOException e) {
        Log.d(TAG, "Error accessing file: " + e.getMessage());
    }

    return pictureFile;
}

```
> See Also:
> - [Android Developer/ Bitmap#compress](https://developer.android.com/reference/android/graphics/Bitmap.html#compress(android.graphics.Bitmap.CompressFormat, int, java.io.OutputStream))
> - [Android Developer/Bitmap.CompressFormat](https://developer.android.com/reference/android/graphics/Bitmap.CompressFormat.html)



### Step 5: 建立callback function

因為系統不會知道我們加入了一張新的圖片，所以我們使用 `MediaScannerConnection` 來叫系統掃描我們剛剛建立的檔案，這樣這個檔案才會出現在相簿中。

```java
private void onSaveComplete(File filePath) {
    if(filePath == null){
        Log.w(TAG, "onSaveComplete: file dir error");
            return;
        }

        Toast saveCompleteMsg =
            Toast.makeText(getActivity(), getString(R.string.saveComplete), Toast.LENGTH_SHORT);
        saveCompleteMsg.show();

        Log.d(TAG, "onSaveComplete: save image in "+filePath);

        MediaScannerConnection.scanFile(
                getActivity(),
                new String[]{filePath.getAbsolutePath()},
                null,
                new MediaScannerConnection.MediaScannerConnectionClient() {
                    @Override
                    public void onMediaScannerConnected() {
                        Log.d(TAG, "onMediaScannerConnected");
                    }

                    @Override
                    public void onScanCompleted(String path, Uri uri) {
                        Log.d(TAG, "onScanCompleted");
                    }
                }
            );

        }
```



### Step 6: 使用我們上面健的 `SaveImageTask` 來存檔

```java
private void saveImage(Bitmap bitmap) {
	SaveImageTask saveImageTask = new SaveImageTask(getActivity(), filePath -> onSaveComplete(filePath));
	saveImageTask.execute(bitmap);
  }
```
