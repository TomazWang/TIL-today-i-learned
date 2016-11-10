# Input Method

> See Also:
> - [Specifying the Input Method Type][input-method-type](developer.andoid.com)
> - [InputMethodManager][imm-source](developer.android.com)

## Specific Input Type

You can (should) always declare a input type when you place an EditText in layout. By adding `android:inputType` attribute to the `<EditText>` tag.

```xml
<EditText
    android:id="@+id/phone"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:hint="@string/phone_hint"
    android:inputType="phone" />
```

Some useful inputType are `text`, `phone`,`number` and `textPassword`. You can find more in [here][input-types].You can also combine several inputTypes together like `number` and `numberPassword`.

## Return, Next, Send and Done

After input certain type of text. Let's make user move from one field to another. Add [android:imeOptions][imeOptions] in your `<EditText>`.

Sometimes, actions like `actionDone`, `actionGo` won't neccessary change. In this case, you can set `OnEditorActionListener` for your EditText.

```java
edittext.setOnEditorActionListener(new TextView.OnEditorActionListener() {
           @Override
           public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
               if (actionId == EditorInfo.IME_ACTION_DONE) {
                    // close keyboard when action done
                    InputMethodManager inputMethodManager = (InputMethodManager)getActivity().getSystemService(Context.INPUT_METHOD_SERVICE);
                    inputMethodManager.hideSoftInputFromWindow(keyword.getWindowToken(), 0);
                   return true;
               }
               return false;
           }
       });
```






















[imeOptions]:https://developer.android.com/reference/android/widget/TextView.html#attr_android:imeOptions
[input-types]:https://developer.android.com/reference/android/widget/TextView.html#attr_android:inputType
[input-method-type]:https://developer.android.com/training/keyboard-input/style.html
[imm-source]:https://developer.android.com/reference/android/view/inputmethod/InputMethodManager.html
