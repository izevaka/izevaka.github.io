---
layout: post
title: Composite Pattern and Unity IoC container
tags:
- csharp
- Development
- unity-configuration-block
status: publish
type: post
published: true
meta:
  _edit_last: '1'
  _wp_jd_target: http://www.somethingorothersoft.com/2010/12/31/composite-pattern-and-unity-ioc-container/
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _wp_jd_url: ''
  _wp_jd_yourls: ''
  _wp_jd_wp: ''
  _wp_jd_bitly: http://bit.ly/e73M58
  _wp_jd_clig: ''
  _jd_twitter: ''
  _jd_tweet_this: ''
  _syntaxhighlighter_encoded: '1'
  _wp_old_slug: ''
---
Joel Spolsky [thinks](http://stackoverflow.com/questions/871405/why-do-i-need-an-ioc-container-as-opposed-to-straightforward-di-code/871420#871420) that IoC containers are an overkill and Dan North [agrees](http://twitter.com/#!/tastapod/status/12054276046061568) that *IoC Containers inadvertently tolerate highly inter-dependent designs*. IoC containers, in my opinion, are indispensable in creating a loosely coupled application. So much so, that I think that there should be a language that has Dependency Injection built-in.

That aside, I have discovered a feature in [Unity container](http://unity.codeplex.com/) that makes implementing a composite pattern kinda fun. Just a brief remainder of what the [composite pattern](http://en.wikipedia.org/wiki/Composite_pattern) is. It is a simple pattern where a class implements an interface and delegates invocation on that interface to an internal collection of actual interface implementations. This allows the calling class to treat multiple implementations as one single component.

Generally, this pattern is most appropriate for interface containing methods that have a simple return type or void ( i.e. Command Pattern). I found that things that are called "SomethingPolicy" are a good match for this pattern. In this example I will use ASP .NET authorization as an example of how to create a true Open-Closed component using a composite pattern.

For the sake of clarity I will call the class implementing the composite pattern *composite class* and actual implementations *implementation interfaces*.

The implementation of this pattern relies on a couple of interesting Unity Container properties.

**Property 1:** Items registered with a name are not resolvable through `Resolve()`. Also remember that a `Resolve()` call is the same as taking a dependency on that type in the constructor. These registrations are only available when calling `ResolveAll()`.

**Property 2** Items registered without a name are **not** available when calling `ResolveAll()` and are only available as a result of a call to `Resolve()`.

This means that you can register the composite class without a name and implementation interfaces as mappings with name. The composite class then becomes the default implementation - i.e. when someone takes a dependency on that interface in the constructor.

[This question](http://stackoverflow.com/questions/780436/asp-mvc-authorize-all-actions-except-a-few) describes how to create a base controller that overrides `OnAuthorization()` method and allows to require authorization by default, without having to decorate all action methods with `[Authorize]` attribute.

So, here is the base controller: 
<div>
{% highlight csharp %}
public class BaseController : Controller
{
    private readonly IAuthorizationPolicy _Policy;

    public BaseController(IAuthorizationPolicy policy) {
      _Policy = policy;
    }
   
    protected override void OnAuthorization(AuthorizationContext filterContext)
    {
        // Check if this action has NotAuthorizeAttribute
        object[] attributes = filterContext.ActionDescriptor.GetCustomAttributes(true);
        if (attributes.Any(a => a is NotAuthorizeAttribute)) return;

        filterContext.Result = _Policy.Execute(filterContext);
    }
}
{% endhighlight %}
</div>

Below is the composite class. Note that it takes a constructor parameter of an array of mappings of implementation interface registration to the order in which it should be executed. This allows fine tuning the order in which the policies are executed. Note the near textbook following of SRP, where the logic of executing multiple policies and ordering of invocations is kept out of the controller class. The reason for this collection being an array of key value pairs as opposed to a dictionary is for ease of configuration through XML configuration.

<div>
{% highlight csharp %}
	public interface IAuthorizationPolicy {
		ActionResult Execute(AuthorizationContext context);
	}

	public class AuthorizationPolicyComposite : IAuthorizationPolicy{
		
		private KeyValuePair<int, string>[] _NameList;
		private IEnumerable<IRequestPolicy> _Policies;

		/// <summary>
		/// Creates AuthorizationPolicyComposite class.
		/// </summary>
		/// <param name="dictionary">priority list of container registration names. E.g. {1,"policy1"} will use the container 
		/// to resolve an instance of <see cref="IAuthorizationPolicy"/> using "policy1" name and will put that instance in the order specified by the integer.</param>
		public AuthorizationPolicyComposite(KeyValuePair<string, int>[] dictionary) {
			// TODO: Complete member initialization
			this._NameList = dictionary;

			InitComposite();
		}

		private void InitComposite() {
			_Policies = _NameList
				.OrderBy(item => item.Value) //sort on order number
				.Select(item => _Container.Resolve<IAuthorizationPolicy>(item.Key));
		}

		#region IRequestPolicy Members

		public System.Web.Mvc.ActionResult Execute(AuthorizationContext context) {
			foreach(var policy in _Policies) {
				var result = policy.Execute(context);
				if(result != null)
					return result;
			}
			return null;
		}
	}
{% endhighlight %}
</div>

The registrations for all of these can be done as follows:

<div>
{% highlight csharp %}
private void InitContainer(IUnityContainer container) {
	//composite regisrtation
	container.RegisterType<IAuthorizationPolicy, AuthorizationPolicyComposite>(new Dictionary<string,int> {
		{"normal", 1},
		{"activation", 2},
	}.ToArray());
	//normal authorization policy
	container.RegisterType<IAuthorizationPolicy, NormalAuthorizationPolicy>("normal");
	//User activation authorization policy - i.e. a user must go through activation process on first login
	container.RegisterType<IAuthorizationPolicy, UserActivationAuthorizationPolicy>("activation");
}
{% endhighlight %}
</div>

That's it. The configuration for XML is not included here as it is pretty much the same as the API version. There is a trick to get the `KeyValuePair`s to work using a converter, but that's a topic for another post.

