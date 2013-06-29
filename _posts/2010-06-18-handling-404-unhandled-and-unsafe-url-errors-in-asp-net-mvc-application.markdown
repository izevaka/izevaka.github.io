---
layout: post
title: Handling 404, unhandled and unsafe URL errors in ASP.NET MVC application
tags:
- asp.net-mvc
- csharp
- Development
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  wp_jd_bitly: http://bit.ly/aA1Kad
  wp_jd_target: http://www.somethingorothersoft.com/2010/06/18/handling-404-unhandled-and-unsafe-url-errors-in-asp-net-mvc-application/
---
Spent a bit of time recently to try and get my MVC application handle 404 errors exactly as I want. The solution that I ended up choosing is based on this excellent [SO answer][1].

The advantage of rendering the Error view by calling `ErrorController.Execute`, as opposed to making an entry in web.config, is that you get to preserve the URL without a browser redirect.

If you think about which scenarios warrant a 404 response a more involved strategy is justifiable:

1. Non-supported controller name - captured by `{controller}/{action}/{parameter}` rule.
2. Supported controller but bad action - captured by `Controller.HandleUnknownAction`.
3. Non-supported URL format - captured by the catch-all rule.

Using `ErrorController.Execute` allows to preserve the URL for all of those scenarios. Now, to trap all unhandled exceptions in order to not show a Yellow Screen of Death, you would need to do some error handling in `Application.Error` event handler. Something similar is described in [this answer][2].

There is a catch, however. If the anti-XSS features kick in and disallow a URL containing, say, angle brackets, executing error controller from global exception handler will throw an exception resulting in a YSOD.

The best solution i found was to catch the exception when doing `Execute` and do a browser redirect then. This prevents the runtime from creating a YSOD due to an unhandled exception in the exception event.

<div>
{% highlight csharp %}
IController errorController = ControllerBuilder.Current.GetControllerFactory().CreateController(null, "error");
try {
	errorController.Execute(new RequestContext(
			contextBase, errorRoute));
} catch(Exception) {
	//If we are here it means that the URL is unsafe and the only way to handle it gracefully is to redirect.
	Response.Redirect("~/Error/BadRequest"); //Controller/View pair that returns 400 - Bad Request
}

{% endhighlight %}
</div>

It's probably not the best idea to handle 400 error as 404, hence we redirect to a "BadRequest" action in the `ErrorController`.

  [1]: http://stackoverflow.com/questions/619895/how-can-i-properly-handle-404s-in-asp-net-mvc/2577095#2577095
  [2]: http://stackoverflow.com/questions/619895/how-can-i-properly-handle-404s-in-asp-net-mvc/620559#620559
