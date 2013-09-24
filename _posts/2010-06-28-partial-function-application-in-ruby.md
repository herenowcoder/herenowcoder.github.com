---
layout: post
title: Partial function application in Ruby
tags: ruby, functional
---

### First, the Meat

I’m used to partial functions in various programming languages so much that I rolled my own solution for Ruby 1.8 (yeah well 1.9 has Proc.curry):

{% highlight ruby %}
def partial f, *args0
  f = method(f) unless f.respond_to? :call
  proc{|*args| f.call *(args0+args)}
end

irb> partial(lambda{|x,y| [x,y]}, 42).call(23)
=> [42, 23]
irb> def f x,y; [x,y] end
irb> g = partial(:f, 42)
irb> g.call(23)
=> [42, 23]
{% endhighlight %}

As you see, it accepts a `Proc` to be called and a list of initial arguments then creates a closure around these arguments. For convenience it also accepts a symbol denoting a method instead of an explicit Proc. It mimics Clojure’s partial function which I got used to.

To use it you can `call()` the returned closure or define a new method around it – I prefer the latter so it saves my keystrokes when prototyping, so I added some sugar:

{% highlight ruby %}
class Object
  def add_code &block
    (class << self; self end).module_eval &block
  end
end

def def_partial new_meth, f, *args0
  closure = partial(f, *args0)
  add_code do
    define_method new_meth, closure
  end
end

irb> def_partial :g, :f, 42
irb> g 23
 => [42, 23]
{% endhighlight %}

Btw, a little meta hack `add_code` found its way here, well, it's a shortcut for dynamically adding
methods from the scope of other method.

I especially like the def_partial usage as it seems very clean & simple. It’s near to the clarity of OCaml and Haskell, where I have this mechanism for free as functions are automatically curried:

{% highlight ocaml %}
$ ocaml
Objective Caml version 3.11.1
# let f x y = [x; y];;
val f : 'a -> 'a -> 'a list = <fun>
# let g = f 42;;
val g : int -> int list = <fun>
# g 23;;
- : int list = [42; 23]
{% endhighlight %}
{% highlight haskell %}
$ ghci
GHCi, version 6.10.4: http://www.haskell.org/ghc/  :? for help
Prelude> let f x y = [x,y]
Prelude> :type f
f :: t -> t -> [t]
Prelude> let g = f 42
Prelude> :type g
g :: Integer -> [Integer]
Prelude> g 23
[42,23]
{% endhighlight %}

### But Why?

Yeah, why bother and use such construct at all? Is it another academic example of forcing functional style in Ruby without real purpose?

Actually not at all.

For those folks like myself whose mindset is not quite object oriented, but still admiring Ruby’s elegance enough to stay coding in it, it’s a very useful idiom. At least for a certain approach to hacking, which I’ll try to describe.

When I start to prototype, the first thing I write is a bunch of functions which operate on some input data; the functions are usually pure. It may be the effect of my past exposure to functional languages or just the fact that I naturally think of a problem in terms of actions rather than objects which are subject to them; in other words, I describe it using verbs rather than nouns. Besides, having pure code can be a great advantage, especially for testing. Living in a REPL I just have my test fixture data together with the state needed to run them bound to some variables and I just run my pure functions on them, that’s all. I do it even before I create unit tests, which are delayed a bit till I know exactly what kind of behavior do I really need from the code.

The functions I start with have their arguments in the order of importance, or to put it more clearly, in the reverse order of how often they change. That makes them an ideal candidate for partial application. Let’s look at the example..

The other day I was checking the database usage for specific queries, which ended up getting the metadata from the database engine and shuffling it to extract some relevant bits. It had to be parametrized by the usernames whose values were retrieved from another source and by the tables subset. The code had to operate on several databases which had roughly the same structure and data – just assume it was production and several test instances each having a significant portion of production data. Of course I didn’t wanted to run my code on production too quickly; even if it didn’t modify anything it could give it a performance hit when retrieving data in a non-clever way.

The code I quickly created was called roughly like this:

{% highlight ruby %}
process_metadata(connection_pool[db_instance], tables_subset, 
                 guess_most_abusing_user(...), options)
{% endhighlight %}
When I was sure that retrieving the metadata doesn’t make the db crawling, it was ready to be run on production, namely to be put at many places in the monitoring script. The quickest way to do it, while saving keystrokes and improving readability, was:
{% highlight ruby %}
def_partial :check_usage, :process_metadata,
            connection_pool[:production]
def_partial :check_biggest_tables, :check_usage,
            biggest_tables_subset
# later in the code:
check_usage subset1, a_hardcoded_user
check_usage subset2, guess_most_abusing_user
check_biggest_tables guess_most_abusing_user
check_biggest_tables guess_most_abusing_user_in_other_way
{% endhighlight %}

..and so on. No need to create classes, wrappers, guessing the PerfectNameForNewlyWrittenAdapter and how it could be extended in the future, no new nouns, also: no defining verbose methods whose only purpose is calling existing methods with less args. Just using what’s already there and making a closure of it for subsequent use.

### So many words, now a short summary

The techniques I described are of course nothing new; in fact, it’s all quite trivial. I just wanted you to track a mental process of programming in this specific way.

I quite enjoy having minimized the number of classes by writing code like this.. In fact, every time I start writing class SomeNoun ... I’m asking myself: do I really need it? Can I live without it? Can I achieve what I need using some dynamic meta-trickery in an one-liner?

Well, you don’t have to call it cleverness, you can call it laziness if you’d like :–) Personally I wonder how many other coders also prefer such programming style in Ruby; if you are one of them, please drop me a line..

If you find it all artificial or unnatural, please note there’s ongoing debate whether closures are poor man’s objects or rather objects are poor man’s closures. There are no clear winners and there’s consensus it won’t be any ;–)

Thank you for your time!

Edit: the original post had a bug which is fixed now.
