---
layout: post
title: Keeping your sanity whilst developing for Windows Phone 7
tags:
- Development
- silverlight
- windows-phone-7
- wpf
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  jd_twitter: ! 'Blogged - #title# - #url#. #wp7dev #wp7'
  wp_jd_target: http://www.somethingorothersoft.com/2010/04/28/keeping-your-sanity-whilst-developing-for-windows-phone-7/
  wp_jd_bitly: http://bit.ly/btgB8Y
---
<a href="{{ site.url }}/images/2010/04/frustration-300x299.jpg"><img src="{{ site.url }}/images/2010/04/frustration-300x299.jpg" alt="" title="frustration-300x299" width="300" height="299" class="alignnone size-full wp-image-380" /></a>

Please note that this post is written in April 2010, when the WP7 SDK is only one month old. Most of the issues described here will likely not be a problem in later releases. The defining characteristic of such pre-release SDK is that when you are doing the right thing, everything is OK. When you screw up, it just goes KABOOM! without much explanation.

I spent a few hours of trying to work out why I am getting these run-time errors and so I've put together a list of the most painful ones, enjoy.

## Using `{x:Static ...}` Syntax
**Problem:** `{x:Static ...}` is not supported in Silverlight.

If you to do something like this:

<div>{% highlight xml %}
<DiscreteObjectKeyFrame  KeyTime="00:00:00" Value="{x:Static Visibility.Collapsed}">
{% endhighlight %}</div>

You will get an obtuse error at runtime:

<pre>
A first chance exception of type 'System.Windows.Markup.XamlParseException' occurred in System.Windows.dll
Additional information: 2024 An error has occurred. [Line: 114 Position: 61]
</pre>

Get used to seeing that error. It'll happen every time you screw up the XAML. Sometimes it's more helpful than other times and you get the line number of the offending XAML. The line number, along with the exception text will be available in the Output window:

<a href="{{ site.url }}/images/2010/04/crash1.png"><img src="{{ site.url }}/images/2010/04/crash1-1024x611.png" alt="" title="crash1" width="450" height="268" class="alignnone size-large wp-image-369" /></a>

If you are like me and rarely look at the error window, perhaps you should start now. This particular error doesn't raise a warning during compilation, but does come up as an error when the file is open in the editor:

<a href="{{ site.url }}/images/2010/04/xaml_error.png"><img src="{{ site.url }}/images/2010/04/xaml_error.png" alt="" title="xaml_error" width="641" height="262" class="alignnone size-full wp-image-370" /></a>

**Solution:** Don't use `x:Static` but instead an instance of a type in XAML like so:
<div>{% highlight xml %}
	<DiscreteObjectKeyFrame  KeyTime="00:00:00">
		<DiscreteObjectKeyFrame.Value>
			<Visibility>Collapsed</Visibility>
		</DiscreteObjectKeyFrame.Value>
	</DiscreteObjectKeyFrame>
{% endhighlight %}</div>

## Misspelling Animation Element or Property Name
**Problem:** Misspelling Animation Element or Property Name

In the below code `Storyboard.TargetName` is invalid. The project compiles and there are no errors in the error window.

<div>{% highlight xml %}
	<ObjectAnimationUsingKeyFrames Storyboard.TargetName="SearchBoxMisspelled" Storyboard.TargetProperty="Visibility">
		<!--animation key frames-->
	</ObjectAnimationUsingKeyFrames>
{% endhighlight %}</div>

At run-time, you get this error message:

<pre>
A first chance exception of type 'System.InvalidOperationException' occurred in System.Windows.dll
An unhandled exception of type 'System.InvalidOperationException' occurred in System.Windows.dll
Additional information: 2213 An error has occurred.
</pre>

The call stack only tells you that this is something to do with the animation:

<pre>
System.Windows.dll!MS.Internal.XcpImports.MethodEx(System.IntPtr ptr = 146517008, string name = "Begin", MS.Internal.CValue[] cvData = null) + 0x9d bytes
System.Windows.dll!MS.Internal.XcpImports.MethodEx(System.Windows.DependencyObject obj = {System.Windows.Media.Animation.Storyboard}, string name = "Begin") + 0x6 bytes	
System.Windows.dll!MS.Internal.XcpImports.Storyboard_Begin(System.Windows.Media.Animation.Storyboard storyboard = {System.Windows.Media.Animation.Storyboard})
System.Windows.dll!System.Windows.Media.Animation.Storyboard.Begin() + 0x6 bytes
</pre>

**Solution:** To resolve this, check your animation element and property names. Unfortunately I haven't found a way to get at the offending animation frame from the call stack. Would be happy to find out if there is a way. 

## References' Dependencies

**Problem:** "Object reference is not set to an instance of an object" error when debugging and crashes when running without debugger.

After adding a reference library you might start getting this error when you debug the project:

<a href="{{ site.url }}/images/2010/04/error_debugging.png"><img src="{{ site.url }}/images/2010/04/error_debugging.png" alt="" title="error_debugging" width="391" height="171" class="alignnone size-full wp-image-371" /></a>

This is caused, as far as I can tell, by your references' dependencies not being copied to the device. After hours of playing around with this it's apparent to me that this is an intermittent issue with Visual Studio. It is supposed to work out what all the references' dependencies are, copy them to the output folder and add them to the manifest file (`$(OutputDir)\AppManifest.xaml`).

Sometimes that doesn't happen and the app gets deployed without those dependencies. It will still work, up until the point where a class is referenced from a dependant assembly and that assembly cannot be loaded. I expect this problem to go away in later releases of WP7 SDK.

The error will look something like this:

<a href="{{ site.url }}/images/2010/04/runtime_error.png"><img src="{{ site.url }}/images/2010/04/runtime_error.png" alt="" title="runtime_error" width="396" height="772" class="alignnone size-full wp-image-372" /></a>

<pre>
2024 An error has occurred[Line: <line> Position: <position>]
at
System.Windows.Application.LoadComponent
...Rest of the callstack.
</pre>

**Solution:** Add all the assembiles that your references depend upon as references to the project.
**Edit:** This error kept happening to me until my coworker pointed out the solution, which led to the "Doh moment of the week."
**Solution2:** When debugging the project **select the project in the solution pane and press F5**. Yeah, that simple.




