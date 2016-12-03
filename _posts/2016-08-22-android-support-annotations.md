---
layout: post
title: Android support Annotations
description: "Android Support Annotations"
tags: [Android, Android Support Annotations]
categories: [Android]
---

In this blog post, we are going to be talking about the Android Support
Annotations Library and why we should care about it.

Version 19.1 of the support library included a new annotations package which
contains a number of useful annotations that can be added to your code, to help
catch bugs. These annotations will help Android studio to check your code for
possible errors and report them to you. As of version 22.2 of the support
library, this annotations package has been enriched with an additional 13
annotations.

<!-- more -->

## Adding the Library to your project
The annotations library are not included in an Android project by default, as they
are shipped as a separate library. If you're using `appcompat library` however,
you already have access to the annotations, because appcompat itself depends on it.

In any case, to have access to the support library annotations in your android
project, add this line to your module level `build.gradle` file.

{% highlight groovy %}
compile 'com.android.support:support-annotations:<latest-library-version>'
{% endhighlight %}

These annotations are grouped together based on similarities in their usage and
functionality and based on these grouping, we have the following classes of
annotations in the support annotations library:

* Nullness Annotations
* Resource Annotations
* Thread Annotations
* Value Constraints Annotations
* Others are: Permissions Annotations, CheckResults Annotations and CallSuper
Annotations.

### Nullness Annotations
`@Nullable` and `@NonNull` annotations can be used to check the nullness of a
variable, parameter or method return value. `@NonNull` annotation means
that the value of the variable, parameter or method return value cannot be null.
Android studio generates a warning if this is violated. For example:

{% highlight java %}
    //pass a null argument to a method annotated as @NonNull
    doubleNumber(null);

    public int doubleNumber(@NonNull int num) {
        return num * 2;
    }
{% endhighlight %}

@Nullable means a variable is allowed to take a null value and if used with a
method, it means the method can return a null value. Null check should be done
on variables or return values whenever `@Nullable` annotation is used.

### Resource Annotations
Since resources are passed around in Android as integers, a code expecting a
string resource id can be passed an int pointing to a drawable and the compiler
will accept it. Resource type annotations allows type checking to be done in
situations like this. By annotating an int parameter with `@StringRes` for
examples, ensures that passing anything other than a `R.string` ref will be
flagged by the IDE.

{% highlight java %}
    public void setButtonText(@StringRes int id) {
         //set text on some button
    }

    //this will be flagged by the IDE
    setButtonText(R.drawable.icon);
{% endhighlight %}

Each of the android resource type has a corresponding resource type annotation.
Some other examples are `@DrawableRes`, `@ColorRes`, `@InterpolatorRes` and so
on. The general rule is, if there is a resource of type **"Foo"**, the corresponding
resource type annotation is **"FooRes"**.

Lastly, there is a special resource annotation called `@AnyRes` which is used
when the specific type of resource is not known but it must be a resource type.

### Thread Annotations
Thread annotations checks if a method is called from a specific type of thread.
Supported thread annotations are:

* @UiThread
* @MainThread
* @WorkerThread
* @BinderThread

`@MainThread` and `@UiThread` annotations are interchangeable and method calls
from either thread type are allowed for these annotations.

A class can be annotated with a thread annotation if all methods in that class
are called from the same thread. A good example of the usage of thread
annotations is in `AsyncTask`

{% highlight java %}
    @WorkerThread
    protected abstract Result doInBackground(Params... params);

    @MainThread
    protected void onProgressUpdate(Progress... values) {
    }
{% endhighlight %}

An error will be flagged if the `onProgressUpdate` method is called from a
thread other than the main thread.


### Value Constraints Annotations
`@IntRange`, `@FloatRange` and `@Size` annotations are used to validate the values
of passed parameters.
`@IntRange` annotation validates that the parameter is within the specified
range of integer values. The `setAlpha` method below for example, ensures that
the alpha parameter value is between 0 and 255 inclusive.

{% highlight java %}
    public void setAlpha(@IntRange(from=0, to=255) int alpha) {
        //set alpha
    }
{% endhighlight %}

`@FloatRange` likewise checks that a parameter value is within a specified
range of floating point values. The `@Size` annotation on the other hand, is
used to check the size of a collection or array, as well as the length of a
string. `@Size(min=1)` annotation can be used to check that a collection is not
empty and `@Size(2)` annotation checks that an array contains exactly two
values.

### CheckResult Annotations
This is used to ensure that the result of a method or its return value is
actually used. It's main purpose is to help with cases where users of the API
may be confused and may think the method has a side effect. As explained in the
support annotations documentation, a good example of this is the `String.trim`
method, which may be confusing to new Java developers, thinking that the method
call actually changes the string by removing whitespace. If the `String.trim`
method is annotated with `@CheckResult`, the IDE will flag uses of `String.trim`
where the caller does not do something with the result.

{% highlight java %}
    @CheckResult
    public String trim(@NonNull String string) {
        //remove whitespace from string
    }

    String s = "hello world ";

    //this will make the IDE flag an error since the result from the @CheckResult
    //annotated method is not used
    s.trim();
{% endhighlight %}

Other support annotations worth looking at are `@CallSuper`, `@Keep` and
`@RequiresPermission`. For a complete list check the android developer reference
for [support annotations](https://developer.android.com/reference/android/support/annotation/package-summary.html).


## Conclusion
Using support annotation makes helps to make obvious how the code works. It makes
the code predictable and also makes it easy for other developers integrating your
code or when you have to make changes to the code some times in the future.


## References
[Improve code Inspection with Annotations - Android developer doc](https://developer.android.com/studio/write/annotations.html#adding-nullness)

[Support Annotation documentation](http://tools.android.com/tech-docs/support-annotations)

[http://michaelevans.org/blog/2015/07/14/improving-your-code-with-android-support-annotations/](http://michaelevans.org/blog/2015/07/14/improving-your-code-with-android-support-annotations/)
