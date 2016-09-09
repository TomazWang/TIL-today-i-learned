# Sharing Image With Intent
Make a share method to share image by intent.

> See Also:
> - [CodePath/Sharing Content with Intents](https://guides.codepath.com/android/Sharing-Content-with-Intents#sharing-local-content)
> - [Stackoverflow/How to use “Share image using” sharing Intent to share images in android?](http://stackoverflow.com/questions/7661875/how-to-use-share-image-using-sharing-intent-to-share-images-in-android)

----

### Sharing Content
Sharing content is easy. Simply invoke an intent with `ACTION_SEND`.

1. new a Intent and pass Intent.ACTION_SEND as argument.
2. use `setType()` and use [MIME type](https://www.sitepoint.com/web-foundations/mime-types-complete-list/) as argument.
3. use `putExtra()` to put file inside intent.
4. start Intent using `startActivity()` and create a [chooser](https://developer.android.com/reference/android/content/Intent.html#createChooser)

### Sample Codes

__To send images:__

``` java
private void share(){

    // get file directory.
    final File pictureFile = getFileDir();

    // invoke an intent with ACTION_SEND
    final Intent shareIntent = new Intent(Intent.ACTION_SEND);

    shareIntent.setType("image/jpeg");

    shareIntent.putExtra(Intent.EXTRA_STREAM, Uri.fromFile(pictureFile));

    startActivity(Intent.createChooser(shareIntent, getString(R.string.share_using)));

}
```

__Send Bitmap__

To send bitmap as image file. First we have to save bitmap as an image file then send the image.
See [this post](Save Bitmap as Image File.md) to learn more about saving bitmap.

---

TODOs:
- Send multiple images to Facebook.
- Sharing other contents.
