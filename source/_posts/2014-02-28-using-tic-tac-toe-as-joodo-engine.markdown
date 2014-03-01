---
layout: post
title: "Using Tic Tac Toe as Joodo Engine"
date: 2014-02-28 08:56
categories: 

---
One of my tasks this week was to upload my Tic Tac Toe to Clojars to use as an engine on my Joodo Tic Tac Toe.
Uploading it went smooth until I had to make a change to my engine. After every change I had to repush it up to Clojars and I knew there had to be a better way of doing this.

I remembered you could install Ruby gems locally while working on them without having to push them to rubygems or github every time. After looking around for a bit I found out you can `lein install` in your engine directory and it installs that engine clojar whatever to your local system so you can require it as a dependency without having it on clojars.