---
layout: post
title: The definitive step by step guide on how to delete a directory permanently
  from git on Widnows for dumbasses like myself
tags:
- General
- git
- howto
status: publish
type: post
published: true
meta:
  _edit_last: '1'
  _jd_twitter: ''
  _jd_tweet_this: 'yes'
  _wp_jd_clig: ''
  _wp_jd_bitly: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
---
I admit, git hurts my head. I also admit that I only understand about 10% of git documentation. This is probably about industry average. Removing big files out of git index is just about one of the painful things a normal person will ever have to do. At some point Mr Craig of <a href="http://www.kodespace.com/">kodespace</a> indicated IT IS ABSOLUTELY IMPOSSIBLE to get rid of a large file from your repo. I am sure that I misunderstood him and that's possibly not what he said or meant.

Regardless, deleting pre-compiled libraries out of your repository is possible and useful. The page that I got most of your information is this one:

<a href="http://stackoverflow.com/questions/1216733/remove-a-directory-permanently-from-git">http://stackoverflow.com/questions/1216733/remove-a-directory-permanently-from-git</a>

<strong>git filter-branch</strong> is the command you need to use to rewrite the history. <strong>git rebase</strong> should also be able to do it, but I had no luck using it. So, most of the steps come from here:

<a href="http://progit.org/book/ch9-7.html">http://progit.org/book/ch9-7.html</a>

The steps for directories on Windows are a bit different though and I will describe them here. 
<ol>
<li>We have a our clean repo with one file in it, run git gc and see how much space it's taking up:

<pre>
C:\git\test>git gc
Counting objects: 8, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (8/8), done.
Total 8 (delta 0), reused 0 (delta 0)

C:\git\test>git count-objects -v
count: 0
size: 0
in-pack: 8
packs: 1
size-pack: 4
prune-packable: 0
garbage: 0
</pre>size-pack is the one we are interested in and it is 4K. Good.</li>
<li>Now we add boost library, lets see what happened to our size:

<pre>
C:\git\test>git gc
Counting objects: 562, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (553/553), done.
Writing objects: 100% (562/562), done.
Total 562 (delta 102), reused 8 (delta 0)

C:\git\test>git count-objects -v
count: 0
size: 0
in-pack: 562
packs: 1
size-pack: 738
prune-packable: 0
garbage: 0
</pre>

Packed size is up to 738K. This is what gets transmitted over the wire. Lets now permanently delete it.
</li>

<li>First, garbage collect the repo. This is required to pack all your objects. This is absolutely necessary if you want the repo size to decrease. <strong>DO NOT OMIT THIS STEP</strong>!!!

<pre>
C:\git\test>git gc
Counting objects: 562, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (553/553), done.
Writing objects: 100% (562/562), done.
Total 562 (delta 102), reused 8 (delta 0)
</pre>
</li>
<li>
Now, lets run <strong>git filter-branch</strong>. This step is taken out of Pro Git book. However there are a few caveats. <strong>First</strong>, the slashes in the path must be forward slashes (unix style), even if working out of windows command line. <strong>Second</strong>, you must specify <strong>-r</strong> argument when calling <strong>git rm</strong>.

<pre>c:\git\test\git filter-branch --index-filter "git rm -r --cached --ignore-unmatch libs/BOOST_1_28_0"  HEAD

...
...
...
rm 'libs/BOOST_1_28_0/type_traits/function_traits.hpp'
rm 'libs/BOOST_1_28_0/type_traits/fwd.hpp'
rm 'libs/BOOST_1_28_0/type_traits/ice.hpp'
rm 'libs/BOOST_1_28_0/type_traits/object_traits.hpp'
rm 'libs/BOOST_1_28_0/type_traits/same_traits.hpp'
rm 'libs/BOOST_1_28_0/type_traits/transform_traits.hpp'
rm 'libs/BOOST_1_28_0/type_traits/transform_traits_spec.hpp'
rm 'libs/BOOST_1_28_0/type_traits/type_traits_test.hpp'
rm 'libs/BOOST_1_28_0/utility.hpp'
rm 'libs/BOOST_1_28_0/utility/base_from_member.hpp'
rm 'libs/BOOST_1_28_0/utility_fwd.hpp'
rm 'libs/BOOST_1_28_0/version.hpp'
rm 'libs/BOOST_1_28_0/weak_ptr.hpp'

Ref 'refs/heads/master' was rewritten</pre>

<strong>Note.</strong>You should see each individual file being rewritten, if you see nothing at all, something is not right.
</li>
<li>Still following the Pro Git book, we delete .git/refs/original and .git/logs. Prune and count objects:

<pre>C:\git\test>git gc --prune=now
Counting objects: 9, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (9/9), done.
Total 9 (delta 1), reused 8 (delta 0)</pre>

<pre>C:\git\test>git count-objects -v
count: 0
size: 0
in-pack: 9
packs: 1
size-pack: 4
prune-packable: 0
garbage: 0
</pre>

Yay!!! the repository is back to it's original size.</li>
<li>Sometimes the steps above do not work and your packed size remains the same. Do not fret, there is a solution and it can be found on this page:

<a href="http://stubbisms.wordpress.com/2009/07/10/git-script-to-show-largest-pack-objects-and-trim-your-waist-line/">http://stubbisms.wordpress.com/2009/07/10/git-script-to-show-largest-pack-objects-and-trim-your-waist-line/</a>

Create an empty repository and clone the half cleaned one like so:

<pre>
C:\git\test>mkdir ..\test_clean

C:\git\test>cd ..\test_clean

C:\git\test_clean>git init
Initialized empty Git repository in C:/git/test_clean/.git/

C:\git\test_clean>git pull file:///c/git/test
remote: Counting objects: 17, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 17 (delta 3), reused 15 (delta 2)
Unpacking objects: 100% (17/17), done.
From file:///c/git/test
 * branch            HEAD       -> FETCH_HEAD

C:\git\test_clean>git count-objects -v
count: 17
size: 11
in-pack: 0
packs: 0
size-pack: 0
prune-packable: 0
garbage: 0

C:\git\test_clean>git gc
Counting objects: 17, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (17/17), done.
Total 17 (delta 3), reused 0 (delta 0)

C:\git\test_clean>git count-objects -v
count: 0
size: 0
in-pack: 17
packs: 1
size-pack: 5
prune-packable: 0
garbage: 0
</pre>

Voila, The repo size is back down again!!! The fact that it's 5K is probably due to the fact that we added some stuff after we added boost.
</ol>
