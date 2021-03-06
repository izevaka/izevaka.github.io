---
layout: post
title: I Like Words - Or Introduction Into Functional Programming and F#.
tags:
- f#
- functional-programming
- General
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  wp_jd_target: http://www.somethingorothersoft.com/2010/02/09/i-like-words-or-introduction-into-functional-programming-and-f/
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  wp_jd_bitly: http://bit.ly/8YpX30
---
If you hoped to find some sort of functional programming introduction here you are sadly mistaken. This is MY introduction into such said programming. Or it is more like "I've read up on F#, my brain exploded and spilled out into this blog post".

I would like to start off by comparing programming languages to spoken languages. Most will agree that the comparison is fair. Programming languages bear most hallmarks of real life languages. There is syntax, grammar, dictionary, there are historical reasons for why the language the way it is, etc. Generally speaking one is most fluent in their native tongue and could deal with a couple of others. Similarly in programming you have your native language - C family, VB, Pascal, Ruby, Python, Perl, hieroglyphics(PHP) etc.

My native language is Russian and that's what I grew up with. From a reasonalby early age I learnt English and was reasonably fluent in it as a second language. I would like to equate this SECOND language to your FIRST programming language. The similarity, for the sake of this post, is that you think in your native language and translate it into code. The process, as those who speak other tongues would know, is similar. You form what you want to say in your head, translate all the words into the other language, rearrange the sentence if the target language requies so and then produce the output.

As with a second spoken language, writing programs eventually becomes second nature. Enter third (spoken) langague, e.g. French, that I learned in school. I found that my brain was stuck in this binary Russian/English mode and I could not add French to the mix, so my French was coming out very English like.

This is the problem I am facing with F#. It is SO compltely different that my C/C++/C#/Whatever-happy brain does not accept it. If comparing to spoken languages, it's even further away from C than French is from English. It's more like Japanese, where both the alphabet and the mindset are different.

So, coming back to the point of this dribble. I like words. I like the explicitness of procedural languages. I think it tends to follow how one thinks. `if` statements, `while` statements, assignment, parameter passing - they are all very logical.

My biggest hurdle understanding F# is the fact that the same syntax can be used for instantiation and matching. For example defining a function that takes a tuple with two members:

{% highlight fsharp %}let func (a, b) = printfn "a: %d, b: %d" a b;;{% endhighlight %}

The syntax is the same as when you define a tuple:

{% highlight fsharp %}let tuple = (4, 5);;{% endhighlight %}

Wheres a, say C++ would do something like this:

<div>

{% highlight cpp %}
Tuple<int, int> = new Tuple<int, int>(4,5);

void func(Tuple<int, int> tuple) {
}
{% endhighlight %}
</div>
F# seems to just use all these symbol operators for everything and I just can't let go of my attachment to words. `List.Add` is logical, `[4;5;]@[6;7]` is less so, especially considering that the operands cound be either values or lists.

Speaking of words, here is some lingo that you will undoubttedly come across as you learn a functional programming language. The list is not complete and some words are not simple definitions, but quite complex concepts - like combinators - those will make your head hurt extra good :))


* High Order Function - Function that can take functions as parameters and/or return a function
* Curried function - High Order Function that binds one or more of the parameters to a predetermined value and returns a function that takes less parameters.
* Purity - Functions not having side effects - i.e. the function produces an output without chnaging the input or modifying state somewhere else. This normally invoves using immutable data structures and combinators.
* Cons operator (`::`) - Adding an element to the beginning of the list.
* Strictness - Evaluating parameters to the function before the function is evaluated.
* combinator - High order function that has no free variables. Short version is that this function only operates on its parameters, not any other functions or values defined elsewhere.
* Memoization - Remembering the result of a slow running function for future use (Caching).
* Imperative programming - "normal" programming - i.e. Procedural, Object Orientated, etc.




