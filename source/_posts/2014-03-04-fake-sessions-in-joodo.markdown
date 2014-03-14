---
layout: post
title: "Fake Sessions in Joodo"
date: 2014-03-04 14:23

---
This week I have been working on using my command line Tic Tac Toe as an engine for a Joodo app.
Yesterday I made pretty good progress and got basic gameplay working. Today I have been mostly working on the front end of the app.

While testing I was having trouble testing any part of my controller because to play the game, you have to have a gamestate in your session so the controller can pull that from the request.

While learning about Joodo, I have been mostly working through the sample app tutorial to see how they do things because I dont really know what is available to me. This was an issue for me because in the sample app they dont use the request, they just use the response from a `do-get` or `do-post`.

After reading through the source a bit I found you could send direct requests through instead, which is part of the request macro in the [controller helper](https://github.com/slagyr/joodo/blob/master/joodo/src/joodo/spec_helpers/controller.clj#L22-L30).

For example, I want to test, when I post to `/move` that the controller takes the gamestate from the session and makes my move for me.

First I create a new gamestate to pass to the request.

{% codeblock lang:clojure %}

(def new-gamestate {:board [:- :- :- :- :- :- :- :- :-],
                    :computer :o,
                    :players [(new-human :x nil) (new-computer :o)]
                    :options {:difficulty :unbeatable}})

{% endcodeblock %}

Then I can build up the request with my chosen move as a param.

{% codeblock lang:clojure %}
(request :post "/move" :session {:gamestate new-gamestate} :params {:move "0"})
{% endcodeblock %}

This will post to the server and give me the full request back so I can test that when the human posts to `/move`
the controller will parse that move and put the humans piece on the board, then if the game is not over, the controller has the comptuer make a move and sets the new gamestate to the session.

{% codeblock lang:clojure %}

(def new-gamestate {:board [:- :- :- :- :- :- :- :- :-],
                    :computer :o,
                    :players [(new-human :x nil) (new-computer :o)]
                    :options {:difficulty :unbeatable}})

(describe "game-controller"
  (describe "routes"
    (it "handles /move and moves the gamestate to the humans chosen move"
      (let [result (request :post "/move" :session {:gamestate new-gamestate} :params {:move "0"})]
        (should= [:x :o :- :- :- :- :- :- :-] (-> result :session :gamestate :board))))))

{% endcodeblock %}
