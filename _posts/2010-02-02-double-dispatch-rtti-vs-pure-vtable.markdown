---
layout: post
title: Double Dispatch - RTTI vs. Pure Vtable
tags:
- c++
- General
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  wp_jd_target: http://www.somethingorothersoft.com/2010/02/02/double-dispatch-rtti-vs-pure-vtable/
  wp_jd_bitly: http://bit.ly/aaRanU
---
As Jeremy Clarkson would say, I have been inundated with [a request](http://www.somethingorothersoft.com/2010/02/01/double-dispatch-without-rtti/comment-page-1/#comment-250) to performance test a `dynamic_cast` DD mechanism against the [pure vtable](http://www.somethingorothersoft.com/2010/02/01/double-dispatch-without-rtti/) one. The results surprised me, both in Debug and Release modes. So here is the `dynamic_cast` implementation:

<div class="foo">
[code lang="cpp"]
void c_rtti::foobar(a_rtti &amp; a2) {
   c_rtti* c_ = dynamic_cast&lt;c_rtti*&gt;(&amp;a2);
   if (c_) {
      ::foobar(*this,*c_);
      return;
   }
   b_rtti* b_ = dynamic_cast&lt;b_rtti*&gt;(&amp;a2);
   if (b_) {
      ::foobar(*b_, *this);
   }
}
[/code]
</div>

I have used the most non-trivial case where the above function is called with `a2` parameter pointing to an instance of `b_rtti`, thus triggerring two dynamic casts. I used the same technique on the vtable implementation for consistency, although it doesn't matter as the sequence of execution is the same for any combination of objects. The test ran for 4 million iterations.

Here is the test code:

<div class="foo">
[code lang="cpp"]
        a&amp; a_ = c();
        b bInstance;
        a&amp; b_ = bInstance;
        a_.foobar(b_);
[/code]
</div>

Just to show what actually happens in both implementations, here are the sequence diagrams.
#### vtable:

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/vtable1.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/vtable1.png" alt="" title="vtable" width="346" height="230" class="alignnone size-full wp-image-238" /></a>

#### dynamic_cast:

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/rtti.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/rtti.png" alt="" title="dynamic_cast Sequnce Diagram" width="254" height="304" class="alignnone size-full wp-image-236" /></a>

### The results
In debug mode, RTTI implementation is about 20% faster:

    No RTTI - 1685 milleseconds.
    With RTTI - 1295 milleseconds.(23.15% faster)

So this is pretty insignificant in a real life application and I doubt it would come in the profiler. By my calculation there is about 100 clock cycles (on a 1Ghz CPU) difference between the two per call.

In Release mode things get interesting:

    No RTTI - 62 milleseconds.
    With RTTI - 780 milleseconds.(-1158.06% faster)

The pure vtable version is ten times faster!!! Of course this again means very little in the real world. It's worth noting that the RTTI verson is twice faster in Release mode. What is also interesting is that in Debug mode vtable is slower by 100 clock cycles, in release it's faster by 200.

To summarise. The real life value of this performance analysis is zero. If you are Google and you had 100 PhDs design the program and another 100 wrote the library routines for it, doing this optimization will improve the performance by about 0.000001%. In any other case, this is textbook case of premature optimisation.
