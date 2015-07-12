---
layout: post
title: Creating an Android Custom View in Xamarin
---

Hello!

I've been playing with Xamarin on Android quite a bit recently. If you don't
know what Xamarin is and you're interested in cross-platform development, you should definitely check it out: [Xamarin](http://xamarin.com)

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

<code>

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

<code>


### Step 1

Now, let's create a class that will implement our custom view. Right click on your project and add a new file. Call it whatever you like but make sure you're consistent
thoughout the rest of the tutorial:


![step1](public/step1.png "Step 1")


Lets extend the View class and put the necessary minimum we need to get the view class working. Don't be too concerned with the attributeset and style constructor as that is somewhat outside the (time) scope of this tutorial.

<code>

	using Android.Views;
	using Android.Content;
	using Android.Util;

	namespace XamarinDemo
	{
		public class AwesomeView : View
		{
			Context mContext;
			public AwesomeView(Context context) :
			base(context)
			{
				init (context);
			}
			public AwesomeView(Context context, IAttributeSet attrs) :
			base(context, attrs)
			{
				init (context);
			}

			public AwesomeView(Context context, IAttributeSet attrs, int defStyle) :
			base(context, attrs, defStyle)
			{
				init (context);
			}

			private void init(Context ctx){
				mContext = ctx;
			}
		}
	}

<code>


Lets include this view in our layout


<code>

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:orientation="vertical"
	    android:layout_width="fill_parent"
	    android:layout_height="fill_parent">

	    <xamarindemo.AwesomeView
	    	android:id="@+id/awesomeview_main"
	    	android:layout_height="wrap_content"
	    	android:layout_width="wrap_content"
	    />
	 
	</LinearLayout>

<code>


Hit the play button and you'll see...Nothing. Well that's because we aren't doing any drawing yet.


### Step 2 

The first thing we need to do to start drawing is to override the onDraw method for the view. We'll also create two stub functions called drawBigCircle and drawSmallCircle to make our code a little easier to work with. So you should've added the following to your view:

<code>


		private void drawBigCircle(Canvas canvas){
		}

		private void drawSmallCircles(Canvas canvas){
		}

		protected override void OnDraw(Canvas canvas){
			drawSmallCircles (canvas);
			drawBigCircle (canvas);
		}


<code>


Lets start with drawing the little circles at the bottom. Add the following to drawSmallCircles:

<code>

		const int NUM_BUBBLES = 5;
		int radius = 60;
		private void drawSmallCircles(Canvas canvas){

			int spacing = Width / NUM_BUBBLES;
			int shift = spacing / 2;
			int bottomMargin = 10;

			var paintCircle = new Paint (){ Color = Color.White};
			for (int i = 0; i < NUM_BUBBLES; i++) {
				int x = i * spacing + shift;
				int y = Height - radius * 2 - bottomMargin;
				canvas.DrawCircle (x, y, radius, paintCircle);
			}

		}

<code>

Note that the 0,0 starts at the top left corner (as with most drawing engines). We equally space the 5 circles and show them just above the bottom of the screen. You should see something like this when you hit play:



![step2](public/step2.png "Step 2")


If you understood that, then drawing the big circle should be even easier:


<code>



<code>
	