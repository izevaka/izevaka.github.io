---
layout: post
title: Double dispatch without RTTI
tags:
- c++
- Development
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  wp_jd_target: http://www.somethingorothersoft.com/2010/02/01/double-dispatch-without-rtti/
  wp_jd_bitly: http://bit.ly/dqEZAJ
---
I liked my [StackOverflow answer](http://stackoverflow.com/questions/2159496/c-problem-with-function-overloading/2159524#2159524) so much that I decided to make it into a blog post. The question was how to implement a double dispatch mechanism for subclasses of one base class without casting. After dismissing the question as stupid, I spent a bit of time trying to come up with a solution that didn't rely on `dynamic_cast`. The solution is pretty pointless as it's pretty confusing and might be slower than RTTI solution due to an extra function call. It was largely inspired by wikipedia article on double dispatch found [here](http://en.wikipedia.org/wiki/Double_dispatch).

So the trick to get this working as pointed out in the article was to call the function on the pointer or reference of the parameter to the original function, thus cauising a second dispatch.
<div class="foo">
[code lang="cpp"]
class A {
  virtual void PublicFunction(B&amp; b) {
    b.PerformFunction(*this);
  }
}
[/code]
</div>

The problem with doing this sort of thing with subclasses of the same base is that we need to allow the possibility of the base class being called with one of its subclasses as a parameter. Luckily we can use forward declarations to define the functions and implement them later on.

<div class="foo">
[code lang="cpp"]
class b;
class c;
class a {
protected:
    virtual void doFoobar(a&amp; a2); //do stuff here 
    virtual void doFoobar(b&amp; b2); //delegate to b
    virtual void doFoobar(c&amp; c2); //delegate to c
public:
    virtual void foobar(a&amp; a2) {
        a2.doFoobar(*this);
    }
    friend class b;
    friend class c;
};
class b : public a {
protected:
    virtual void doFoobar(a&amp; a2); //do stuff here 
    virtual void doFoobar(b&amp; b2); //do stuff here
    virtual void doFoobar(c&amp; c2); //delegate to c
public:
    virtual void foobar(a&amp; a2) {
        a2.doFoobar(*this);
    }
    friend class a;
};
class c : public b {
protected:
    virtual void doFoobar(a&amp; a2); //do stuff here 
    virtual void doFoobar(b&amp; b2); //do stuff here
    virtual void doFoobar(c&amp; c2); //delegate to c
public:
    virtual void foobar(a&amp; a2) {
        a2.doFoobar(*this);
    }
    friend class a;
    friend class b;
};
//class a
void a::doFoobar(a&amp; a2)  {
    printf(&quot;Doing a&lt;-&gt;a\n&quot;);
}
void a::doFoobar(b&amp; b2)  {
    b2.doFoobar(*this);
}
void a::doFoobar(c&amp; c2)  {
    c2.doFoobar(*this);
}
//class b
void b::doFoobar(a&amp; a2)  {
    printf(&quot;Doing b&lt;-&gt;a\n&quot;);
}
void b::doFoobar(b&amp; b2)  {
    printf(&quot;Doing b&lt;-&gt;b\n&quot;);
}
void b::doFoobar(c&amp; c2)  {
    c2.doFoobar(*this);
}
//class c
void c::doFoobar(a&amp; a2)  {
    printf(&quot;Doing c&lt;-&gt;a\n&quot;);
}
void c::doFoobar(b&amp; b2)  {
    printf(&quot;Doing c&lt;-&gt;b\n&quot;);
}
void c::doFoobar(c&amp; c2)  {
    printf(&quot;Doing c&lt;-&gt;c\n&quot;);
}
[/code]
</div>
The code is far from an ideal double dispatch solution as it relies on the forward declaration trick to resolve the circular dependency between the subclasses. This means that all the classes that need to have this functionality must be known at compile-time (of the base class). This means the base class cannot be made into a library component.

To explain all the friend declarations. Considering the fact that all the classes `a`, `b` and `c` are tightly coupled as is, I added friend declarations so that the actual worker function is private and cannot be called from the outside.