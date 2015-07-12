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

The beauty of Xamarin is that it allows you to use Xamarin forms to write UI code once and it will work perfectly on both Android and iOS (and even Windows phone!). However, 
sometimes when you need to do a really complicated custom view, using forms alone won't be enough. 

### What You Need to Get Started

* Xamarin Studio (or Visual studio with the Xamarin SDK)
* An Android Phone (ideally with 4.0+ but 3.0+ should work too)
* About 30-45min of time to spare (I'll keep it short as possible!)


The view itself is a little contrived but the purpose here is to show how seamlessly the native Android code maps so to the Xamarin code. If you've ever done custom views in native Java, this will look extremely familiar.


### Setup

Fire up Xamarin studio and create a new Android project. 

![step0](public/step0.png "Step 0")

### Clean up

Lets get rid of the button since we won't be using it. From the panel on the left, go to Resources -> Layout -> Main.axml and get rid of the button element. So your main.axml should look like this:

<code>

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:orientation="vertical"
    	android:layout_width="fill_parent"
    	android:layout_height="fill_parent">

	</LinearLayout>

</code>

Lets also get rid of the button related code in the MainActivity.cs (which can be found at root of the project). So you code should look something like this for now:

<code>


using Android.App;
using Android.OS;

namespace XamarinDemo
{
	[Activity (Label = "XamarinDemo", MainLauncher = true, Icon = "@drawable/icon")]
	public class MainActivity : Activity
	{

		protected override void OnCreate (Bundle bundle)
		{
			base.OnCreate (bundle);

			// Set our view from the "main" layout resource
			SetContentView (Resource.Layout.Main);
		
		}
	}
}

</code>


### Step 1

Now, let's create a class that will implement our custom view. Right click on your project and add a new file. Call it whatever you like but make sure you're consistent
thoughout the rest of the tutorial:


![step1](public/step1.png "Step 1")

