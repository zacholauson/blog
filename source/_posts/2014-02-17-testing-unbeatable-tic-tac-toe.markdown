---
layout: post
title: "Testing Unbeatable Tic Tac Toe"
date: 2014-02-17 09:00

---
Over the past two weeks I have been working on Tic Tac Toe in Clojure. You can view my whole repo [here](https://github.com/zacholauson/tictactoe-clojure) but I thought I would explain how I tested if my Ai was actually unbeatable.

{% codeblock lang:clojure %}
(defn playout-every-game [gamestate]
  ;; Here I check if its the computers turn.
  ;; It should always be the computers turn at this point,
  ;; if its not the function will just return nil
  (if (computers-turn? gamestate)
    ;; Now we take the gamestate given to us and have the Ai decide on its first move
    ;; then move there.
    (let [new-gamestate (move gamestate (find-move gamestate))]
         ;; If the new game results in a win for the computer or a tie it will return true
         (if (or (win? new-gamestate (:computer new-gamestate)) (tied? new-gamestate)) true
             (map (fn [possible-move]
                  ;; If the game isnt over it creates a new gamestate for every available move
                  (let [newer-gamestate (move new-gamestate possible-move)]
                       (cond
                         ;; If the last move results in a computer win, it returns true
                         (win? newer-gamestate (:computer newer-gamestate)) true
                         ;; If the last move results in a win for the human,
                         ;; it returns a vector with the gamestate that allowed the human to win and false
                         (win? newer-gamestate (human-mark newer-gamestate)) [newer-gamestate false]
                         ;; If the game isn't over it will pass the newest gamestate back
                         ;; through the function until every gamestate has been played.
                         :else (playout-every-game newer-gamestate))))
             (possible-moves new-gamestate))))))
{% endcodeblock %}

If I run `(playout-every-game {:board [:- :- :- :- :- :- :- :- :-] :computer :x})`
it will return a bunch of nested lists to represent the wins and loses
of all of the gamestates played, something like this:
`(((true (true true) true true)))` but many more lists.
Then we can call distinct on it, which will return a lazy sequence with duplicates removed (like uniq in Ruby),
so it should return `(true)` but if the human did win in a gamestate it would return `(true false)`.
Then we check that everything within the lazy sequence is true

{% codeblock lang:clojure %}
(describe "minimax"
  (it "when playing out every possible game they should all return true for win or tie when computer goes first"
    (should= true (every? true? (distinct (flatten (playout-every-game {:board [:- :- :- :- :- :- :- :- :-] :computer :x}))))))
  (it "when playing out every possible game they should all return true for win or tie when human goes first"
    (should= true (every? true? (distinct (flatten (playout-every-game {:board [:- :- :- :- :- :- :- :- :-] :computer :o})))))))
{% endcodeblock %}
