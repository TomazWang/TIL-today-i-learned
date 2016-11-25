# "match_parent" not working with Recycler View Item

**Q**: Sometimes `match_parent` wont work `RecyclerVew` or `ListView`. Even you clearly set `match_parent` in the layout file.

**A**: set **parent** when inflating the item view.

LayoutInflater.inflate() has three parameter. First one is layout resource file, the xml file you want to inflate. Second is the parent ViewGroup you wish this view to add in.  The last one is whether you want this new view add to the **parent** ViewGroup right after it inflate.
In the case of ListView and RecyclerView (and mostly all of the case), the last parameter is set to `false` since you (or Adapter) will add the view to a ViewGroup later. However, if the second parameter is set to `null`, the view will generate with `wrap_content` width (since there's no parent to match). That's why you shouldn't pass `null` as parent to the `inflate()` method.
 
 

 