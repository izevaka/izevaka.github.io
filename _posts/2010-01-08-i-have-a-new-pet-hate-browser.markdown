---
layout: post
title: I have a new pet hate browser
tags:
- chrome
- css
- Development
- firefox
- ie
- web
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  wp_jd_bitly: http%3A%2F%2Fwww.somethingorothersoft.com%2F2010%2F01%2F08%2Fi-have-a-new-pet-hate-browser%2F
  wp_jd_target: ''
---
Hating IE6 is so 2003. I hate Chrome now. Maybe I am biased. I only used Chrome to test a layout that has a lot of transparency. It's quite well known that webkit sucks a bit handling RGBA. But I am getting ahead of myself.

Everyone kept going on about how fast Chrome after it came out. My experience has been the opposite. A few months back every time I would start Chrome it would sit for half a minute, discovering proxy, I think. It hasn't really altered my browsing experience once it got started. That was fixed a while back.

Now I have found more issues that actually make it the slower browser out of IE(!!!) and Firefox. Try browsing this site:

<a href="24ways.org">24ways.org</a>

Yeah. You can't scroll for shit.

Fair enough, it's a bug. That's OK, we all write bugs. I would have completely not cared if not for yet another Chrome idiosynchrasy that just tipped me over the edge.

The layout that I am working on is largely inspired by 24ways.org, so it has a few semi-transparent elements, which of course don't work in IE. As a workaround I am using one pixel PNGs with alpha channel. Supposedly this is the way to do it:

[css]
.transparent {
  background: url(pngwithalpha.png);
  background: rgba(255,255,255,0.1);
}
[/css]

What the browser is meant to do is to disregard the first <code>background</code> declaration and use the second if it supports RGBA colours. That's what Firefox does... But no, Chrome loads those images anyway.

Fair enough, it has quirks. At least it would cache the images, right? <span style="font-size: x-large">WRONG!!!</span>. Unlike Firefox <strong>and</strong> IE, Chrome would request those image even though you don't refresh the page. Both IE and Firefox, when you focus on the address bar and hit enter will retrieve the images from local cache. When you refresh, they would do an <code>If-Modified-Since</code> request. Chrome does an <code>If-Modified-Since</code> request regardless.

Actually, I lie. It doesn't do the request regardless. It will do a status request for images that are visible straight away. If there is a background image that is in a <code>:hover</code> pseudo-class, it will retrieve that one from the local cache. Go figure.

<a href="http://925html.com/code/rgba-ie-fallback/">Here</a> is an excellent and very detailed comparison of browser support for transparencies and pngs. Go Firefox!!!
