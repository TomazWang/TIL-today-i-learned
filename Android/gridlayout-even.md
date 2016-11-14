# Even the column width and row height

Today I learned GridLayout not always positions its child as even width(or height). To achieve this effect, the easiest way is to add `columnWeight` and `rowWeight` in every child. However, `columnWeight` and `rowWeight` are add after API 21. If you want to support devices before API 21, `android.support.v7.widget.GridLayout` is the answer.
