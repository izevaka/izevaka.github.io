---
layout: post
title: F# - Combinators And Tail Recursion
tags:
- Development
- f#
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  wp_jd_target: http://www.somethingorothersoft.com/2010/03/01/f-combinators-and-tail-recursion/
  wp_jd_bitly: http://bit.ly/ax1vRA
---
Perhaps combinators and tail recursion are not the best beginner topics in F# but they are the ones I got stuck into pretty much straight away. I blame Google. After reading a few google hits on functional programming I of course found out about combinators and I just had to understand them.

I finally got it but it took a while. The issue, particularly in F#, is that combinators don't make all that much sense as a substitute for non-recursive functions. After all, the only way to run the combinator recursively is with a recursive function (as in `let rec`). A different solution might be to use recursive types, but that's is pretty pointless. There was a great [StackOverflow question](http://stackoverflow.com/questions/1998407/how-do-i-define-y-combinator-without-let-rec) about that by the way.

### What is a combinator

I am getting ahead of myself though. Combinators are a mathematical concept pioneered by Haskell Curry. The essence of combinators is that they allow sophisticated computation by combining simple functions. Hence the term combinator. Combinators allow a recursive algorithm to be written using a function that does not call itself but instead invokes another function that is passed in as one of the arguments.

One distinct characteristic of a combinator function is that all of its variables are bound. That means that the function only depends on its parameters, not any other data structures or function defined elsewhere. Just about the best description of what is a combinator and what isn't can be found [here](http://mvanier.livejournal.com/2897.html)

<div>
[code lang="fsharp"]
let freevar=3 //a variable

let IAmACombinator x = x*2;; //variable x is bound as a formal parameter
let IAmNotACombinator x = x*y;; x is bound but y is free. This is not a combinator
[/code]
</div>

### Example

Lets do some examples. An algorithm that I will implement is the sum of arithmetic  progression. It's an algorithm that can really be solved in O(1) time (by using the formula (m+n)*(m-n+1)/2, but it makes for good tail recursion test (i.e. you can blow the stack quite easily, unlike the factorial, which runs out of the integer range at about 15).

This is the normal recursive arithmetic progression function:

<div>
[code lang="fsharp"]
let rec progsum x =  
    if x &lt;= 1 then 1 
    else x+progsum (x-1)
[/code]
</div>

### Combinator Version
Thanks to F# type inference, the  combinator function is pretty the same for any application and only depends on the number of parameters to the worker function. 

This is the combinator version of the above function:

<div>
[code lang="fsharp"]
//Normal arithmentic progression sum combinator
let rec comb f = f (fun x -&gt; comb f x)
let progworker f x = 
    if x &lt;= 1 then 1 
    else x+f(x-1)
//Use partial application to wrap up the combinator call    
let progCombinator x = comb progworker x ;;
[/code]
</div>

This might need a bit of explaining. `comb` is a higher order function that takes another function as a parameter and returns a function taking one argument. This returned function is what we actually want - a function taking one parameter and returning something of the same type as the parameter.

In the above snippet `progCombinator` is a convenient way to partially apply our combinator to the arithmetic progression worker function. We could have just as easily done for a factorial:

<div>
[code lang="fsharp"]
let factworker f x = 
    if x &lt;= 1 then 1
    else f(x-1) * x;;
//Use partial application to wrap up the combinator call    
let factCombinator x = comb factworker x ;;
[/code]
</div>

### Tail recursion

*Tail Call Optimization* is an F# compiler feature (and in some cases [JIT Compiler feature](http://blogs.msdn.com/jomo_fisher/archive/2007/09/19/adventures-in-f-tail-recursion-in-three-languages.aspx)) where it will transform a recursive function into a while loop if the recursive call is the last statement in the function. Let's go back to the original arithmetic progression function and see if that is the case:

<div>
[code lang="fsharp"]
let rec progsum x =  
    if x &lt;= 1 then 1 
    else x+progsum(x-1)
[/code]
</div>

And it isn't. Even though it looks like the function call is the last statement of the function, `progsum(x+1)`  will be evaluated before `x+progsum(x+1)` can be computed. Let's rewrite it to take advantage of tail recursion:

<div>
[code lang="fsharp"]
let progTR x =  
    let rec inner x sum = 
        if x &lt;= 1 then sum 
        else inner (x-1) (sum + x)
    inner x 1;;
[/code]
</div>

So, what happened here? Well, for once we declared a wrapper function and the worker function inside it. This is not strictly necessary, but allows to hide some of the implementation semantics. So, if we can visualize how the original function worked, this is a crude diagram that shows the execution path for `x = 4`:

<pre>
progsum 4
->calls progsum 3
  ->calls progsum 2
    ->calls progsum 1
       ->returns 1
    <-returns (progsum 1) + 2
  <-returns (progsum 2) + 3
<-returns (progsum 3) + 4
</pre>

As you can see the result is computed "on the way out" - i.e. as the function returns. In the tail recursive version we compute the result "on the way in", which allows the recursive function invocation to be the last statement in the function. Here is the diagram:

<pre>
inner 4 1
->calls inner 3 (1 + 4)
  ->calls inner 2 (5 + 3)
    ->calls inner 1 (8 + 2)
      -> returns 10
    <-returns 10
  <-returns 10
<-returns 10
</pre>

An interesting side effect of this tail recursive function is the fact that the order in which sum happens is not quite the same as in the original function.

Original:

<pre>
4 + (3 + (2 +(1)))
</pre>

Tail Recursive:

<pre>
((4 + 1) + 3) + 2
</pre>

### Tail Recursive Combinator
So, now that we've gone over combinator functions and tail recursive functions you may ask: "What about tail recursive combinators? Do those exist?". And the answer would be "Yes, indeed, such beasts do exist". Here is the same algorithm implemented as a tail recursive combinator:

<div>
[code lang="fsharp"]
let rec combTR f = f (fun x acc -&gt; combTR f x acc)
let progTR f x acc = 
    match x with
    | 1 -&gt; acc
    | x -&gt; f(x-1) (acc + x);;
    
let progCombTR x = combTR progTR x 1;;
[/code]
</div>

Looking at the code, there is nothing groundbreaking there. Just as we converted the original function into a tail recursive function by adding a second parameter, we did the same for the both the combinator function and the inner function.

For more details on tail recursion and different ways to optimise it have a look [here](http://thevalerios.net/matt/2009/01/recursion-in-f-and-the-tail-recursion-police/) and [here](http://blogs.msdn.com/jomo_fisher/archive/2007/09/19/adventures-in-f-tail-recursion-in-three-languages.aspx).
