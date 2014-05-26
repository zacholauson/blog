---
layout: post
title: "3D Modeling in Clojure"
date: 2014-05-26 11:33

---
I recently found this cool Clojure library called [Scad-clj](https://github.com/farrellm/scad-clj), that converts Clojure code to OpenSCAD, to generate 3D models.
I dont really know anything about 3D modeling but I think this is pretty awesome because I really like writing Clojure and making visual things with code is pretty awesome.

Scad-clj allows you to write `.scad` files to be read by the [OpenSCAD](http://www.openscad.org/downloads.html) app, which is what renders the models. The app is really cool because you can set it to watch for file changes and compile when something changes.

Ive mostly been working through the repl so I dont have to run the app everytime I make a change.

To get started you have to include the `Scad-clj` dependency to the project.clj.

` [scad-clj "0.2.1"] `

Then if you pop open your repl you can get started.

I start by defining a function that takes a shape and writes it to my scad file for openscad to read, you will need to require 2 namespaces from Scad-clj, `model` for all modeling and `scad` to write the models to a file.

{% codeblock lang:clojure %}

(use 'scad-clj.model)
(use 'scad-clj.scad)

(defn print-scad [shape]
 (spit "filename.scad"
  (write-scad shape)))

{% endcodeblock %}

Once you have that setup you can start making some models, there are 3 primitives you can make with Scad-clj, sphere, cube, and cylinder.

We can start with a cube, if you look at the source for the [cube function](https://github.com/farrellm/scad-clj/blob/master/src/scad_clj/model.clj#L63-L64),
you can see that it takes 3 arguments, height, width, and depth.

{% codeblock lang:clojure %}
(print-scad (cube 100 100 100))
{% endcodeblock %}

This will write to our `filename.scad` file and if we open that with openscad, we will see something like this:

{% img http://i.imgur.com/tp3yeiR.png %}

There are a couple basic functions that are used to make models interact with each other.

The one I have used the most is Union, which is just a function that takes a group of model functions and then combines them.
With union we can build stuff like this:

{% codeblock lang:clojure %}
(print-scad
  (union
    (cube 100 200 100)
    (translate  [0 50 -25] (cylinder 25 100))
    (translate [0 -50 -25] (cylinder 25 100))))
{% endcodeblock %}

{% img http://i.imgur.com/ZxJKbCk.png %}

There currently is no documentation for Scad-clj, so you will just have to look at the source, but I have found that the [OpenSCAD documentation](http://en.wikibooks.org/wiki/OpenSCAD_User_Manual) is pretty helpful.

