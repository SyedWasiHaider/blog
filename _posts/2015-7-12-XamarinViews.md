---
layout: post
title: Creating an Android Custom View in Xamarin
---

Hello!

I've played with Xamarin on Android quite a bit recently. If you don't
what Xamarin is and you're interested in cross-platform development, you should definitely check it out: [Xamarin](http://xamarin.com)

### Custom Views

In this session, I am going to create a simple custom Android view with Xamarin.

### Why?

The beauty of Xamarin is that it allows use to use Xamarin forms to write UI code once and it will work perfectly on both Android and iOS (and even Windows phone!). However, 
when you need to do a really complicated custom view, using forms alone won't be enough. 

### What You Need to Get Started

* Xamarin Studio (or Visual studio with the Xamarin SDK)
* An Android Phone (ideally with 4.0+ but 3.0+ should work too)
* About 30min-45min of time to spare (I'll keep it short as possible!)


The view itself is a little contrived but the purpose here is to show how seamlessly the native Android code maps so to the Xamarin code. If you've ever done custom views in native Java, this will look extremely familiar.


### Setup

Fire up Xamarin studio and create a new Android project. 

### Clean up

Lets get rid of the button since we won't be using it. From the panel on the left, go to Resources -> Layout -> Main.axml and get rid of the button element. So your main.axml should look like this:

<code>

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:orientation="vertical"
    	android:layout_width="fill_parent"
    	android:layout_height="fill_parent">

	</LinearLayout>

	var k = 11;
	var test = test + 1;

<code>


