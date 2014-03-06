---
layout: post
title: "Deploying Joodo Tic Tac Toe"
date: 2014-03-06 12:41

---
Today I finally got my Joodo Tic Tac Toe deployed after a ton of trouble with Heroku. I dont exactly know why I was running into the issues I was, but Ill walk through what I did while debugging and how I got it deployed.

## Debugging Joodo Deployment
##### This part will be about how I went through debugging my deployment issues, and the next section will be about how I got the app deployed.

Following the guide on the Joodo website:

{% codeblock %}

git init
git add .
git commit -m "Initial Commit"
heroku create --stack cedar
git push heroku master

{% endcodeblock %}

I couldn't get the app to deploy because of an error caused by the Java version heroku was using, the Lein version Heroku was using, and I didn't have a Procfile.

### Set Heroku Java version

  To fix the Java version error you can create a `system.properties` file in the root of your project specifying the Java version your app should use:

  {% codeblock lang:ini %} java.runtime.version=1.7 {% endcodeblock %}

### Set minimum Lein version

  By default Heroku uses `1.7.x` but we want it to use atleast `2.0.0`

  You can fix this by setting it in your `project.clj` like this:

  {% codeblock lang:clojure %} :min-lein-version "2.0.0" {% endcodeblock %}


### Procfile

  The [Procfile](https://devcenter.heroku.com/articles/procfile) can do a lot of things on Heroku, but all we need it to do is tell Heroku how to run our app.

  Add a `Procfile` to the root of your app.

  {% codeblock %}
  web: lein trampoline ring server-headless $PORT
  {% endcodeblock %}

  `trampoline` allows the parent Leiningen process to exit before the projects process starts, so then you only have one JVM running instead of two.

  `server-headless` makes the ring server to start without opening a browser, I dont think this would cause an issue with Heroku, but theres really no point in it trying.

  `$PORT` takes the port Heroku wants the app to run on and tells Lein Ring to run on that port.

### Fix missing dependency

  I then ran into another error where while compiling, it couldn't find the [Fresh](https://github.com/slagyr/fresh) dependency, you can add that to your `project.clj` like I did at line 9:

``` clojure
(defproject ttt-clojure-web "0.1.0-SNAPSHOT"
  :dependencies [[hiccups "0.2.0"]
                 [joodo "2.1.0"]
                 [org.clojars.trptcolin/domina "1.0.2.1"]
                 [org.clojars.trptcolin/shoreleave-remote "0.3.0.1"]
                 [org.clojure/clojure "1.5.1"]
                 [ring-server/ring-server "0.3.1"]
                 [shoreleave/shoreleave-remote-ring "0.3.0" :exclusions [[org.clojure/tools.reader]]]
                 [fresh "1.0.1"]
                 [ttt-clojure "0.1.1-SNAPSHOT"]])
```

After all of that, I finally got Heroku to compile my app and deploy, although thats where the real problem started happening. It was not loading the `ttt-clojure-web.view-helpers` namespace, so the app wouldn't actually run. 

This was strange because while compiling I saw it going through the namespaces and compiling them and it said it successfully compiled `ttt-clojure-web.view-helpers`. 

My next thought was to see if it was actually loaded, so I ran `heroku run lein repl` to connect to my app and check if that namespace existed, and it didn't.

This is where I got really stuck, I spent a few hours trying different things I was reading online but nothing worked. 

I figured it had to do with the Lein Ring plugin not compiling the app properly, so when it was running, portions of the app would be missing.

## Deploying a Joodo app with a WAR file

After reading about compiling Clojure apps to deploy for a while, I came across an article on Heroku about [WAR deployments](https://devcenter.heroku.com/articles/war-deployment) and this is how I got my app deployed.

1. You need to compile a WAR file, Lein ring has this feature built in:

  `lein ring uberwar`

  This will compile a WAR file and put it in your `target/` directory.

  There are also some things you can pass with it as options. I didnt need any of them to get this work work but you can find them [here](https://github.com/weavejester/lein-ring#compiling)

2. Install Heroku Deploy plugin

  You can install it with this if you have the [Heroku toolbelt](https://toolbelt.heroku.com/) installed:

  `heroku plugins:install https://github.com/heroku/heroku-deploy`

3. Deploy!

  `heroku deploy:war --war <path_to_war_file>`

  You can read more about how to deploy WAR files and the options available here:

  [https://devcenter.heroku.com/articles/war-deployment#command-line](https://devcenter.heroku.com/articles/war-deployment#command-line)

I would still like to figure out why specifically my namespaces werent being required when deploying and running with Lein, but this works for now.
