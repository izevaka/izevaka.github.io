---
layout: post
title: Test Driven Development
tags:
- Development
- tdd
status: publish
type: post
published: true
meta:
  _edit_last: '1'
---
I heard a lot of buzz about test driven development. All that time I thought that all that means is writing unit tests. Then I found out about writing tests before writing code. Meh, I thought, no big deal, a bit fussy if you ask me, it still hasn't changed my view on the whole thing.

Then recently I saw some unit tests in a Microsoft article talking about using TDD to create web controls using Model-View-Presenter paradigm. The article itself can be found here <a href="http://msdn.microsoft.com/en-us/magazine/cc188690.aspx">http://msdn.microsoft.com/en-us/magazine/cc188690.aspx</a>. 

Its crazy! You write twice as much code! Not only that, you have to use this framework to create mock objects and test methods on the interfaces before even writing them (tests before code, doh).

Having said that, I am willing to give TDD for really just one reason. And the reason is that writing tests that independently test the two halves (e.g. View and Presenter) makes you think upfront about the design of the two interfaces a bit more. This, in turn, precipitates the right design faster, without having to write a lot of either UI or business logic code.

The long  term advantage is quite obvious and is comes from both Separation Of Concerns principles (whatever shape they come in - MVP, MVC, MVV, MVA) and having unit tests. In some time, if there is a need to rewrite the implementation of any part of the system, the interfaces between them are loosely coupled and testable. The latter, in my opinion, is even more important than the former. Having a testable interface will aid finding the problems in the new implementation more than a "beautifully" designed one.
