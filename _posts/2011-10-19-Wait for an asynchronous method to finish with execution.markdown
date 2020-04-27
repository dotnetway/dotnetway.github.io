---
layout: post
title:  "Wait for an asynchronous method to finish execution."
date:   2011-10-19 18:41:31 +0100
categories: c#, TLP
comments: false
---

I have had the case of having to call a method that runs asynchronously but I need to wait for it to finish before chasing because it needed a value that was established in the latter method. A colleague told me that I could use TPL (Task Parallel Library) to simplify things and specifically with ContinueWith of the Task class. An example would be the following:

{% highlight c# %}
Task.Factory.StartNew(GetControlType).ContinueWith(t =>
{
      openElevatorDoors();
});
{% endhighlight %}

Here we begin a new task with the GetControlType method that is asynchronous and that sets me a variable that uses openElevatorDoors. With ContinueWith, which asks us for an Action we specify with a lamba the method that we want to continue executing when the asynchronous method has finished executing, in our case the one that opens the elevator doors which is the one that uses one of the variables that it assigns the asynchronous method
