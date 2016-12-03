---
layout: post
title: AnimatedVectorDrawable XML Bundles
description: "Creating AnimatedVectorDrawable XML resource using a single XML file"
tags: [Android, AnimatedVectorDrawable]
categories: [Android]
---


Creating AnimatedVectorDrawables on Android requires you to create three xml resources that represents each of the following
`animated-vector` , `objectAnimator` and `vector`. The `<vector />` is the actual file that represents the vector drawable.
The `<objectAnimator />` defines the animation to  apply to different parts of the vector and the `<animated-vector />`
is used to link the vector drawable and the animation together.

As an example, to create an animated vector drawable that transforms a play icon to stop icon, the following xml files will be created.

<!-- more -->

{% highlight xml %}
    <!-- res/drawable/ic_play.xml -->

    <?xml version="1.0" encoding="utf-8"?>
    <vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:width="48dp"
        android:height="48dp"
        android:viewportWidth="48"
        android:viewportHeight="48">

        <path
            android:name="play"
            android:pathData="M 20 15 L 32 24 L 20 33 Z"
            android:fillColor="@android:color/white"/>
    </vector>
{% endhighlight %}

Then an animator that defines the transformation on the `pathData` property of the vector and morphs it into the stop icon.

 {% highlight xml %}
    <!-- res/animator/animates_play_to_stop.xml -->

    <?xml version="1.0" encoding="utf-8"?>
    <set xmlns:android="http://schemas.android.com/apk/res/android">
        <objectAnimator
            android:duration="300"
            android:interpolator="@android:anim/accelerate_decelerate_interpolator"
            android:propertyName="pathData"
            android:valueFrom="M 20 15 L 32 24 L 32 24 L 20 33 Z"
            android:valueTo="M 16 16 L 32 16 L 32 32 L 16 32 Z"
            android:valueType="pathType"/>
    </set>
{% endhighlight %}

Finally, we create the AnimatedVectorDrawable xml file links the animation to the vector:

{% highlight xml %}
    <!-- res/drawable/animated_drawable_play_to_stop.xml -->

    <animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:drawable="@drawable/ic_play">

        <target
            android:animation="@animator/animate_play_to_stop"
            android:name="play" />
    </animated-vector>
{% endhighlight %}

At IO 2016, Google announced a new xml bundle format that enables creating AnimatedVectorDrawables using a single xml file.
The XML bundle format requires build tools version 24 and above to work, and also requires adding the `aapt` namespace to your xml declaration.
For example, the three xml files for the AnimatedVectorDrawable above can be written in a single xml file as shown below:

{% highlight xml %}
    <!-- res/drawable/animated_drawable_play_to_stop.xml -->

    <?xml version="1.0" encoding="utf-8"?>
    <animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        xmlns:appt="http://schemas.android.com/aapt"
        tools:ignore="MissingPrefix">

        <appt:attr name = "android:drawable">
            <vector xmlns:android="http://schemas.android.com/apk/res/android"
                android:width="48dp"
                android:height="48dp"
                android:viewportWidth="48"
                android:viewportHeight="48">

                <path
                    android:name="play"
                    android:pathData="M 20 15 L 32 24 L 20 33 Z"
                    android:fillColor="@android:color/white"/>
            </vector>
        </appt:attr>

        <target
            android:name="play">
            <appt:attr name="android:animation">
                <objectAnimator
                    android:duration="300"
                    android:interpolator="@android:anim/accelerate_decelerate_interpolator"
                    android:propertyName="pathData"
                    android:valueFrom="M 20 15 L 32 24 L 32 24 L 20 33 Z"
                    android:valueTo="M 16 16 L 32 16 L 32 32 L 16 32 Z"
                    android:valueType="pathType"/>
            </appt:attr>
        </target>

</animated-vector>
{% endhighlight %}

The keys things to note in this new format are:

* Inclusion of the `appt` namespace inside the `<animated-vector ..` opening xml tag:
    {% highlight xml %}
        <animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:appt="http://schemas.android.com/aapt">
    {% endhighlight %}

* The `android:drawable` atttibute of the `<animated-vector` has been removed and now you put the vector xml directly within this tag:
    {% highlight xml %}
        <appt:attr name = "android:drawable">
             <!-- vector graphics xml -->
        </appt:attr>
    {% endhighlight %}
* Finally, within each of the `<tagret..` tags, you can either directly specify the `objectAnimator` wihtin an `<appt:attr name="android:animation>`, or
reference an external animation located in `res/animator/..`.

{% highlight xml %}
    <!-- directly specify the animation -->
    <tagret android:name="play">
        <appt:attr name="android:animation">
            <objectAnimator
                ....... />
        </appt:attr>
   </target>

   <!-- referenceing an external object animator -->
   <tagret android:name="play"
        android:animation="@animator/animate_play_to_stop"/>
{% endhighlight %}

You can add the `tools:ignore="MissingPrefix"` to suppress lint warnings because the bundle format is not yet recognized by lint.

## References
[Nick Butcher Google Plus Post on New XML Bundle format](https://plus.google.com/+NickButcher/posts/A8KKxnJdg4r)

[AnimatedVectorDrawables Bundle](https://blog.stylingandroid.com/animatedvectordrawable-bundles/)
