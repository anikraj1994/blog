---
title: Animation on Android
layout: post
description: A buttery smooth animation expedition
date: '2017-05-14'
tags:
- animation
- android
comments: true
---

This will be about the animations I tried to implement on Android using its native APIs and 3rd party libraries. 

I fell in love with the simplicity of animations on the Web platform. A few lines of CSS could produce beautiful animations. It was simple, reusable and easy to learn. Android needs a bit more work, but it's worth it at the end. It's easy to get an animation set up on the Web but it's not as smooth as a native application animation. 

## Rebound by Facebook
> Rebound is a Java library that models spring dynamics. Rebound spring models can be used to create animations that feel natural by introducing real world physics to your application.

Rebound is very easy to use and get started with. It is well documented [here](http://facebook.github.io/rebound/). 

The main concept lies around a spring system which can hold multiple springs. We listen to the motion of the spring and update our views accordingly.  The following code found on their website kinda explains everything you need to get started. 

```java
// Create a system to run the physics loop for a set of springs.
SpringSystem springSystem = SpringSystem.create();

// Add a spring to the system.
Spring spring = springSystem.createSpring();

// Add a listener to observe the motion of the spring.
spring.addListener(new SimpleSpringListener() {

  @Override
  public void onSpringUpdate(Spring spring) {
    // You can observe the updates in the spring
    // state by asking its current value in onSpringUpdate.
    float value = (float) spring.getCurrentValue();
    float scale = 1f - (value * 0.5f);
    myView.setScaleX(scale);
    myView.setScaleY(scale);
  }
});

// Set the spring in motion; moving from 0 to 1
spring.setEndValue(1);
```

`OnSpringUpdate` we get the value of the spring as a float, ranging from 0-1. We use this value to update our views. This example here scales to half the size of the original view. We can do alot of animations by just playing with the `value`. Let's see a few easy ones.

### Scale up and down

<video height="400px" src="/animation1.mp4" autoplay  loop></video>


```java
 // Create a system to run the physics loop for a set of springs.
        SpringSystem springSystem = SpringSystem.create();

        // Add a spring to the system.
        spring = springSystem.createSpring();

        // Add a listener to observe the motion of the spring.
        spring.addListener(new SimpleSpringListener() {

            @Override
            public void onSpringUpdate(Spring spring) {
                // You can observe the updates in the spring
                // state by asking its current value in onSpringUpdate.
                float value = (float) spring.getCurrentValue();
                float scale = 1f - (value * 0.5f);
                testImage.setScaleX(scale);
                testImage.setScaleY(scale);
            }
        });


        //Touch handler to handle down and up events
        testImage.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()){
                    case MotionEvent.ACTION_DOWN:
                        spring.setEndValue(1);
                        break;
                    case MotionEvent.ACTION_UP:
                        spring.setEndValue(0);
                        break;
                }
                return true;
            }
        });
```

### Translation

<video height="400px" src="/animation2.mp4" autoplay  loop></video>


```java
        // Create a system to run the physics loop for a set of springs.
        SpringSystem springSystem = SpringSystem.create();

        // Add a spring to the system.
        spring = springSystem.createSpring();

        // Add a listener to observe the motion of the spring.
        spring.addListener(new SimpleSpringListener() {

            @Override
            public void onSpringUpdate(Spring spring) {
                // You can observe the updates in the spring
                // state by asking its current value in onSpringUpdate.
                float value = (float) spring.getCurrentValue();
                float scale = value * 500f;
                testImage.setTranslationX(scale);
                testImage.setTranslationY(scale);
            }
        });



        //Touch handler to handle down and up events
        testImage.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()){
                    case MotionEvent.ACTION_DOWN:
                        spring.setEndValue(1);
                        break;
                    case MotionEvent.ACTION_UP:
                        spring.setEndValue(0);
                        break;
                }
                return true;
            }
        });
````

### Complex animations (combined)

<video height="400px" src="/animation3.mp4" autoplay  loop></video>

```java
testImage = (ImageView)findViewById(R.id.testImage);
        tooltip = (TextView)findViewById(R.id.tooltip);
        tooltip.setAlpha(0);
        tooltip.setTranslationY(-150f);

        // Create a system to run the physics loop for a set of springs.
        SpringSystem springSystem = SpringSystem.create();

        // Add a spring to the system.
        spring = springSystem.createSpring();
        toolSpring= springSystem.createSpring();
        // Add a listener to observe the motion of the spring.
        spring.addListener(new SimpleSpringListener() {

            @Override
            public void onSpringUpdate(Spring spring) {
                // You can observe the updates in the spring
                // state by asking its current value in onSpringUpdate.
                float value = (float) spring.getCurrentValue();
                float scale = value * 150f;
                //tooltip.setAlpha(value);
                testImage.setTranslationY(-scale);
                //tooltip.setTranslationX(scale);
                if(value>=.8)
                toolSpring.setEndValue(1);
                else
                toolSpring.setEndValue(0);

            }
        });


        toolSpring.addListener(new SimpleSpringListener() {

            @Override
            public void onSpringUpdate(Spring spring) {
                // You can observe the updates in the spring
                // state by asking its current value in onSpringUpdate.
                float value = (float) spring.getCurrentValue();
                float scale = value * 150f;
                tooltip.setAlpha(value);
                tooltip.setTranslationX(scale);


            }
        });



        //Touch handler to handle down and up events
        testImage.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()){
                    case MotionEvent.ACTION_DOWN:
                        spring.setEndValue(1);
                        break;
                    case MotionEvent.ACTION_UP:
                        spring.setEndValue(0);
                        break;
                }
                return true;
            }
        });
```

And for the layout

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_rebound_animated_button_with_tooltip"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.anikrajc.animation.rebound.ReboundAnimatedButtonWithTooltip">
    <ImageView
        android:layout_width="wrap_content"
        android:layout_centerInParent="true"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"
        android:id="@+id/testImage"/>

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="ToolTip"
        android:textColor="#fff"
        android:id="@+id/tooltip"
        android:background="#000"
        android:padding="5dp"
        android:layout_centerInParent="true"
        />
</RelativeLayout>
```