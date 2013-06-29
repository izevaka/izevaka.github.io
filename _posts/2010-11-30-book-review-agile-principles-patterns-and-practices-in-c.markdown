---
layout: post
title: ! 'Book review: Agile Principles, Patterns, and Practices in C#'
tags:
- bookshelf
- csharp
- Development
status: publish
type: post
published: true
meta:
  _wp_jd_target: ''
  _wp_jd_url: ''
  _wp_jd_yourls: ''
  _wp_jd_wp: ''
  _wp_jd_bitly: ''
  _wp_jd_clig: ''
  _jd_twitter: ''
  _jd_tweet_this: ''
  _jd_post_meta_fixed: 'true'
  _jd_wp_twitter: ''
  _edit_last: '1'
  _wp_old_slug: ''
---
<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/11/agile-c.jpg"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/11/agile-c.jpg" alt="" title="agile c#" width="372" height="500" class="alignnone size-full wp-image-477" /></a>

Bob C. Martin's *Agile Principles, Patterns, and Practices in C#" has to be the most readable and enjoyable computer text ever written. Having created production object orientated code for years, seeing ways to do OO properly is eye opening. Reflecting back, I realise that a lot of that "OO" code is actually procedural code written using OO tools.

A lot of the patterns described in the book are really there to enforce one of the guiding principles - Single Responsibility Principle (SRP). By looking at designing your system through the SRP lens, a lot of things become clearer.

In OO classes are first class citizens, and as a C++ developer, having one method classes seemed like a waste. Sounds silly, but I thought that a class should have lots of methods in order for it to be useful. Having now opened the door to designing loosely coupled system with lots of coherent classes, there is no going back.

I think this should be one of the main tenets tought in OO classes - THERE IS NOTHING WRONG WITH SMALL CLASSES. "Saving" classes by bundling lots of functionality into them has to be eradicated early in OO education.

Back to the book. As I mentioned before, having C++ background made this book even more fun to read as I could tell that the code was originally written in C++. Most of the code is .NET 1.1 and that might be the only minor criticism -  sample code does not have most of the new features of even .NET 2.0 (e.g. generics). I would also like to see what would happen if this book was rewritten to take into account functional aspects of .NET 3.5 - lambdas, LINQ libraries, Expressions.

Another valuable insight that I found particularly useful is working through the design to figure out if a pattern application is necessary. A lot of the time design patterns can become "golden hammers" of software teams, it's good to know when to stop and evaluate if a pattern is needed.

Overall, this is a must read for any developer. C++, Java or C# people will all get something out of it.
