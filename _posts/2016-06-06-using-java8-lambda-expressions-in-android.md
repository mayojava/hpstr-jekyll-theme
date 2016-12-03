---
layout: post
title: Using Java 8 Lambda expressions in Android
description: "A guide on using Java 8 lambda expressions in Android"
tags: [Java 8, Lambda Expressions]
categories: [Android, Java]
---

## What are Lambda expressions

Lambda Expressions are one of the most important features added to Java 8. Prior
to Lambda Expressions, implementing functional interfaces i.e interfaces with
only one abstract method has been done using syntax that may seem cumbersome and
unclear. In cases like this, what we are trying to do is pass a functionality
as an argument to a method, such as what happens when a button is clicked. Lambda
expressions enables you to do just that, in a way that is much more compact and
clear. In android for example, lambdas can replace anonymous inner
class when providing event listeners.

## Syntax of Lambda Expressions

A lambda expression consist of the following:  

* A comma separated list of formal parameters enclosed in parentheses. The data
types of the parameters in a lambda expression can be omitted. Also the
parenthesis can be omitted if there is only one parameter. For example:
{% highlight java %}
TextView textView = (TextView) findViewById(R.id.text_view);
textView.setOnLongClickListener(v -> System.out.println("Long Click"));
{% endhighlight %}

* The arrow token `->`
* A body which contains a single expression or a statement block. If a single
expression is specified, the java runtime evaluates the expression and then return
its value. To specify a statement block, enclose statements in curly braces `"{}"`

<!-- more -->

## Lambda Expressions in Android
To use Lambda Expressions and other Java 8 features in Android, you need to use
the new Jack toolchain. Open your module level `build.gradle` file and add the
following:

{% highlight groovy %}
android {
  ...
  defaultConfig {
    ...
    jackOptions {
      enabled true
    }
  }
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
{% endhighlight %}

sync your `build.gradle` file and if you are having any issue with build tools,
you may need to update `buildToolsVersion` in your `build.gradle` file to `"24rc4"`
or just download the latest Android SDK Build-tools from the SDK Manager, under
the Tools (Preview channel).

![Alt sdk manager build tools](/images/sdk_manager_build_tool.png)

## Example
Adding a click listener to a button

**without lambda expression**
{% highlight java %}
Button button = (Button)findViewById(R.id.button);
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Toast.makeText(this, "Button clicked", Toast.LENGTH_LONG).show();
    }
});
{% endhighlight %}

**with lambda expressions**
It is as simple as:
{% highlight java %}
Button button = (Button)findViewById(R.id.button);
button.setOnClickListener(v -> Toast.makeText(this, "Button clicked", Toast.LENGTH_LONG).show());
{% endhighlight %}

As we can see above, using lambda expressions makes implementing a functional
interface clearer and compact. Standard functional interfaces can be found in
the `java.util.function` package [included in Java 8]. These interfaces can be
used as target types for lambda expressions and method references.

Another way to have Java 8 features in your Android app is using the
RetroLambda plugin. You can check out the RetroLambda github repo [here](https://github.com/evant/gradle-retrolambda).


## References
[https://developer.android.com/preview/j8-jack.html#configuration](https://developer.android.com/preview/j8-jack.html#configuration)
[https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
