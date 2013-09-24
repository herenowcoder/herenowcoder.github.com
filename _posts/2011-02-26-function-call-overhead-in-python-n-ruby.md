---
layout: post
title:  Function call overhead in Python & Ruby (or rather a lack of it)
tags: ruby, python, performance
---


We’re recently coding our new startup in Python. Switching back to Python after quite amount of years with Ruby plus lots of much more esoteric languages was really interesting process and it’s worth a separate blog post. Just for the record, the decision was purely rational and now I’m enjoying the transition, not to mention some warm feelings while rediscovering the old fellow. Well, enough sentiments, let’s get to the meat ;)

### The Goal
As I write and then use lots of meta-hackery stuff, I try to measure overhead introduced by new levels of indirection. It’s obvious that when you use the metaclasses, proxies, dynamically injected methods, method_missing catchers and stuff like that instead of calling instanceOf WellKnownStaticallyCompiledClass.rigidlyDefinedMethod(), then you may have some overhead in just calling such chain.

Well, my gut feeling is that such overhead is completely irrelevant comparing to where your code spends most of the time, and arguing that metaprogramming would slow your code is a braindead activity.

To prove or refute such brave claim I prepared myself to run some tests. I started with the simplest thing of all things: overhead of calling nested functions in nested functions in nested functions in nested functions in nested….

And for the language zealots: sorry guys nothing to see here, my goal is NOT to compare speed between Python & Ruby and draw religious conclusions from it. Rational people can expect Py will be slightly faster than Ru and that’s it. Let’s move on.

### Nested functions - The Method
Don’t expect sophisticated profiling here. We will wrap our calls in a simple `timer()` function which measures wall time of running its arg. For Ruby, this arg is a block; for Python, a function. That’s it. I’m sure you rolled your own function for this.

I hacked simple one-line generators of our deeply nested function. This is basically identity function calling itself as many times as you want. For making it i used bread'n'butter of my favorite toolbox which is functional programming: the fold function, called `reduce()` in our both noble languages. Remember: no loops needed where we can use simple functional construct.

Pythonic generator:
{% highlight python %}
def nested_fun(lvl):
    return reduce(lambda acc,_: lambda y:acc(y), xrange(lvl), lambda x:x)
{% endhighlight %}

Ruby equivalent:
{% highlight ruby %}
def nested_fun(lvl)
    (1..lvl).reduce(lambda{|x|x}) {|acc,_| lambda{|y| acc.call(y)}}
end
{% endhighlight %}

I love oneliners, what about you? Honestly, I’d really like to start a contest on coding comprised exclusively of oneliners. Well, another time maybe ;)

### The Run
My Ruby is 1.9.2, Python 2.7.1. Machine is the same and was basically idle at that time. I’m showing you “vgrep median” values – you can guess what I mean by that ;) I’m cutting out the results returned by all these functions (which are of course the same as arguments given to generated lambdas), showing only the output timings.

{% highlight python %}
>>> timer(lambda: nested_fun(1)('functionss hiss like pythonsss'))
Elapsed time: 0.007868 ms
>>> timer(lambda: nested_fun(10)('functionss hiss like pythonsss'))
Elapsed time: 0.015020 ms
>>> timer(lambda: nested_fun(100)('functionss hiss like pythonsss'))
Elapsed time: 0.077009
>>> timer(lambda: nested_fun(1000)('functionss hiss like pythonsss'))
Elapsed time: 1.033783 ms
>>> timer(lambda: nested_fun(10000)('functionss hiss like pythonsss'))
Elapsed time: 17.657995 ms

{% endhighlight %}

To be honest, to achieve such stack level I’ve had to manually set `sys.setrecursionlimit(far_above_default_1000)` to get rid of RuntimeError: maximum recursion depth exceeded. Upon setting this to an arbitrary high value, python interpreter segfaulted when I tortured it with 100 thousand nested calls. Fair enough.

{% highlight ruby %}
irb> timer{nested_fun(1).call('functions shine like rubies')}
Elapsed time: 0.012 ms
irb> timer{nested_fun(10).call('functions shine like rubies')}
Elapsed time: 0.017 ms
irb> timer{nested_fun(100).call('functions shine like rubies')}
Elapsed time: 0.132 ms
irb> timer{nested_fun(1000).call('functions shine like rubies')}
Elapsed time: 1.203 ms

{% endhighlight %}

Trying 10 thousands nested calls gave me a nice SystemStackError: stack level too deep. I heard that messing with `ulimit` would enlarge the stack (probably for Python version too), but frankly, that would be purely academic exercise here.

### The Conclusion
So what do we have:

Even for level of indirection at 100, the overhead is as low as ~100 microseconds. That’s at least 10x faster than your fastest database request can possibly be. I guess I have my answer – for most of the code mortal earthlings produce: programs which grab something from a database backend (not to mention write to it), mungle the data and put it into a html template for a dear client, the overhead introduced by many levels of indirections (wrt to function calls) is completely completely completely irrevelant.

A slight improvement of this test would be calling not a function but a method bound to some object, so we’d measure the overhead of getting a valid method for an object as well (think `getattr()` in Py or `send()` in Ru). Well. Maybe next time.

Thanks for reading this, I hope you enjoyed :))

<!--eoe-->
*26 II 2011*


 