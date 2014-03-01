---
layout: post
title: "Different Board Sizes"
date: 2014-02-24 19:59
comments: true
categories:

---

This morning I worked on putting together code that would generate the winning lines for the board size of the current gamestate. This was kinda challenging because I originally did this in my Ruby version of Tic Tac Toe but Clojure doesnt have a few of the functions that made that easy in Ruby so I got to recreate them, which was pretty fun. 

<script src="https://gist.github.com/zacholauson/4d4d7a2d80b3946ff074.js"></script>

Originally I was just passing the function a gamestate but it really slowed down my app and my tests.
I did some reading on memoization and realized that if I passed the function the board size instead of the unique gamestate that I could cache the winning positions for the game and really cut back on the time it took to execute.

So I change the argument `calculate-winning-positions` to accept a board size instead of the gamestate and then wrapped it in memoize.

	(defn winning-positions [gamestate]
      (calculate-winning-positions (count (:board gamestate))))
      
The time this cut down kinda blew me away, I was able to run all my unit tests in ~ 0.02 seconds.