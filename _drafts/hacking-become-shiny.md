---
layout: post
title: "hacking become: shiny."
tags: smalltalk
---

Continuing the richness of thoughts after more time spent with recently rediscovered **Smalltalk**:

Using it is a multi-paradigm thinking inside one-paradigm engine.

This is very easy to see after a couple of messages and classes written.

/.../

{% highlight smalltalk %}
    MoonpixLib randomPicWithUrlAndBg: [ :url :bg |
        html image url: url; 
                altText: 'Moon image shines here';
                height: 100;
                emergeWithDuration: 4 seconds  usingInit: [ :r |
                    r script: ((html jQuery: 'body') cssAt: 'background-color' put: bg)
                ].  
{% endhighlight %}

/..explain multiple ret values hack,
then emerge stuff & monkey patching../

<!--eoe-->
*IX 2013*
