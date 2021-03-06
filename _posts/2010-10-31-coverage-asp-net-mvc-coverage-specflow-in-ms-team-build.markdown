---
layout: post
title: Coverage, ASP.NET MVC Coverage, SpecFlow in MS Team Build
tags:
- General
status: publish
type: post
published: true
meta:
  _jd_tweet_this: ''
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: http://bit.ly/aB7AjP
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _wp_old_slug: ''
---
In this post I will go through creating solutions with code coverage, including ASP.NET MVC projects, setting up MS Test Specflow projects and getting all that to run on MS Team Build 2010.

The default out of the box setup for doing the above is not too tricky, however Team Build has stricter test pass criteria, so it is important to get both coverage and SpecFlow features with non-implemented steps to not return any errors. 

###Code coverage for MS Test projects
**Problem:** Collecting code coverage data.

Once your solution has at least one MS Test project, there will be a *.testsettings* file created. Double click it and go to *Data And Diagnostics > Code Coverage(Check Enable) > Configure*. Select your projects and you are good to go.

<a href="{{ site.url }}/images/2010/10/codecoverage.png"><img src="{{ site.url }}/images/2010/10/codecoverage.png" alt="" title="codecoverage" width="673" height="614" class="alignnone size-full wp-image-461" /></a>

Once that is done, every time a test is run, code coverage data will be collected. To show the results, simply click the *Show Code Coverage Results* button in the *Test Results* window.
<a href="{{ site.url }}/images/2010/10/showcodecoverage.png"><img src="{{ site.url }}/images/2010/10/showcodecoverage.png" alt="" title="showcodecoverage" width="437" height="141" class="alignnone size-full wp-image-464" /></a>

###Coverage for ASP.NET MVC projects
**Problem:** ASP .NET projects cause a test run error that looks this:

    Cannot initialize the ASP.NET project 'Project'. Exception was thrown: The web site could not be configured correctly; getting ASP.NET process information failed. Requesting 'http://localhost:5803/VSEnterpriseHelper.axd' returned an error: The remote server returned an error: (500) Internal Server Error.

The problem, as I understand it, is to do with misconfigured Development Web Server (Cassini) that does not properly expose a helper handler for profiler to collect code coverage data. Not ever having used ASP .NET MVC specific features in MS Test (like `[UrlToTestAttribute]`), I simply removed all those attributes and removed that project from test coverage.

Next, in order to collect code coverage I simply added the MVC project DLL and the problem went away. By default, code coverage does special handling for MVC projects to instrument them as websites (which also causes the web server to start up and slow down the test run). If you then add the MVC project's DLL, it'll run with no issues.
<a href="{{ site.url }}/images/2010/10/mvccoverage.png"><img src="{{ site.url }}/images/2010/10/mvccoverage.png" alt="" title="mvccoverage" width="520" height="634" class="aligncenter size-full wp-image-466" /></a>

###SpecFlow features
**Problem:** SpecFlow features with non-implemented steps fail build.
When a step in a feature is not implemented, the default implementation makes the test inconclusive. This is fine when run locally, but will fail a Team Build build.

There are a number of solutions that I have profiled before settling on adding `@ignore` attribtue to every feature that hasn't been implemented.

1. Set Team Build to not fail build on unit test fail. I rejected this as there shouldn't be a state where there are failing tests in a build. If a build fails, the culprit will have more incentive to fix a failing build than a simple statistic from TFS saying x per cent of tests are failing.

2. Create a test list and white list the unit tests that should be run during Test Build (by specifying the list name as part of Team Build configuration). This is fairly robust in the way that adding a new feature and not disabling it will not fail the build, however the overhead of having to add every unit test that you write to the list is too much. Chances are, developers won't do it simply because there is nothing guiding them to have to do it.

3. Disable all SpecFlow tests from test list editor. As with above approach, the overhead of opening up test list window and marking a SpecFlow test as disabled makes for a bad team process. Also, the test lists tend to get unwieldy as the number of them grows and because disabled tests are referenced explicitly from withing the `.vsmdi` file, removed test will stay there and will pollute the view.

4. My preferred way. Use SpecFlow `@ignore` attribute to disable the feature/scenario. The advantage over the above approach is that the fact that the feature is disabled lives in the feature file, not in the `.vsmdi` file. The business analysts still need to mark them them disabled, but the amount of friction is slightly less to make the process workable.

In Order to get SpecFlow unit test generator to spit out attributes, you will need to edit `specflow.exe.config` to look like this:

<div>
{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="specFlow" type="TechTalk.SpecFlow.Configuration.ConfigurationSectionHandler, TechTalk.SpecFlow"/>
  </configSections>

  <specFlow>
    <unitTestProvider name="MsTest.2010" />
  </specFlow>
</configuration>
{% endhighlight %}
</div> 

###In conclusion
Hopefully, with these simple tips, you will be able to fine tune your CI environment using TFS to eliminate the little niggling things that take hours(days, in the case of MVC coverage issues) to resolve.
