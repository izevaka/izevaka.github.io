---
layout: post
title: Git Rebase - more than a machine gun to shoot yourself in the foot with
tags:
- Development
status: publish
type: post
published: true
meta:
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _wp_jd_target: http://www.somethingorothersoft.com/2011/04/11/git-rebase-more-than-a-machine-gun-to-shoot-yourself-in-the-foot-with/
  _wp_jd_wp: ''
  _wp_jd_bitly: http://bit.ly/hJhgNv
  _jd_tweet_this: ''
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _edit_last: '1'
---
There is an adage in the development circles that C programming language is both powerful and dangerous, akin to a gun that you can accidentally shoot yourself in the foot with. By the same token, C++ makes it possibly to blow your entire leg. In this post i would like to apply these analogies in the context of git version control system.

The actual quote about c and C++ is most commonly attributed to Bjarne Stroustrup and it goes like this:

>C makes it easy to shoot yourself in the foot; C++ makes it harder, but when you do it blows your whole leg off ([link](http://www2.research.att.com/~bs/bs_faq.html#really-say-that))

git is not for faint hearted. Proper usage requires knowledge of branches, merging, remotes, detached heads (OK, maybe not detached heads, but I just wanted to mention those as they sound cool). Plenty a developer can go for years without ever having to branch (myself included). git, on the other hand almost mandates using local branches on daily basis.

History rewriting deserves a special chapter in the book of git-fu. The fetch-rebase-rebase workflow is ostensibly simple, yet incredibly powerful. This lead me to believe that git-rebase is a special kind of weapon - a handheld Tomahawk launcher with a twist. 
<a href="{{ site.url }}/images/2011/04/300px-Tomahawk_Block_IV_cruise_missile_-crop.jpg"><img src="{{ site.url }}/images/2011/04/300px-Tomahawk_Block_IV_cruise_missile_-crop.jpg" alt="" title="300px-Tomahawk_Block_IV_cruise_missile_-crop" width="300" height="193" class="alignnone size-full wp-image-526" /></a><br />
If you do happen to shoot yourself in the foot, and by that stage you already took out half a suburb with you, it gives you an option to go back in time and prevent yourself from doing that. A bit like Nicholas Cage in the movie [Next](http://www.imdb.com/title/tt0435705/).

The only catch is that you get a second chance once, rewriting history is something that should only be on on un-pushed changes. Once you push those changes (and you get a safeguard from that git that will disallow pushing rewritten history without `--force` flag), all bets are off. I am not even sure what happens with other people's repositories when a branch has been rebased upstream, most likely your coworkers will be highly pissed of with you.
