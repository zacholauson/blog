---
layout: post
title: "Environment Aware Database Connections in Clojure"
date: 2014-04-27 11:57
---
This post kind of builds on my last post about [Database Management in Clojure](http://blog.zacholauson.io/blog/2014/04/26/database-management-in-clojure/), but in this one I will expand on how you can setup multiple databases for different environments.

While working on my API side project, I wanted to be able to have a database just for my speclj tests to use, and once I read a bit about [Lein Profiles](https://github.com/technomancy/leiningen/blob/master/doc/PROFILES.md) and how they work, it wasn't too hard to setup.

My goals for this were to be able to define a different database for each environment, and be able to use a different database in each environment without changing any implementation.

In your `project.clj` you define different configurations and there is a profiles map that allows you to define different app behaviors depending on the profile the app is run with.

You can run your app with a specific profile with `lein with-profile <PROFILE> <COMMANDS>`
With this, and knowing a little about how [resources in lein projects](https://groups.google.com/forum/#!topic/clojure/xeAqGyNnj3A) work, we can define our db connections in different profiles with seperate configurations.
I found this cool plugin [carica](https://github.com/sonian/carica) that loads `config.edn` files from your resource path, and allows you to pull the data out of it in your project with `(config :key-from-config-map)`

{% codeblock project.clj lang:clojure %}
(defproject api "0.0.1-SNAPSHOT"
  :description ""
  :dependencies [[org.clojure/clojure "1.5.1"]
                 ...
                 [sonian/carica "1.1.0" :exclusions [[cheshire]]]]
  :profiles {:dev  {:resource-paths ["resources/development"]}
             :test {:dependencies   [[speclj "3.0.1"]]
                    :resource-paths ["resources/test"]}}
  :plugins [[speclj "3.0.1"]
            [lein-ring "0.8.8"]
            [ragtime/ragtime.lein "0.3.6"]]
  :ragtime {:migrations ragtime.sql.files/migrations
            :database "dburl"}
  :test-paths ["spec"])
{% endcodeblock %}

Note: I wasn't able to get rid of the ragtime config if I want to use ragtime to load my migrations, even though I already define that db in `resources/development`

Now we have to create our resources directories and configurations for each.
I created `<project-root>/resources/development/`, `<project-root>/resources/test/` and add a `config.edn` file in each of those directories for carica to load.

{% codeblock development/config.edn lang:clojure %}
{:db {:subprotocol "postgresql"
      :subname     "//localhost:5432/db-development"
      :user        "username"
      :password    "password"}}
{% endcodeblock %}

{% codeblock test/config.edn lang:clojure %}
{:db {:classname "org.h2.Driver"
      :subprotocol "h2"
      :subname "tcp://localhost/test"
      :naming {:keys clojure.string/lower-case
               :fields clojure.string/upper-case}
      :user "test"}}
{% endcodeblock %}

Now that we have our configurations setup, we just need to implement them into our database loading code.

{% codeblock db.clj %}
(ns api.db
  (:require [carica.core          :refer [config]]
            [ragtime.sql.database :refer [->SqlDatabase]]))

(def db (config :db))

(def ragtime-db (merge (->SqlDatabase) db))
{% endcodeblock %}

Now anywhere we use db we will have the proper db connection.

You may have noticed I am using Postgres for my development database and the h2 memory database for the test env. Everything is set up to use those, but an issue I ran into while setting this up was, [Speclj](http://speclj.com/), doesnt run in :test, so it loads my dev db instead of the right one. Turns out its a pretty easy fix because you can define aliases in your `project.clj`.

{% codeblock project.clj lang:clojure %}
(defproject api "0.0.1-SNAPSHOT"
  :description ""
  ...
  :profiles {:dev  {:resource-paths ["resources/development"]}
             :test {:dependencies   [[speclj "3.0.1"]
                                     [com.h2database/h2 "1.3.170"]]
                    :resource-paths ["resources/test"]}}
  :plugins [[speclj "3.0.1"]
            [lein-ring "0.8.8"]
            [ragtime/ragtime.lein "0.3.6"]]

  :aliases {"spec"    ["with-profile" "test" "spec"]
            "spec -a" ["with-profile" "test" "spec -a"]}

  ...)
{% endcodeblock %}

Now with these aliases set, anytime we run `lein spec` or `lein spec -a` it will load the test environment.

