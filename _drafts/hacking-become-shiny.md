---
layout: post
title: "hacking become: shiny."
tags: smalltalk, web
---

Continuing the richness of thoughts after more time spent with recently rediscovered **Smalltalk** I came up with the conclusion:

Using it is a multi-paradigm thinking inside one-paradigm engine.

This is very easy to see after a couple of messages and classes written, if you'd give yourself some freedom.

What does it mean exactly?

I usually use many constructs from functional languages - the ones which let you write less code and prototype faster. The most shiny yet simple of them are map/folds over collections, anonymous closures and multiple return values. When these constructs are missing, I look around how to mimic them.
The other thing which is not necessarily another paradigm, but rather a way of organizing work of yourself vs other people, is the benefit of dynamism applied to the different kinds of code reuse. You can subclass other people's code, but you can also strike in the middle and monkey-patch their stuff. I really feel free and not tied when I can do all of the above, straight away in the default coding env.

Smalltalk has all the things described above, but some of them have an unexpected form, so to say.

Let's go with the code which steered my mind into writing this article and then draw some essence from it.

Here it is -- technically, a method firing one message with a block containing more messages inside, some of them cascaded:

{% highlight smalltalk %}
renderContentOn: html

    MoonpixLib randomPicWithUrlAndBg: [ :url :bg |
        html image url: url; 
                altText: 'Moon image shines here';
                height: 100;
                emergeWithDuration: 4 seconds    usingInit: [ :r |
                    r script: ((html jQuery: 'body') cssAt: 'background-color' put: bg)
                ].  
    ]
{% endhighlight %}

This code does lots of things:
* gets random picture from my library of moon porn ;) plus an information of its background color,
* emits an html tag containing this picture,
* tells the DOM to first set the background of the whole html body to the same color as picture background,
* tells the DOM to first make the picture invisible and then "emerge" it - make it fading in through the 4 seconds, using an jQuery effect.

What is really happening here?

First, I need to admit there's something on my mentioned list of favourites which is not directly available in this language: multiple return values. So I couldn't go with an equivalent of `url, bg = MoonpixLib.randomPic()`. A remedy for my aching soul was to pass the block to the method we'd expect to return multiple values -- this block should have as many arguments as the original number of returned values. Then we continue the code in this block. Please note it changes functional-like coding style into more callback-like one. The syntax is much nicer than in the most today's callback-oriented environments (hello, Node.js), IMO.

Just to stretch the mind of the reader, here's how this `randomPic..` method would be written in Ruby:

{% highlight ruby %}
def random_pic_with_url_and_bg(&block)
    pic = random_pic_from_my_lib
    bg = pic.extract_bg
    url = pic.url
    # how above methods are implemented is irrelevant
    block.call url, bg  # or:  yield url, bg
end
{% endhighlight %}

This is no rocket science, just a normal block passing technique.

What is important in my Smalltalk example, is that the block passed is a true lexical closure. It allows me to use inside the `html` variable, which is argument of the `renderContentOn` method. In terms of the library used here ([Seaside] web framework), `html` is a *canvas* I want to paint on -- beautiful metaphor, by the way... Then what happens inside the block is a series of messages. Let's look at it again:

{% highlight smalltalk linenos %}
        html image url: url; 
                altText: 'Moon image shines here';
                height: 100;
                emergeWithDuration: 4 seconds    usingInit: [ :r |
                    r script: ((html jQuery: 'body') cssAt: 'background-color' put: bg)
                ]. 
{% endhighlight %}

First we send `image` message to the `html` canvas, then the series of messages to the object (let's call him a pic-object) returned by it. Such series is called a *message cascade* and is denoted by the semicolons between message sends. Such cascade effect could be done in Ruby by invoking the `tap{|o| o.mutate(...)}` in the middle of some call chain.

Each message sent to pic-object mutates its state and finally causes `<img>` tag with chosen attributes to be written on the canvas - or an action being registered to change the canvas later. The last message in the cascade -- line 4 -- is actually two-arg message -- its name aka selector is `emergeWithDuration:usingInit:` -- you may know this convention from Objective-C or RubyMotion if you coded for your iPhone yet. 

The interesting thing is that there was no such thing as *emerging* in the Seaside library. First I wrote the code which does the trick: hides the DOM element, changes the background of the whole page and then fades in the element. Then I wanted to abstract away this code, so I could emerge *any* html element on my page with the code like above.

It was so easy and beautiful!

I just monkey-patched one of the superclasses of the html elements - `WATagBrush` in this case - the topmost common thing that can be painted on the html canvas (the brush and the canvas, if you care for a little poetry - Seaside authors did for sure).  By the way, I think I may be the only Smalltalker using the term monkey-patching, well this is my Ruby habit -- in Smalltalk the detailed technique used is just writing a new method for the existing class in a separate package and it's done by putting this new method (message, in fact) in a protocol named as `*Your-Package`. Then all existing subclasses of such "patched" class have this new behaviour and also your code is in separate package, it's not messed with the  code of the original class, you can distribute it separately etc etc (there are no *files* with the source code in Smalltalk, really).

Now some note for the ending -- although I haven't used Smalltalk for years and I'm only reminding it since few weeks, it was really quick to catch up things and learn what was described here. I landed with the piece of code above much faster than it took to write this article. Most important thing is that the little code above is part of my experiment of using composable ui components in web apps - meaning, the parts of controller & view are encapsulated into boxes which can be reused. This is the topic to write much more about. 

<!--eoe-->
*IX 2013*

[Seaside]: http://seaside.st
