AutoGridView
============

Solution to Android GridView overlap
Explanation: http://obduro.nl/blog/solution-to-android-gridview-overlap/


Usage
=====
It is really simple, just define the AutoGridView in XML like you would with a normal GridView. Make sure you set the “android:numColumns” value to a valid integer resource, and you are good to go.

android:id="@+id/activity_version_GridView"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:listSelector="@drawable/selector"
android:numColumns="@integer/pair_cols"
android:stretchMode="columnWidth">

Attaching an adapter or listener is exactly the same as a GridView as well.
AutoGridView grid = (AutoGridView) findViewById(R.id.activity_version_GridView);
PairAdapter adapter = new PairAdapter(getBaseContext(), -1, list);
grid.setAdapter(adapter);