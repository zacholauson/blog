---
layout: post
title: "Different Board Sizes"
date: 2014-02-24 19:59
comments: true
categories:

---

This morning I worked on putting together code that would generate the winning lines for the board size of the current gamestate. This was kinda challenging because I originally did this in my Ruby version of Tic Tac Toe but Clojure doesnt have a few of the functions that made that easy in Ruby so I got to recreate them, which was pretty fun. 

Getting the rows and columns was pretty easy
using partition and mapv (like Ruby's transpose from what I understand).

``` clojure
(defn rows [board-size]
  (partition (math/sqrt board-size) (range board-size)))

(defn columns [board-size]
  (apply mapv vector (rows board-size)))
```

The more challenging part came when I had to get the diagonals
I decided to recreate the step method from Ruby
to get the diagonal indexes starting from the left

``` clojure
(defn step [step-num range-vec]
  (loop [range-vec range-vec
         return-col []]
    (if (> (count range-vec) 0)
      (let [return-col (conj return-col (first range-vec))]
        (recur (drop step-num range-vec) return-col))
       return-col)))
```

For the 'right diagonal' I basically just did the reverse

``` clojure

(defn right-diag [board-size]
  (let [range-vec (range board-size)
        step-num (- (math/sqrt board-size) 1)]
    (loop [range-vec range-vec
           return-col []]
      (if (< (+ (first range-vec) (+ step-num 1)) board-size)
        (let [return-col (conj return-col (+ (first range-vec) step-num))]
          (recur (drop step-num range-vec) return-col))
        return-col))))

(def calculate-winning-positions
  (memoize
    (fn [board-size]
      (let [row-size (math/sqrt board-size)]
```

This is my favorite part of the function, 
  I had been seeing this ->> macro all over but I didnt quite understand
  how it was used, but this turned out to
  be a perfect place to learn about how it worked.

Basically you start out the macro with a initial value,
  in this case I started with the right diagonal value,
  then for every thing underneath it, it takes the value from the
  function above and passes it to the next function.

So in this case the right diagonal will result in:
[2 4 6] for a 3x3 board

``` clojure

(->> (right-diag board-size)
    ;; then it passes the [2 4 6] from the function above to the next function
    ;; so this is really
    ;;   (concat (step (+ row-size 1) (range board-size)) [2 4 6])
    (concat (step (+ row-size 1) (range board-size)))
    ;; and just keeps doing this all the way down.
    (concat (columns board-size))
    (concat (rows board-size))
    (flatten)
    (partition row-size))))))

```

Originally I was just passing the function a gamestate but it really slowed down my app and my tests.
I did some reading on memoization and realized that if I passed the function the board size instead of the unique gamestate that I could cache the winning positions for the game and really cut back on the time it took to execute.

So I change the argument `calculate-winning-positions` to accept a board size instead of the gamestate and then wrapped it in memoize.

``` clojure
(defn winning-positions [gamestate]
    (calculate-winning-positions (count (:board gamestate))))
```

The time this cut down kinda blew me away, I was able to run all my unit tests in ~ 0.02 seconds.
