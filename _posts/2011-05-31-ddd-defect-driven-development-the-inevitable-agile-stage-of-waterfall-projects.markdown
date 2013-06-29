---
layout: post
title: DDD - Defect Driven Development - The inevitable agile stage of waterfall projects
tags:
- General
status: publish
type: post
published: true
meta:
  _jd_tweet_this: ''
  _wp_jd_clig: ''
  _jd_twitter: ''
  _wp_jd_bitly: http://bit.ly/llOD3V
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: http://www.somethingorothersoft.com/2011/05/31/ddd-defect-driven-development-the-inevitable-agile-stage-of-waterfall-projects/
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _edit_last: '1'
  _wp_old_slug: ''
---
Yes, this is yet another waterfall bashing post. If you are of the mindset that agile is for lazy code cowboy hippies, then I probably won't convince you otherwise. By the same token this is not **yet another** AGILE RULEZ write-ups, so perhaps it might be worth your while.

Gojko Adzic, in his excellent book *Bridging Communication Gap: Agile Acceptance Testing* states that Waterfall projects succeed when the team has worked together before on the same kind of project a number of times and amount of discovery to be done is minimal.

And indeed, that is rarely the case. The projects that I've been on involved a large amount of discovery, by the teams frequently unfamiliar with the most fundamental aspects of the delivery - technology, usability, functionality and little experience working together. In my opinion, unless you have done the same thing five times over, chances are your estimate is not worth the paper it's printed on.

There is a sentiment in the programming circles that software engineering should be a lot like other areas of traditional engineering. Yes, we have a lot to learn from those industries, but they are not immune to time and budget blowouts. Just of think of the last couple of big public infrastructure projects in your capital city that were on time and on budget.

There is one project in Sydney that has all the hallmarks of a large software project going wrong and that is [Parramatta to Chatswood rail link](http://en.wikipedia.org/wiki/Epping_to_Chatswood_railway_line). Surveying was complete, estimates were done, by the time the project was complete it was over budget by almost a billion dollars. After finally getting trains on the tracks it was discovered that the tunnels were a little narrow in one spot and in some trains this was causing high levels of noise. This in turn caused further delays and the need to reshuffle the train fleet.

The above example demonstrates that 100% insurance aginst unpredictable is impossible. One can always count on obstacles just popping up out of nowhere. Now, in this instance I am not advocating that the train line should be built in an agile fashion, in fact I am certin that will simply not work. However, software systems have a unique advantage over brick and mortar projects where the system is better off grown gradually over time, rather than delivered once with all the features present.

Every waterfall projects starts off with grand plans for the software to do everything under the sun. At the time when you spend a few weeks talking about what features should be in, they all seem quite achievable, and you have plenty of time, since you decide how long you need for the whole project. There alsmost is a constant factor that you need to multiply your time planning features to get the implementation time. Something in the order of 1 hour of planning gives you enough features to work for two weeks. Say 1:80. Similar to the movie Inception, if you spend a week planning features, be prepared for 80 weeks of coding.

In my experience, the first round of testing comes at a time where only half of the features intended for delivered are implemented and in turn half of those are buggy. At this time the project is kicked into a whole new phase - what I call Defect Driven Development.

Defect Driven Development (DDD for short - Hi Eric Evans :) ) is where developers only work on high priority defects as identified by QA and Business Analysts. This is really a fundamental shift in the rhythm of the project. At this point the releases to testing become closer (usually around two weeks between them), development becomes more focused and features are starting to get culled.

Come to think about it, these are all hallmarks of agile software projects:

* Prioritisation of features on nearly daily basis
* Close iterations
* Cutting scope
* Releasing with minimal set of features

I find this stage of the project the most exciting. I could be "in the zone" for days, churning out fixes and features. Some people find it stressful and annoying - to each their own. There is,however, one difference to a proper agile project - reduced responsibility on behalf of the developers. I found that "Let the testers find bugs" attitude quite common. After all, if it's not in the bug system - you don't have to do it.

What I found the most interesting is that inadvertently, teams slip into what Agile proponents have been advocating for a good decade now. What I would like to see is recognition that there is something natural about the above qualities of software development and integrating those practices into the project from the get go.
