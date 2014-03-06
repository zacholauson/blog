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

{% codeblock lang:clojure %}

(defprotocol Player
  (piece [this])
  (next-move [this gamestate]))

{% endcodeblock %}

This is what my protocol looks like. This means any Player, will know how to respond to `next-move` and `piece`, now these functions will be implemented differently but they will still respond to the function and give us what we want.

{% codeblock lang:clojure - human.clj %}

(ns ttt-clojure.players.human
  (:require [ttt-clojure.interface.player   :refer :all]
            [ttt-clojure.interface.prompter :refer :all]))

(deftype Human [-piece -prompter]
  Player
  (piece [this]
    -piece)
  (next-move [this gamestate]
    (ask-for-move -prompter gamestate)))

(defn new-human [piece prompter]
  (Human. piece prompter))

{% endcodeblock %}

Here is my Human type, its part of the Player protocol and has 2 functions, piece and next-move, just as we would expect based on the protocol. The piece function just returns the piece that was set when it was created, but next-move takes its prompter and prompts for input in the console.

{% codeblock lang:clojure - computer.clj %}

(ns ttt-clojure.players.computer
  (:require [ttt-clojure.interface.player :refer :all])
  (:use     [ttt-clojure.ai :as ai :only [find-move]]))

(deftype Computer [-piece]
  Player
  (piece [this]
    -piece)
  (next-move [this gamestate]
    (ai/find-move gamestate)))

(defn new-computer [piece]
  (Computer. piece))

{% endcodeblock %}

Computer is different because we cant prompt for input on the console, we have to take a gamestate and figure out what the best move is. So if its the computers turn we can just call next-move on it and it will take the gamestate and figure out what the next move is.

Having a protocol like this is great because now I can have collection of my players, and to get the next move I just pull the first player from the collection and call next-move on it, and it will know what to do.

{% codeblock lang:clojure %}

;; before

(defn get-next-move [gamestate]
  (if (computers-turn? gamestate)
      (find-move gamestate)
      (ask-human-for-move gamestate)))

;; after

(defn get-next-move [gamestate]
  (next-move (-> gamestate :players first) gamestate))

{% endcodeblock %}
