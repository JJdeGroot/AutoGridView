AutoGridView
============
Solving GridView overlap on Android.

Usage
=====
Add the AutoGridView.java file to your project. Now you can simply define an AutoGridview element in the XML just like you would with a normal Gridview. **Make sure you set the “android:numColumns” value to a valid integer resource**, and you are good to go.

```xml
<nl.obduro.views.AutoGridView
        android:id="@+id/example_GridView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:listSelector="@drawable/selector"
        android:numColumns="@integer/grid_columns"
        android:stretchMode="columnWidth">
</nl.obduro.views.AutoGridView>
```

Attaching an adapter or listener is exactly the same as a GridView as well.
```java
AutoGridView grid = (AutoGridView) findViewById(R.id.activity_version_GridView);
PairAdapter adapter = new PairAdapter(getBaseContext(), -1, list);
grid.setAdapter(adapter);
```

I hope this helped you out, and if it did, please leave a comment or star the repository :)


Explanation
===========
Lately I have been working on a new Android app that heavily depends on GridViews. The GridView works great when all of your views are the same height. But when your views have different heights, the grid doesn't behave as expected. Views will overlap each other, causing an anaesthetically pleasing grid.

## The problem

Searching around the web for a solution I found out why this happens. The GridView uses the [far right view](https://plus.google.com/+ChrisBoyle/posts/XejzPf84HBP) in a row to determine the height. This can be found in the [GridView source code](http://grepcode.com/file/repo1.maven.org/maven2/org.robolectric/android-all/4.4_r1-robolectric-0/android/widget/GridView.java), at line xx Searching further for a work around I found a couple people with the same problem: [GridView item overlap when scrolling](http://stackoverflow.com/questions/23941801/gridview-item-overlaps-when-scrolling) & [How to make the GridView row height fit the tallest item?](http://stackoverflow.com/questions/7545915/gridview-rows-overlapping-how-to-make-row-height-fit-the-tallest-item/). A few solutions have been posted, but they didn't really satisfy me so I decided to try to get something working myself.

## The idea

1.  Determine which views are in which row
2.  Determine the height of each view in a row
3.  Determine the tallest view for each row
4.  Apply this tallest height to all views in the row

After finding the correct hooks to implement the calculation it started to work. The result is a GridView that does actually work as you would expect it to work!

## The methods

_private void init(AttributeSet attrs)_ 

In this method I determine the resource ID of the "numColumns" attribute defined in the XML. The amount of columns is needed for the calculations. GridView has a method _getNumColumns()_, but this requires API level 11.. 


_private void updateColumns()_ 

Reads the numColumns integer value from the resources. This is used to show different amount of columns depending on landscape or portrait mode. 


_public void setNumColumns(int numColumns)_ 

This method has been overridden to grab the new value of the number of columns, and to adjust the selection. The selection needs to be adjusted so the same row is shown to the user before a rotation. 


_protected void onLayout(boolean changed, int leftPos, int topPos, int rightPos, int bottomPos)_ 

This method has been overridden to calculate the new heights of the views. 


_protected void onConfigurationChanged(Configuration newConfig)_ 

This method has been overridden to update the amount of columns to show. 


_protected void onScrollChanged(int newHorizontal, int newVertical, int oldHorizontal, int oldVertical)_

This method has been overridden to check if new views need to be shown. If this is the case, the view heights must be recalculated. 


_private void setHeights()_ 

This is the method where all the magic happens. The used adapter gets retrieved, and by using this adapter we can get the displayed views. With the help of the numColumns variable the views per row can be determined. The tallest height gets calculated, and is then applied to each view.