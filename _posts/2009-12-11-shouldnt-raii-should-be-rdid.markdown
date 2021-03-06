---
layout: post
title: Shouldn't RAII should be RDID???
tags:
- c++
- design-patterns
- Development
status: publish
type: post
published: true
meta:
  _edit_last: '1'
  _jd_tweet_this: 'yes'
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
---
I will openly plead ignorance for design patterns. I know this is really not the way to be and I have <em>Design Patterns</em> and <em>Code Complete</em> on my "to read" list. It's only that I trawl through StackOverflow, I get to hear a lot of buzz about patterns and idioms. Some people get really obsessive about identifying the patterns and making sure they use the right ones. But that's not really the point of this post.

Most of the time I realize that a lot of those pattens are something that I have been doing for years without thinking. In fact this inspires me to go through "the list" of all the patterns and do a series of blog posts - <strong>"Design Patterns - What you have coded for years is actually an idiom - [insert design pattern here]"</strong>.

So, I only just recently found out that the practice of using smart pointers is officially called RAII - Resource Allocation Is Initialization. The  <a href="http://en.wikipedia.org/wiki/Resource_Acquisition_Is_Initialization">Wikipedia article</a> bangs on about ojects being allocated on the stack and exception safety, but in my opinion those considerations are not as significant as the magic of destruction.

The only way this works in C++ is due to deterministic destructors. The magic happens upon destruction, not upon initialization. Any garbage collected language can have Allocation on Initialization, but they can't guarantee implicit destruction when going out of scope like C++ does.

I also think that that object scope (the fact that it should be on the stack) is somewhat peripheral again. The destructor will get called whenever you explicitly delete the object as well. Whilst this is not strictly automagic destruction, it can be - for example when using collections that are aware of element removal semantics. For example a collection might either call a Release method on a ref counted object or call operator delete on plain old pointer.

So this brings me back to the title of hte post - shouldn't Resource Allocation Is Initialization be Resource Deallocation Is Destruction? That's where the magic happens, right?
