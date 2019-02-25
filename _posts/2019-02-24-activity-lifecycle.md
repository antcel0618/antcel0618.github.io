---
layout: post
title:  "Lifecycles and you, 2019 edition"
date:   2019-02-24 16:00:00 -0500
categories: android
---

It's 2019, Android Architecture Component will keep the state of our activities and no need to know anything about lifcycle and what it does, right? Wrong 😩. But its suppose to take care of all of the problems that I have with lifecycle, correct? Why would the Android developers release something that will take the care of the problems that so many of us faced everyday and even brought some of us to our knees during interviews.

Let's take a look back at what we're suppose to know about activity lifecycles straight from the Android Developer site. The six core lifecycle callbacks are the `onCreate`, `onStart`, `onResume`, `onPause`, `onStop` and `onDestroy`. They are what I call complements of each other and there's two sets: one set that creates and one that destroys. 

Creation
* `onCreate`
* `onStart`
* `onResume`

Destroy
* `onPause`
* `onStop`
* `onDestroy`

Complements
* `onCreate` = `onDestroy`
* `onStart` = `onStop`
* `onResume` = `onPause`

The `onCreate` is called when the activity is created. Mostly we only want to do minimal work like getting the data from the intents and setting the layout that is for the layout.

The `onStart` is when the activity is visible to the user. This doesn't mean that the activity is active, this is very important since with the advent of foldable phones 🤪, there can be multiple activities that are visible but only one can be active to the user.

`onResume` will be the one tell the activity that the activity is active and is ready for the user to interact with. 

Now the complements are doing exactly the opposite of the Creation state. So the `onPause` will be called when the activity is no longer the active activity, but it is still visible to the user. `onStop` will be called when the acitivity is no longer viewable on the screen. 

Another set of compliments that are not on here is the `onSaveInstanceState` and `onRestoreInstanceState`. What are these methods and do we still need them if I start using the Architecture Components? The short answer is yes. These methods are called by the system to notify the developer to save any data that is needed to recreate the activity state. But wait, isn't the Architecture Component, namely the View Model be able to re-create our state without using the `onSaveInstanceState`. 

The answer to that is no. If the system deems the it is necessary to kill the application, the corresponding View Models will be also killed. When user restarts the application, the system will try to recreate the same activity where the user left off. This will trigger the same lifecycle and if there's nothing saved on that Bundle then it'll be a fresh session on that activity.

Another set of pitfalls is when finish is called during the activity lifecycle.
{% highlight kotlin %}
public class FragmentActivity extends ComponentActivity implements
        ActivityCompat.OnRequestPermissionsResultCallback,
        ActivityCompat.RequestPermissionsRequestCodeValidator {
    ...
    boolean mCreated;
    boolean mResumed;
    boolean mStopped = true;
    ...
{% endhighlight %}
Taking a look at the `FragmentActivity` implementation there's flags that keep tracks if it has been called. Looking back at the complements, if finish is called on the *onCreate* then only its complement will be called *onDestroy*.

Yikes, that's something that can be a headache. There's a lot of pitfalls that we can fallthrough when dealing with Activity lifecycle, but one rule that I have is that I remember the complement rule. If I would subscribe on the creation complement and unsubscribe on the destroy complement. For example, subscribing to data source changes during `onStart` and unsubsribing during `onStop`.

I know there will be another day that lifecycle will get me stuck on figuring out what I happening, until that day, let's keep learning.



