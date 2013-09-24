---
layout: post
title: Back to Ruby
tags: ruby
---

I’m starting this blog to write about adventurous hacking and dynamic languages: Ruby, Erlang, Smalltalk and others. I have spent a decent amount of time investigating them, observing the communities and of course using them in the wild to write everyday code.

Your value of *decent* may vary - that was ten years or more. Things can be really be digested over that time.

I’ve also had my romance with languages which enable static typing, most notably OCaml and Haskell. I’m not into the debate of DT vs ST, just please note that if it comes to static typing I’ve just been there, done that. There are several areas for which the idea of implementing stuff with type checking still gives me a warm fuzzy feeling, but well, those are not many. What gives me most fun are cool things I can do with a decent REPL and a handy arsenal of metaprogramming tricks, and these shine usually in dynamically typed languages. And programming should be fun, right?

Well, fun is what finally brought me back to Ruby.

I remember I was overwhelmed by Ruby’s beauty in 2003 and it immediately became my scripting language of choice. I was a sysadmin then and that’s what I still do for a living occasionally. It just ended my years of hunting for the Graal of the best environment for writing scripts: oneliners as well as big monsters which evolve into quite sophisticated programs sometimes. Ruby won, it was quick to write, readable, and deadly sexy.

At start I wasn’t thinking about coding more complex stuff in it. It changed the next year when I came up with an idea of coding a next small web app in Ruby, so I dived into the stuff like mod_ruby, cookies and lots of low-level stuff which few coders need to care about today. I remember when I was planning how to handle persistent sessions, I found then-fresh DHH’s demo of that new Rails thingy. That was it! I just played with it and it was more than I needed. Though I didn’t follow the path of creating dozens of websites with Rails at that time, it just reinforced my feeling that this language and its community is incredibly cool.

Then I went to the wild exploring more. One excellent essay of Paul Graham led me to rediscovering Lisp, which irreversible twisted my brain so I still mumble hungry zombie’s “want macrrrrrossss” whenever I come across a new language. Then the era of typechecking fascination came. I remember some of my coworkers really started to worry when it occured to them that a tool they use day by day is written in something such obscure to them as OCaml. Well, I was paid for the effects and tools were only a byproduct, so I could write in whatever I wanted, even brainfuck.. (by the way this is an excellent way of making fun & profit from coding at work and staying mentally healthy, but you have to be employed as an admin not a programmer, unless you’re on your own business). I was really between two worlds, solving the problems of corporate programmers with half of a brain and reading endless Haskell monad tutorials or hacking quite esoteric AliceML with another half ;). This research phase lasted several years and also Erlang arrived on a radar for another year, making me realize that I love dynamic languages more than I thought. From the moody:erlang(syntax) I went back to paren love, discovering that Clojure is more than an acceptable Lisp. I was quite sure that I will stay with these two languages for a long time. Until a cold shower came.

I was about to hack stuff which had to use a quite rare library written in Java, so the obvious choice was firing up a Clojure REPL, creating some wrappers around original crufty oo-overengineered api and adding some logic. Did it, saved my prototype and switched to another project which had to be launched.

After a month or two I was ready to continue with the first project so I loaded that prototype into Emacs and… I stumbled. It was quite hard to decipher my own code at a first glance (believe me, not because of (((parens)))), not to mention I had to recreate my state of mind in which I created a bunch of half-finished macros just to realize wtf they were supposed to do.

Saving the investigation why it happened for later, I just downloaded recent JRuby (remember, needed to use a Java lib) and in an hour or two I happily committed to git a chunk of code which was shining example of readability comparing to the original version. And of course it worked.

Now I’m not writing this to bash Clojure in any way; it’s an excellent piece of technology with a community of really inspiring people. It’s just I realized that not only writing in, but also reading Ruby was so natural thing to my mind that everything else seemed like a waste of time. Natural to the levels that I just smiled to myself when coding..

So that’s how my old lover did a come back. Since then, as I started to write more & more code in it, I naturally digested a knowledge what changed in the Ruby world, what kind of stuff people write and what state of mind they have doing this. I’ll spare the place here as some folks said it already very well, needless to say, I’m totally impressed!

I’m of course very thankful to the creators & supporters of other languages which I came across; I’m sure I’m gonna write some cool code in many of them. I will also share some of my experiences & gotchas on this blog for sure. Still, what I want to emphasize now, is:

Go Ruby go ! :D

<!--eoe-->
*27 VI 2010*
