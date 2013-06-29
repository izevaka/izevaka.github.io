---
layout: post
title: A few things you might not know about System.Nullable
tags:
- c++
- Development
- General
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  wp_jd_target: http://www.somethingorothersoft.com/2010/05/29/a-few-things-you-might-not-know-about-system-nullable/
  wp_jd_bitly: http://bit.ly/b0xSvz
---
There is plenty of information about `Nullable<T>` type out there and how to use it. There are, however a few tidbits that are not quite as obvious at first glance.    

#Nullable<T> is a value type

It's a struct that can be null. The only reason why `int? intVal = null;` compiles is because C# compiler supports nullable types. You **cannot** define the same kind of struct yourself. 

The decision to make `Nullable<T>` a value type actually makes sense if you think about it. Calling its member methods and properties will always succeed, i.e. NullReferenceException will never be thrown. This gurantees that `HasValue` never throws a NullReferenceException.

#Comparison to null

Under the hood, the compiler, in fact translates comparison to null to a call to HasValue. The below MSIL demonstrates that:

<div>
[code lang="csharp"]
	.locals init ([0] valuetype [mscorlib]System.Nullable`1&lt;int32&gt; 'value',
		   [1] bool hasvalue,
		   [2] bool hasvalue2)

	//int? value = null;
	IL_0001:  ldloca.s   'value'
	IL_0003:  initobj    valuetype [mscorlib]System.Nullable`1&lt;int32&gt;
	//bool hasvalue = value.HasValue;
	IL_0009:  ldloca.s   'value'
	IL_000b:  call       instance bool valuetype [mscorlib]System.Nullable`1&lt;int32&gt;::get_HasValue()
	IL_0010:  stloc.1
	//bool hasvalue2 = value != null;
	IL_0011:  ldloca.s   'value' //loads a variable onto evaluation stack
	IL_0013:  call       instance bool valuetype [mscorlib]System.Nullable`1&lt;int32&gt;::get_HasValue() //calls HasValue
	IL_001b:  stloc.2 //Stores the result in location 2 (hasvalue2)
[/code]
</div>

As you can see the generated IL for `hasvalue = value.HasValue` and `hasvalue2 = value != null` are identical.  

#null Assignment

Also from above code you can see that assigning null to a nullable value is the same as calling the parameterless contructor (which all value types have).  

#Ternary operator

C# compiler also does special things with ternary operator for nullable types. The following code compiles:

<div>
[code lang="csharp"]
int? nullableIntVal = 4;
int intVal = nullableIntVal ?? -1;
[/code]
</div>

The above is just syntactic sugar for calling `HasValue` and if it's true calling GetValueOrDefault(). The compiler actually generates a temporary variable to call `HasValue` off in both Release and Debug modes. Not sure why that is. Below code illustrates the equivalent C# code to what the compiler generates for ternary operator.

<div>
[code lang="csharp"]
int? nullableIntVal = 4;
int? temp = nullableIntVal;
int intVal = temp.HasValue ?? temp.GetValueOrDefault() : -1;
[/code]
</div>

#Summary

`Nullable<T>` is a special value type that has support from C# compiler to allow the following:

* `null` assignment  - under the hood: calling defualt constructor.
* `null` comparison - under the hood: calling `HasValue` property.
* ternary operator `??` - under the hood: calling `HasValue` and conditionally `GetValueOrDefault`.
