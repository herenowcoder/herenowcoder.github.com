---
layout: post
title: "Who answers your Ansible?"
tags: admin, cm, beauty
---

The answer to this question will be clear after digesting this:

[Ansible] is simply a configuration management done right.

No agents means no firewall problems, no overhead,
no-chicken-and-egg problem (what should monitor the agents?).
Just using what is already configured - and well, admin needs
to have ssh to his machines configured.

I like the beauty and elegance of simple solutions.

So, to answer the title question:

just your machine, not an intermediate agent.

The other possible answer is: 
*no one is urged to* ;)

(the former seems to be valid considering the original [ansible idea]..
 with greetings to Ursula K. Le Guin and Orson Scott Card)

<!--eoe-->
*VIII 2014*

[ansible]: http://en.wikipedia.org/wiki/Ansible_(software)
[ansible idea]: http://en.wikipedia.org/wiki/Ansible
