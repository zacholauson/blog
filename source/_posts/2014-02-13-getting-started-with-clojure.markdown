---
layout: post
title: "Getting Started with Clojure"
date: 2014-02-13 09:00
comments: true

---

Last week I started my apprenticeship at 8th Light and was given the task of writing an unbeatable Tic Tac Toe in Clojure. I had not done much Clojure before then but I was really looking forward to the challenge.

To get started learning Clojure, I went through the [Clojure Koans](https://github.com/functional-koans/clojure-koans). I think Koans are a great place to start because they allow you to get your feet wet with different aspects of the language, without having to understand everything about a specific thing.

I started on some Katas once I kind of got the hang of some of Clojure's features. Katas are a great way to learn a language because they are relatively small projects that you can do over and over again and fully understand the problem. Then when you move on to a new language, you can focus on the language instead of trying to figure out how the algorithm or problem works.

[This](http://chongkim.org/programming/2013/09/04/using-katas-to-improve.html) post is by Chong Kim and is a excellent read about why we do katas and a good example of how useful they can be when learning a new language.

After I had done some katas, I started on Tic Tac Toe. At first I was having a difficult time thinking through it in a functional way. I initially started writing it out revolving around a gamestate with state using Atoms. I was told that was not very functional so I scrapped that and moved to just a single hash-map storing the movelist, turn, and board like this: 

``` {:movelist [] :turn "x" :board [:- :- :- :- :- :- :- :- :-]} ```
	
On Tuesday I got minimax working so I have a [unbeatable Tic Tac Toe](https://github.com/zacholauson/tictactoe-clojure), but its still pretty slow, even though its much quicker than my Ruby version. I am now working on implementing Alpha-Beta pruning which is a way of pruning out gamestates that wont benefit minimax to score which, in the best case can speed up the algorithm quite a lot.