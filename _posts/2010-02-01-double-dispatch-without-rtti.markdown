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
{% highlight cpp %}
class A {
  virtual void PublicFunction(B& b) {
    b.PerformFunction(*this);
  }
}
{% endhighlight %}
</div>

The problem with doing this sort of thing with subclasses of the same base is that we need to allow the possibility of the base class being called with one of its subclasses as a parameter. Luckily we can use forward declarations to define the functions and implement them later on.

<div class="foo">
{% highlight cpp %}
class b;
class c;
class a {
protected:
    virtual void doFoobar(a& a2); //do stuff here 
    virtual void doFoobar(b& b2); //delegate to b
    virtual void doFoobar(c& c2); //delegate to c
public:
    virtual void foobar(a& a2) {
        a2.doFoobar(*this);
    }
    friend class b;
    friend class c;
};
class b : public a {
protected:
    virtual void doFoobar(a& a2); //do stuff here 
    virtual void doFoobar(b& b2); //do stuff here
    virtual void doFoobar(c& c2); //delegate to c
public:
    virtual void foobar(a& a2) {
        a2.doFoobar(*this);
    }
    friend class a;
};
class c : public b {
protected:
    virtual void doFoobar(a& a2); //do stuff here 
    virtual void doFoobar(b& b2); //do stuff here
    virtual void doFoobar(c& c2); //delegate to c
public:
    virtual void foobar(a& a2) {
        a2.doFoobar(*this);
    }
    friend class a;
    friend class b;
};
//class a
void a::doFoobar(a& a2)  {
    printf("Doing a<->a\n");
}
void a::doFoobar(b& b2)  {
    b2.doFoobar(*this);
}
void a::doFoobar(c& c2)  {
    c2.doFoobar(*this);
}
//class b
void b::doFoobar(a& a2)  {
    printf("Doing b<->a\n");
}
void b::doFoobar(b& b2)  {
    printf("Doing b<->b\n");
}
void b::doFoobar(c& c2)  {
    c2.doFoobar(*this);
}
//class c
void c::doFoobar(a& a2)  {
    printf("Doing c<->a\n");
}
void c::doFoobar(b& b2)  {
    printf("Doing c<->b\n");
}
void c::doFoobar(c& c2)  {
    printf("Doing c<->c\n");
}
{% endhighlight %}
</div>
The code is far from an ideal double dispatch solution as it relies on the forward declaration trick to resolve the circular dependency between the subclasses. This means that all the classes that need to have this functionality must be known at compile-time (of the base class). This means the base class cannot be made into a library component.

To explain all the friend declarations. Considering the fact that all the classes `a`, `b` and `c` are tightly coupled as is, I added friend declarations so that the actual worker function is private and cannot be called from the outside.
