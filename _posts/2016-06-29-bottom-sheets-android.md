---
layout: post
title: Bottom Sheets in Android
description: "Bottom Sheets in Android"
tags: [Android, Support Library, Bottom Sheets]
categories: [Android]
image:
    feature: bottom-sheet.png
    credit: gunhansancar
    creditlink: http://gunhansancar.com/bottom-sheets-in-android/
---

Bottom Sheet is a material design component, that was added to design support
library in version 23.2. Bottom sheet is a very simple window that displays from the
bottom of the screen, and can be used to reveal more content to the user.
Examples of bottom sheet can be seen in some of Google's apps and widgets,
such as the Place Picker from the Places API.

In this post, I will be showing how you can use bottom sheets in your apps.

<!-- more -->

Bottom sheet can be a structural part of a UI or a temporary modal. To achieve
these, the design support library provides:

* `BottomSheetBehavior` to be used with `CoordinatorLayout`
* `BottomSheetDialog` which is a dialog with a bottom sheet behavior
* `BottomSheetDialogFragment` which is an extension of `DialogFragment`, that
creates a `BottomSheetDialog` instead of a standard dialog.

To get started with `BottomSheet` in your apps, add the support library
dependecy to your `build.gradle` file:

{% highlight groovy %}
    compile 'com.android.support:design:<latest-library-version>'
{% endhighlight %}

## Creating a Persistent Bottom Sheet
To create a bottom sheet as part of your interface layout,

* Create a layout with `CoordinatorLayout` as the root, then add the
the `view` or `layout` for the bottom sheet, as a **direct** child of the
`CoordinatorLayout`. The bottom sheet layout must have `app:layout_behavior`
attribute, with value `android.support.design.widget.BottomSheetBehavior`.

For example:
{% highlight java %}
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
   xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:tools="http://schemas.android.com/tools"
   xmlns:app="http://schemas.android.com/apk/res-auto"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   tools:context="com.mayojava.sample.bottomsheetdemo.MainActivity">

   <!-- main content layout-->
   <RelativeLayout
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:paddingBottom="@dimen/activity_vertical_margin"
       android:paddingLeft="@dimen/activity_horizontal_margin"
       android:paddingRight="@dimen/activity_horizontal_margin"
       android:paddingTop="@dimen/activity_vertical_margin">
       ......
   </RelativeLayout>

   <!-- bottom sheet layout -->
   <RelativeLayout
       android:id="@+id/linear_layout_bottom_sheet"
       android:layout_width="match_parent"
       android:layout_height="220dp"
       app:behavior_peekHeight="80dp"
       app:behavior_hideable="true"
       app:layout_behavior="@string/string_bottom_sheet_behavior"
       android:elevation="@dimen/z_bottom_sheet"
       android:background="@color/color_bottom_sheet">
       <TextView
           android:id="@+id/text_view_sheet_title"
           android:layout_width="match_parent"
           android:layout_height="wrap_content"
           android:text="@string/text_pull_to_show_more"
           android:textSize="@dimen/text_size_medium"
           android:padding="@dimen/activity_vertical_margin"/>

       <TextView
           android:id="@+id/text_view_more_content"
           android:text="@string/text_more_contet_to_user"
           android:textSize="@dimen/text_size_big"
           android:textStyle="bold"
           android:layout_width="match_parent"
           android:layout_height="wrap_content"
           android:layout_below="@+id/text_view_sheet_title"
           android:paddingLeft="@dimen/activity_vertical_margin"
           android:paddingRight="@dimen/activity_vertical_margin"/>

       <Button
           android:text="@string/text_click_me"
           android:layout_width="match_parent"
           android:layout_height="wrap_content"
           android:layout_alignParentLeft="true"
           android:layout_marginLeft="@dimen/activity_vertical_margin"
           android:layout_marginRight="@dimen/activity_vertical_margin"

           android:layout_marginTop="@dimen/activity_vertical_margin" android:layout_below="@+id/text_view_more_content"/>

   </RelativeLayout>

</android.support.design.widget.CoordinatorLayout>

{% endhighlight %}

* For the bottom sheet to be displayable, we need a to create a
`BottomSheetBehavior`. This can be done by calling `BottomSheetBehavior.from()`,
passing a reference to the view with the bottom behavior attribute to the call.
This should be done from the `Activity` that uses the bottom sheet.

{% highlight java %}
LinearLayout bottomSheetLayout  
    = (LinearLayout) findViewById(R.id.layout_bottom_sheet);

//get bottom sheet behavior from bottom sheet view
BottomSheetBehavior bottomSheetBehavior = BottomSheetBehavior.from(mLayoutBottomSheet);
{% endhighlight %}

With a reference to the `BottomSheetBehavior` object, we can call the `setState` method, passing different behavior constants, that toggles the state of the bottom sheet.

* `BottomSheetBehavior.STATE_EXPANDED`: Completely expands the bottom sheet.
* `BottomSheetBehavior.STATE_HIDE`: Completely hides the bottom sheet.
* `BottomSheetBehavior.STATE_COLLAPSED`: Sets the bottom sheet height with the value set on the `peekHeight` attribute.

{% highlight java %}    
//to expand the bottom sheet
mbottomSheetBehavior.setState(BottomSheetBehavior.STATE_EXPANDED);

//to collapse the bottom sheet
mbottomSheetBehavior.setState(BottomSheetBehavior.STATE_COLLAPSED);
{% endhighlight %}

`peekHeight` can be used to show a preview of the bottom sheet when the screen is displayed. It can be set in xml using the `app:behavior_peekHeight` attribute, or in code, by calling `setPeekHeight` on the `BottomSheetBehavior` object.

{% highlight java %}
mbottomSheetBehavior.setPeekHeight(320);
{% endhighlight %}


## Creating a Modal Bottom Sheet
A modal bottom sheet is a dialog with a bottom sheet behavior, and is not part of your view hierarchy. It can be used as alternative to menus, content choosers or simple dialogs in your application. It can also present deep-linked content from other apps.

To do this:

* Create a class that extends `BottomSheetDialogFragment`, inflated with the layout that will be used as the content of the modal dialog.

**layout_custom_bottom_sheet.xml**
{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_vertical_margin"
    android:paddingRight="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingTop="@dimen/activity_vertical_margin">

    <ImageView
        android:id="@+id/ic_image"
        android:src="@mipmap/ic_launcher"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginRight="@dimen/margin_normal"
        android:layout_marginTop="@dimen/activity_vertical_margin"/>

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello bottom sheet"
        android:textSize="@dimen/text_size_medium"
        android:layout_marginTop="@dimen/activity_vertical_margin"
        android:layout_toRightOf="@+id/ic_image"
        android:layout_alignTop="@+id/ic_image"/>

    <TextView
        android:layout_marginTop="@dimen/activity_vertical_margin"
        android:layout_below="@+id/ic_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/text_lorem_ipsum"
        android:lineSpacingExtra="@dimen/margin_small"
        android:layout_marginBottom="@dimen/margin_normal"/>


</RelativeLayout>
{% endhighlight %}

* Create an instance of the modal bottom sheet and show it with the `show` method, passing the `FragmentManager` and a string `tag` as parameters.

{% highlight java %}
CustomBottomSheetDialog bottomSheetDialog = CustomBottomSheetDialog.getInstance();
bottomSheetDialog.show(getSupportFragmentManager(), "Custom Bottom Sheet");
{% endhighlight %}

|   |   |
|![Alt bottom sheet screen 1](/images/result_bottom_sheet_2.png)|![Alt bottom sheet screen 2](/images/result_bottom_sheet.png)|

The complete code for this tutorial can be found in this [github repository](https://github.com/mayojava/BottomSheetDemo).


## Conclusion
Bottom sheets can be used to display unobtrusive dialogs, as well as provide users with context menus or pickers in a very subtle way.


## References
* [http://www.hidroh.com/2016/06/17/bottom-sheet-everything/?utm_source=androiddevdigest](http://www.hidroh.com/2016/06/17/bottom-sheet-everything/?utm_source=androiddevdigest)

* [http://www.materialdoc.com/bottom-sheets/](http://www.materialdoc.com/bottom-sheets/)

* [https://material.google.com/components/bottom-sheets.html](https://material.google.com/components/bottom-sheets.html)
