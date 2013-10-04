---
layout: post
title: "hacking become: shiny."
tags: smalltalk
---

Continuing the richness of thoughts after more time spent with recently rediscovered **Smalltalk**:

Using it is a multi-paradigm thinking inside one-paradigm engine.

This is very easy to see after a couple of messages and classes written, if you'd give yourself some freedom from past experiences.

What does it mean exactly?

I usually apply many constructs from functional languages - the ones which let you write less code and prototype faster. The most shiny yet simple of them are map/folds over collections, anonymous closures and multiple return values.
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
* emits a html containing this picture,
* tells the DOM to first set the background of the whole html body to the same color as picture background,
* tells the DOM to first make the picture invisible and then "emerge" - make it appear through the 4 seconds, using an jQuery effect/


/..explain multiple ret values hack,
then emerge stuff & monkey patching../

<!--eoe-->
*IX 2013*
