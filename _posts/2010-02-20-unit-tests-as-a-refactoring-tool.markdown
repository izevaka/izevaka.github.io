---
layout: post
title: Unit Tests As A Refactoring Tool
tags:
- csharp
- General
- tdd
- unit-testing
status: publish
type: post
published: true
meta:
  _syntaxhighlighter_encoded: '1'
  jd_tweet_this: 'yes'
  _edit_last: '1'
  jd_twitter: ! 'Epic blog post - #title# - #url#'
  wp_jd_target: http://www.somethingorothersoft.com/2010/02/20/unit-tests-as-a-refactoring-tool/
  wp_jd_bitly: http://bit.ly/9ESQxn
---
So I have this class that is a bit too fat and has no unit tests. It's way overdue for a clean up. I thought I would investigate how to refactor with unit tests.  

### Application
The application in question is a fairly normal case of a business DSL where the domain rules are expressed in XML and the compiler generates an application for different platforms (web, mobile web, PDA, WPF). Code is written in C#, tested with MbUnit v.3 and NMock2.

This particular class parses XML and generates JSON to be later consumed by ExtJS library. The JSON is a declaritive form of the control that users will eventually see. This control is complicated and has many options. Hence the fatness.

### Enter the unit tests
Now, unit tests are touted to be great for refactoring. They are. The usual approach is to write the tests, make sure they pass, then refactor and make sure the tests don't break. Well, I want to take it one step further and let the writing of the tests guide the refactoring.

Thoroughly unit tested code tends to have really good code smells. This is mainly due to fact that in order to test all aspects of a large block of code, you must isolate all of its constituents. This is evident in all projects that are written in a TDD way. They are all very modular and modular is good(I think :-) ).

So my goal here is just simply write the unit tests and see where it takes me. I would imagine that just by making the code testable, it will become a little bit better.

I never invisaged this to become a full blown article, but now that I am here, lets introduce some numbers to make this more scientific. I will be using Microsoft Team Studio Analysis Tools throughout the process. Here is a snapshot of what the class looks like at the start:

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/start.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/start.png" alt="" title="start" width="808" height="173" class="alignnone size-full wp-image-284" /></a>

As you can see the `Build` method is too big - 250 lines. This is bad. It's one of those `DoIt` methods that does everything.

### First refactor
Alright, lets get cranking. Straight away I noticed that I was reading a particular bit of XML and making decisions based on the node contents. This particular functionality has already been abstracted away in another class, so I might as well use that class. So I just refactored that without writing any tests before or after. That's pretty naughty :)) Lets make it proper and rip this code into another function so we can test it.

Starting from the top of the method I am looking at a bit of code that parses an atribute and calls a method that is defined in the base class depending on whether or not the attribute exists.

### Base class dependency
Now refactoring that into a method is not a problem, the problem is isolating the call to the base class. There is case to be made for having the base class contain all the dependencies that are required for all of its subclasses, however, just by skimming the code it looks like a lot of the methods there are simply in one place for convinience. So this could potentially be a case of Inheritance vs. Composition. Lets start off by abstracting  this particular method into an interface and have our base derive from the interface. Then in our derived class we access that method through a reference to the interface, not implicitly.

This is what it looks like now:

<div>
[code lang="csharp"]
class Builder {
   public DoStuff(string blah){
      //impl
   }
}

class GridBuilder : Builder{
   public void Build() {
      var elem = Element.GetAttribute(&quot;Blah&quot;);
	  DoStuff(blah);
   }
}
[/code]
</div>

Let's tweak it as described in the previous paragraph:

<div>
[code lang="csharp"]
interface IBuilder {
	DoStuff(string blah);
}

[code lang=&quot;csharp&quot;]
class Builder : IBuilder {
   [Obsolete]
   public DoStuff(string blah){
      //impl
   }
}

class GridBuilder {
   
   IBuilder _Builder;
   
   public GridBuilder() {
     _Builder = this;
   }
   
   public GridBuilder(IClientBuilder b) {
			m_ClientBuilder = b;
	}
   
   public void Build() {
      var elem = Element.GetAttribute(&quot;Blah&quot;);
	  _Builder = DoStuff(blah);
   }
}
[/code]
</div>

So what I have done is put that `DoStuff` into an interface and marked the implementation as Obolete. This is more out of convinience to get the compiler to show me where else this method is being used. Turns out this was the only case where it was used in `GridBuilder`. I could have just as well used text search, but hey, if there is a fancy compiler feature, why not use it? Notice that I also added a dependency constructor so I can compose `GridBuilder` in a unit test.

Let's make a unit test for this use of `DoStuff`. As I am doing that I realised that my base class is in a different assembly and required some code duplication from another test project to make the test not crash. The fact that I had to do that is a pretty bad, but my copying the common setup file and including it in my test project is even worse. We can come back to that later, lets concentrate on what we are doing now. Might as well use the `Obsolete` attribute to remind me to refactor the unit test later on.

### Finally, I can test `Build`
So, after a long time setting up `GridBuilder`'s dependencies I am finally here - the test runs. Lets review what I've achieved so far and have a peek at the metrics. So here is the summary of what was required just to make the test run:

- Two instances of referring to the base class have been abstractes into two interfaces.
- Found a bug where if a required element is present but contains no nodes the builder crashes.
- The test creates a total of three mock objects with a total of five `Stubs` and one `Expect`.
- The test needs to pass in an Xml element with two elements and one atribute.
- Found a small refactoring job that is not really related to the unit testing aspect of this excercise.

Code metrics:

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step1.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step1.png" alt="" title="step1" width="954" height="210" class="alignnone size-full wp-image-286" /></a>

So I didn't get anywhere as far as most metrics are concerned, in fact I almost went backwards, if anything. Let's keep going. So now I have a unit test that runs the `Build` method lets keep trying to test more and more parts of it.

Before I go on, I would like to emphasize the fact that I had to create lots of mocks and pass in lots of dependencies just to make one method not crash. Here is the unit test. Definitely too much for ONE method:

<div>
[code lang="csharp"]

		[SetUp]
		public void Setup() {
			m_Mocks = new Mockery();
			m_Settings = new InlineBuilderSettings {
				Compiler = m_Mocks.NewMock&lt;IWebCompiler&gt;(),
				ComponentName = &quot;Test&quot;
			};
			//Used by setup method
			Stub.On(m_Settings.Compiler).GetProperty(&quot;ServerCompiler&quot;).Will(Return.Value(null));
			Stub.On(m_Settings.Compiler).GetProperty(&quot;ServerType&quot;).Will(Return.Value(typeof(Data.ServerAplication)));
		}
		
		[Test]
		public void BuildShouldCallRequireComponentWhenViewAttribute() {

			var iClientBuilder = m_Mocks.NewMock&lt;IClientBuilder&gt;();
			var iBuilder = m_Mocks.NewMock&lt;IWebBuilder&gt;();

			Builder builder = new Builder(iClientBuilder, iBuilder);
			m_Settings.Element = 
				new XElement(&quot;Grid&quot;, new XAttribute(&quot;View&quot;, &quot;test&quot;), 
					new XElement(&quot;Source&quot;, new XAttribute(&quot;View&quot;, &quot;DataTest&quot;)),
					new XElement(&quot;Fields&quot;));

			Expect.Once.On(iClientBuilder).Method(&quot;RequireComponent&quot;).With(&quot;test&quot;, m_Settings.Element).Will();
			Stub.On(iClientBuilder).Method(&quot;GetComponentInfo&quot;).Will(Return.Value(new ComponentInfo()));
			Stub.On(iBuilder).Method(&quot;AddConfig&quot;).Will();
			Stub.On(m_Settings.Compiler).Method(&quot;AddInclude&quot;).Will();
			
			builder.Setup(m_Settings);
			builder.Build();
		}
[/code]
</div>

Realistically, in order to complete the build process we need all those dependencies. That's pretty fundamental. However, it should be possible to reduce the amount of dependencies ONE method has. 

###Next refactor
My next target was a reasonably self contained code block that I did "Extract Method" method on. Two more unit tests. Slight improvement in maintainability and complexity.

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step2.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step2.png" alt="" title="step2" width="996" height="236" class="alignnone size-full wp-image-287" /></a>

Next one is a big sucker. This block produces about 5 output objects that are dumped to JSON later on. I actually moved to another two classes. One to perform the build and another to store the output.

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step3.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step3.png" alt="" title="step3" width="896" height="236" class="alignnone size-full wp-image-288" /></a>

Good improvement on all counts. Lets keep splitting the Build method and writing tests for it. As I am doing this I decided to make the GridBuilder a namespace within a project. There are a number of classes in it by now, might as well contain them in a folder.

### Day later.
I have somewhat abandoned refactoring GridBuilder as I found a whole bunch of other tightly coupled classes. The unit tests have proven to be a great tool exposing those dependencies. They also become usefull when the refactoring becomes non-trivial - i.e. goes beyond just moving a block of code out of a method.

This is what the class looked like once I finished with it:

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step4.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/step4.png" alt="" title="step4" width="975" height="230" class="alignnone size-full wp-image-289" /></a>

Now that I ran out of time that I gave myself for this task I think I have a few notes for future.

 - Parsing an XML collection is ugly and tends to get messy when things need to be addded to the inner loop. I think for this scenario I would need some sort of visitor pattern implemented so that you could add things to the inner loop without having to alter the loop itself.
 - Having implicit dependencies (like a dependency on a property of an actual dependency) totally breaks the unit tests. A situation where you create one mock and then have its method return another mock is a bit ridiculous.
 - A unit test for a monstrous method will too be monstrous. There is almost 1-to-1 relationship between how fat the method is and how fat its unit test is. I think that if one aims to make unit test simpler, the code under test will in turn become simpler.
