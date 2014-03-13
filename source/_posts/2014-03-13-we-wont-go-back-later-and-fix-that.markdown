---
layout: post
title: "We won't go back later and fix that"
date: 2014-03-13 14:15

---
Currently I am reading [Agile Software Development Principles, Patterns, and Practices](http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445) by Robert Martin, and it is fantastic.

In Chapter 7 "What is Agile Design?", he writes about why we should think about changes that could occur when first writing apps. This allows us to not be forced to tip toe around our broken code when we need to make a change.
If we think this way to wont have to go back and clean up code, by properly writing code and properly extending our functionality, we wont have to go back and 'clean up', the code should never rot.

A few weekends ago I was pairing with a friend on a simple Ruby app. We were like 10 hours in, which already is bad because it's not a [sustainable pace](http://agilemanifesto.org/principles.html).
We were tired, working on this app, and we got snagged when adding a new feature, we came across method that was written pretty poorly, but we say, 'oh well we can come back and fix it later', just so we could keep pushing through the apps features. We were so caught up on adding new things, and we werent constantly checking to make sure our existing code was being properly maintained. Ideally we should have never let the code start to rot, but in this case, if we would have taken the 10 minutes to do some refactoring and made sure everything was written correctly, we wouldn't have had to go back at the end of the night when we were dead tired and fix it. Luckily at the end of the night we remembered that it needed to be fixed and took care of it but, we could have easily forgotten it and let it rot in the code for a while.

{% blockquote Robert Martin, Agile Software Development %}
Agile developers do not "clean up" the design every few weeks. Rather, they keep the software as clean, simple, and expressive as they possibly can, every day, every hour, and even every minute. They never say, "We'll go back and fix that later." They never let the rot begin.
{% endblockquote %}
