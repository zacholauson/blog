---
layout: post
title: "Protocols in Clojure"
date: 2014-03-04 15:59 

---
One of my tasks while refactoring my console Tic Tac Toe was to move the player logic out of the gamestate and into a [protocol](http://clojure.org/protocols).
Protocols are a way of abstracting functions away into an interface that can communicate to different datatypes without having to care what specific type they are. 

So before I had a function that would take a gamestate and decide which players turn it was, and then call another function in the gamestate which allowed each type of player to get the next move.

If it was the computers turn it would ask the Ai for the next move, and if it was the players turn it would just prompt the user for input.

Protocols allow you to have a generic interface that says, if a datatype is part of this protocol, it knows how to do a set of functions.

<script src="https://gist.github.com/zacholauson/66979789f820bfb20958.js"></script>

This is what my protocol looks like. This means any Player, will know how to respond to `next-move` and `piece`, now these functions will be implemented differently but they will still respond to the function and give us what we want.

<script src="https://gist.github.com/zacholauson/e07c24e5d110092e3a01.js"></script>

Here is my Human type, its part of the Player protocol and has 2 functions, piece and next-move, just as we would expect based on the protocol. The piece function just returns the piece that was set when it was created, but next-move takes its prompter and prompts for input in the console.

<script src="https://gist.github.com/zacholauson/1902aa7709db1a0e83d5.js"></script>

Computer is different because we cant prompt for input on the console, we have to take a gamestate and figure out what the best move is. So if its the computers turn we can just call next-move on it and it will take the gamestate and figure out what the next move is.

Having a protocol like this is great because now I can have collection of my players, and to get the next move I just pull the first player from the collection and call next-move on it, and it will know what to do.

<script src="https://gist.github.com/zacholauson/aff69fd6e042254ac497.js"></script>