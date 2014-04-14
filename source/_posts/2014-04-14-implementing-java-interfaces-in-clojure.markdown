---
layout: post
title: "Implementing Java Interfaces in Clojure"
date: 2014-04-14 15:04

---
Currently I am working on using my Java server to serve games of tic tac toe from my tic tac toe written in Clojure.

On Friday I paired with Sandro on trying to figure out how to create a Clojure object that implmented a Java interface. I started with trying to use gen-class which I really could not figure out how it worked, but Sandro showed me I could use the java interface just like any Clojure protocol.

In my server I have this interface that is the base of my routing called `IRoute`
Basically the IRoute interface just says, anything that implements this, has to be able to return an IResponse when `buildResponse(request response)` is called on it.

In my tic tac toe game I wanted to make a route that implemented this interface but instead of showing a file or something, create a new gamestate and save it in the cookies.

{% codeblock lang:java IRoute.java %}
package main.routing.routes;

import main.requests.IRequest;
import main.response.IResponse;

public interface IRoute {
    public IResponse buildResponse(IRequest request, IResponse response);
}
{% endcodeblock %}

{% codeblock lang:clojure new_game_route.clj %}
(ns ttt-clj-java-server.routing.routes.new-game-route
  (:require [ttt-clojure.gamestate :refer [create-board] :as gamestate]
            [ttt-clj-java-server.responses.response :refer :all]))

(defn board-string [board]
  (->> board
       (map name)
       (apply str)))

(defn build-response [request response row-size]
  (set-status response 301)
  (add-header response "Location" "/play")
  (add-cookie response "computer" "x")
  (add-cookie response "human"    "o")
  (add-cookie response "board"    (board-string (create-board 3)))
  response)

(defrecord NewGameRoute [row-size]
  main.routing.routes.IRoute
  (buildResponse [this request response]
    (build-response request response row-size)))

(defn new-game-route [row-size]
  (NewGameRoute. row-size))
{% endcodeblock %}

Now when I extend my routes and add this new route in the server, it treats it just like it would treat the routes I have built in my server.
