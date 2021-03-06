---
layout: post
title: Type-safe Dynamic Mixins
tags:
- Development
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  wp_jd_target: http://www.somethingorothersoft.com/2010/08/12/type-safe-dynamic-mixins/
  wp_jd_bitly: http://bit.ly/a21f3S
  _wp_old_slug: ''
---
This is another one of those posts that arose from an [epic StackOverflow answer](http://stackoverflow.com/questions/3411294/is-it-possible-in-c-to-make-a-factory-that-merges-interfaces/3414970#3414970).

The problem to be solved was creating a factory that would at runtime merge two implementations of different interfaces into one object. I have implemented this using Castle Dynamic Proxy (which is totally awesome, that's why all mocking/testing libraries use it).

In this post I want to concentrate more on the performance aspects of the solution and provide source code for your own perusal. There are two implementations of `CreateMixin` - generic and non-generic versions. They are completely different in how they mix interfaces.

###`CreateMixin`

The non-generic version simply leverages Castle Dynamic Proxy mixins to bundle objects into one instance. The downside is that all of those interfaces are implemented explicitly and the returned instance must be cast to each individual interface to be used. Here is the implementation and the unit test:

<div>
{% highlight csharp %}
public static object CreateMixin(params object[] instances) {

	ProxyGenerator generator = new ProxyGenerator();
	ProxyGenerationOptions options = new ProxyGenerationOptions();

	instances.ToList().ForEach(obj => options.AddMixinInstance(obj));

	return generator.CreateClassProxy<object>(options);
}
[TestMethod]
public void CreatedMixinShouldntThrow() {
	ICat obj1 = new Cat();
	IDog obj2 = new Dog();

	var actual = Factory.CreateMixin(obj1, obj2);
	((IDog)actual).Bark();
	var weight = ((IDog)actual).Weight;
	((ICat)actual).Meow();
	var age = ((ICat)actual).Age;
}
{% endhighlight %}
</div>

###`CreateMixin<T>`
While I was playing around with it I thought I'd create another factory method that could return a strongly typed version of the super object. This turned out to be tricky as this was a bit like having cake and eating it too.

The initial implementation was fairly slow in comparison to the native class implementation (i.e. fully implemented at compile time) - about a thousand times slower. The Castle DP mixin performance on the other hand was pretty good. The breakdown was something like this:

* Native ~10ms
* Mixin ~300ms
* Generic mixin ~10000ms

The culprit, as expected, was this bit of code. It uses reflection and is extremely slow compared to native or even Castle mixins. 
<div>
{% highlight csharp %}
    public void Intercept(IInvocation invocation) {
        invocation.ReturnValue = invocation.Method.Invoke(m_Instance, invocation.Arguments);
    }
{% endhighlight %}
</div>

After digging around Dynamic proxy code I have found `IChangeProxyTarget` interface that allows to change the target of invocation in the interceptor. Since we want to simply redirect a call from the combined interface to the implementation, all we need to do is point the invocation towards the actual instance. 

<div>
{% highlight csharp %}

public void Intercept(IInvocation invocation) {
	IChangeProxyTarget changeTarget = (IChangeProxyTarget)invocation;
	changeTarget.ChangeInvocationTarget(m_Target);
	invocation.Proceed();
}
{% endhighlight %}
</div>

The only icky bit about this is the fact that we need to call `CreateInterfaceProxyWithTargetInterface` in order to be able to switch target of invocation in the interceptor method. What this implies, however is that we need to provide an instance of the combined interface (which never gets used anyway), so we actually create another proxy and pass that to the proxy creation method. A bit recursive, but it gets the job done.

And here is the factory method with test: 

<div>
{% highlight csharp %}
public static TMixin CreateMixin<TMixin>(params object[] instances)
where TMixin : class {
	if(instances == null || instances.Length == 0 || instances.Any(o => o == null))
		throw new ArgumentException("All mixins should be non-null", "instances");

	ProxyGenerator generator = new ProxyGenerator();
	ProxyGenerationOptions options = new ProxyGenerationOptions();
	options.Selector = new MixinSelector();
	var dummy = generator.CreateInterfaceProxyWithoutTarget<TMixin>();

	return generator.CreateInterfaceProxyWithTargetInterface<TMixin>(dummy, options, instances.Select(o => new MixinInterceptor(o)).ToArray());
}
[TestMethod]
public void CreatedGeneric3MixinShouldntThrow() {
	ICat obj1 = new Cat();
	IDog obj2 = new Dog();
	IMouse obj3 = new Mouse();
	var actual = Factory.CreateMixin<ICatDogMouse>(obj1, obj2, obj3);

	actual.Bark();
	var weight = actual.Weight;
	actual.Meow();
	var age = actual.Age;
	actual.Squeek();
}
{% endhighlight %}
</div>

The result is that performance is improved by a lot:

* Native ~10ms
* Mixin ~300ms
* Generic mixin ~500ms

Oh and by the way, this is for 8M invocations. So it's not that big of a hit in realistic terms. I wouldn't run this in a very tight loop - e.g. image processing or anything real time but it's quite suitable for calling some service every now and again. 

<a href='{{ site.url }}/images/2010/08/MixinFactory.zip'>MixinFactory Source</a>
