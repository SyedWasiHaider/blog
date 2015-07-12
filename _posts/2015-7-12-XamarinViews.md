---
layout: post
title: Creating an Android Custom View in Xamarin
---

Hello!

I've been playing with Xamarin on Android quite a bit recently. If you don't know what Xamarin is and you're interested in cross-platform development, you should definitely check it out: [Xamarin](http://xamarin.com). *Even if you don't want to use Xamarin, you can directly apply this knowledge to native Android Java.*

### Custom Views

In this session, I am going to create a simple custom Android view with Xamarin.

### Why?

The beauty of Xamarin is that it allows you to use Xamarin forms to write UI code once and it will work perfectly on both Android and iOS (and even Windows phone!). However, 
sometimes when you need to do a really complicated custom view, using forms alone won't be enough. 

### What You Need to Get Started

* Xamarin Studio (or Visual studio with the Xamarin SDK)
* An Android Phone (ideally with 4.0+ but 3.0+ should work too)
* Some free time (should take roughly 45min-1hr depending on how fast you want to go through it)


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

{% highlight java linenos %}	

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

{% endhighlight %}


### Step 1

Now, let's create a class that will implement our custom view. Right click on your project and add a new file. Call it whatever you like but make sure you're consistent
thoughout the rest of the tutorial:


![step1](public/step1.png "Step 1")


Lets extend the View class and put the necessary minimum we need to get the view class working. Don't be too concerned with the attributeset and style constructor as that is somewhat outside the (time) scope of this tutorial.

{% highlight java linenos %}	

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

{% endhighlight %}


Lets include this view in our layout


{% highlight html linenos %}	

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

{% endhighlight %}


Hit the play button and you'll see...Nothing. Well that's because we aren't doing any drawing yet.


### Step 2 

The first thing we need to do to start drawing is to override the onDraw method for the view. We'll also create two stub functions called drawBigCircle and drawSmallCircle to make our code a little easier to work with. So you should've added the following to your view:

{% highlight java linenos %}	


		private void drawBigCircle(Canvas canvas){
		}

		private void drawSmallCircles(Canvas canvas){
		}

		protected override void OnDraw(Canvas canvas){
			drawSmallCircles (canvas);
			drawBigCircle (canvas);
		}


{% endhighlight %}


Lets start with drawing the little circles at the bottom. Add the following to drawSmallCircles:




{% highlight java linenos %}	

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

{% endhighlight %}


Note that the 0,0 starts at the top left corner (as with most drawing engines). We equally space the 5 circles and show them just above the bottom of the screen. You should see something like this when you hit play:



![step2](public/step2.png "Step 2")


If you understood that, then drawing the big circle should be even easier.


{% highlight java linenos %}	

		int radius_big = 180;
		private void drawBigCircle(Canvas canvas){
			var paintCircle = new Paint (){ Color = Color.White};
			canvas.DrawCircle (Width/2.0, Height/2.0, radius_big, paintCircle);
		}

{% endhighlight %}



![step2.1](public/step2.1.png "Step 2.1")

One thing you might note is that the size of circles looks different on your phone. That is because different phones have different resolutions so feel free to mess around with the values. However, a proper implementation would calculate the pixels using a dpi to pixel conversion. We'll skip that for now.

### Step 3

Now lets add some interactivity to our view. First, we need to keep track of the x,y coordinates of the small circles so we'll create a list of x,y pairs to hold that. We'll also optimize a little since right now we're calculating x and y on every draw. Nothing in terms of drawing should change.

{% highlight java linenos %}	

		const int NUM_BUBBLES = 5;
		int radius = 60;
		List <Pair> positions = new List <Pair> ();
		 void initPositions(){

			if (positions.Count == 0) {

				int spacing = Width / NUM_BUBBLES;
				int shift = spacing / 2;
				int bottomMargin = 10;

				for (int i = 0; i < NUM_BUBBLES; i++) {
					int x = i * spacing + shift;
					int y = Height - radius * 2 - bottomMargin;
					positions.Add (new Pair (x, y));
				}
			}
		}

		 void drawSmallCircles(Canvas canvas){

			initPositions ();
			var paintCircle = new Paint (){ Color = Color.White};
			for (int i = 0; i < NUM_BUBBLES; i++) {
				int x = (int)positions [i].First;
				int y = (int)positions [i].Second;
				canvas.DrawCircle (x, y, radius, paintCircle);
			}
		}
	

{% endhighlight %}



Next we'll override the ontouchevent function and also check if the event is inside any of the given circles. The is InsideCircle function will basically return the index of the circle that was tapped/touched.


{% highlight java linenos %}	

		public override bool OnTouchEvent(MotionEvent e) {

			int indexHit = isInsideCircle (e.GetX (), e.GetY ());
			if (indexHit > -1) {
				Toast.MakeText (mContext, "Got index" + indexHit, ToastLength.Long).Show ();
			}

			return false;
		}

		int isInsideCircle(float x, float y){

			for (int i = 0; i < positions.Count; i++) {

				int centerX = (int)positions [i].First;
				int centerY = (int)positions [i].Second;

				if (System.Math.Pow (x - centerX, 2) + System.Math.Pow (y - centerY, 2) < System.Math.Pow (radius, 2)) {
					return i;	
				}
			}

			return -1;
		}

{% endhighlight %}


Tap the small circles and you should see the toast show up with the correct index.


### Step 4

Now lets add some cool animations to this view. Remember we want the small circle to move to the center and we also want it to scale up. We'll start by adding some ValueAnimators and also the values that they will animate.



{% highlight java linenos %}	

		//Important properties for the large bubble
		int activeIndex = -1;
		float activeX = 0;
		float activeY = 0;
		float activeRadius = 60;
		ValueAnimator animatorX;
		ValueAnimator animatorY;
		ValueAnimator animatorRadius;


{% endhighlight %}


By active, I just mean the big circle. Now let's initialize them by putting the following in the init function:



{% highlight java linenos %}	

		animatorX = new ValueAnimator ();
		animatorY = new ValueAnimator ();
		animatorRadius = new ValueAnimator ();
		animatorX.SetDuration(1000);
		animatorY.SetDuration (1000);
		animatorRadius.SetDuration (1000);
		animatorX.SetInterpolator (new DecelerateInterpolator());
		animatorY.SetInterpolator (new BounceInterpolator());


		//These are called everytime an update happens in the animator.
		animatorRadius.SetIntValues (new [] { radius, radius_big });
		animatorRadius.Update += (sender, e) => {
			activeRadius = (float) e.Animation.AnimatedValue;
			Invalidate();
		};

		animatorX.Update += (sender, e) => {
			activeX = (float) e.Animation.AnimatedValue;
			Invalidate();
		};
		animatorY.Update += (sender, e) => {
			activeY = (float) e.Animation.AnimatedValue;
			Invalidate();
		};


{% endhighlight %}


This is just saying that each animation will last 1 second and we added a fancy interpolator to make the animation look cool (feel free to change it up). We also subscribe to the update event to get the latest value and then we call invalidate which basically clear the canvas and call the onDraw method again.


Now lets add the start and end values for each animator and actually start the animations. Modify the onTouchEvent, drawBigCircle and drawLittleCircle function as follows:

{% highlight java linenos %}	

	public override bool OnTouchEvent(MotionEvent e) {

			float centerScreenX = Width / 2.0f;
			float centerScreenY = Height / 2.0f;
			activeIndex = isInsideCircle (e.GetX (), e.GetY ());
			if (activeIndex > -1) {
				Toast.MakeText (mContext, "Got index" + activeIndex, ToastLength.Long).Show ();
				animatorX.SetFloatValues (new [] {(float)positions[activeIndex].First, centerScreenX});
				animatorY.SetFloatValues (new [] {(float)positions[activeIndex].Second, centerScreenY});
				animatorX.Start ();
				animatorY.Start ();
				animatorRadius.Start ();
			}

			return false;
		}


	int radius_big = 180;
	void drawBigCircle(Canvas canvas){
			if (activeIndex > -1) {
				var paintCircle = new Paint (){ Color = Color.White };
				canvas.DrawCircle (activeX, activeY, activeRadius, paintCircle);
			}
	}

	void drawSmallCircles(Canvas canvas){

		initPositions ();
		var paintCircle = new Paint (){ Color = Color.White};
		for (int i = 0; i < NUM_BUBBLES; i++) {
			if (i == activeIndex) {
				continue;
			}
			int x = (int)positions [i].First;
			int y = (int)positions [i].Second;
			canvas.DrawCircle (x, y, radius, paintCircle);
		}
	}

{% endhighlight %}


In the ontouchevent function, now set the activeindex and all the starting and end values for the interpolator. In the drawSmallCircles function, we skip the index if its the big circle and in the drawBigCircle function we only draw something has actually been tapped (i.e. the index is greated than -1)

Hit play and should see the small circle move to the center and grow when tapped.


### Step 5

Lets finish this off by adding some colors and text to our circles. Add the following colors array (and again you can choose to do different colors).

{% highlight java linenos %}	
	Color []colors = new []{Color.Red, Color.LightBlue, Color.Green, Color.Yellow, Color.Orange};
{% endhighlight %}


And as you might've guessed, we just replace the white color in paint with the colors[index]. Replace paintCircle with the following in the small circle function and move it inside the for loop


{% highlight java linenos %}	
	var paintCircle = new Paint (){ Color = colors[i]};
{% endhighlight %}



And with this in the big circle function:

{% highlight java linenos %}	
	var paintCircle = new Paint (){ Color = colors[activeIndex]};
{% endhighlight %}


Fire up the app and you should see something like this:

![step5](public/step5.png "Step 5")


Lets add an array to hold some names to display in the circles.

{% highlight java linenos %}	
		public string [] names {get;set;}
{% endhighlight %}



And then in your MainActivity.cs add the following names (or anything names you want) but make sure you have at least 5.


{% highlight java linenos %}	
	var awesomeview = FindViewById<AwesomeView> (Resource.Id.awesomeview_main);
	awesomeview.names = new []{"Bob", "John", "Paul", "Wasi", "Mark"};
{% endhighlight %}


Finally lets show the names on the big circle. Modify the big circle function as follows:


{% highlight java linenos %}	

	void drawBigCircle(Canvas canvas){
		if (activeIndex > -1) {
			var paintCircle = new Paint (){ Color = colors[activeIndex]};
			canvas.DrawCircle (activeX, activeY, activeRadius, paintCircle);

			var paintText = new Paint(){Color = Color.Black};
			//  the screen's density scale
			var scale = mContext.Resources.DisplayMetrics.Density;
			// Convert the dps to pixels, based on density scale
			var textSizePx = (int) (20f * scale);
			var name = names [activeIndex];
			paintText.TextSize = textSizePx;
			paintText.TextAlign = Paint.Align.Center;
			canvas.DrawText (name, activeX, activeY + radius/2, paintText);

		}
	}

{% endhighlight %}


Lets also show the first letter of each name on the little circle.


{% highlight java linenos %}	

	void drawSmallCircles(Canvas canvas){

		initPositions ();

		var paintText = new Paint (){ Color = Color.Black };
		// Get the screen's density scale
		var scale = mContext.Resources.DisplayMetrics.Density;
		// Convert the dps to pixels, based on density scale
		var textSizePx = (int) (30f * scale);
		paintText.TextSize = textSizePx;
		paintText.TextAlign = Paint.Align.Center;

		for (int i = 0; i < NUM_BUBBLES; i++) {
			if (i == activeIndex) {
				continue;
			}

			var paintCircle = new Paint (){ Color = colors[i]};
			int x = (int)positions [i].First;
			int y = (int)positions [i].Second;
			canvas.DrawCircle (x, y, radius, paintCircle);
			canvas.DrawText (""+names [i][0], x, y + radius/2, paintText);
		}
	}


{% endhighlight %}


### Done

And we're done! This just shows you a taste of what you can accomplish with custom views.


