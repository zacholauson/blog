---
layout: post
title: "Database Management in Clojure"
date: 2014-04-26 19:32

---
Today I started working on a side project that I wanted to create an API for. I decided it would be fun to make the API in Clojure as its one of my favorite languages right now.
Up until today I had not written a database backed Clojure app so it took me a while to figure out the best way to go about it.

There are many Database managment libraries for Clojure, I tried [Lobos](https://github.com/budu/lobos), and [Ragtime](https://github.com/weavejester/ragtime).

### Lobos
I spent a few hours working with Lobos, and while it seems to do db migrations and connections pretty well, it seems like it just has so much stuff in it.
In your Clojure app you create a Lobos direction in `src/`, that is the home to all of Lobos' configuration, you have the db connection configuration, any helpers that you wish to use with lobos and my least favorite part, your migrations.
The migrations that Lobos uses are written in their own DSL that all live in a single `migrations.clj`, and while you can split them up into separate files, you still have to require them in your `migrations.clj`. I didn't like this, so I moved on to Ragtime.

### Ragtime
Ragtime is pretty basic, it just handles your database connection and running your migrations, it was really easy to setup and it includes pretty much everything that I wanted, the only thing I think might be missing from Ragtime is a sql migration file generator, but I wrote one up pretty quick in a shell script.

{% codeblock lang:sh %}
#!/bin/bash

MIGRATIONS_DIR=.
MIGRATION_NAME=$1
TIMESTAMP=`date +%Y%m%d%H%M%S`
UNDERSCORE="_"
UP_MIGRATION_FILE="$MIGRATIONS_DIR/$TIMESTAMP$UNDERSCORE$MIGRATION_NAME.up.sql"
DOWN_MIGRATION_FILE="$MIGRATIONS_DIR/$TIMESTAMP$UNDERSCORE$MIGRATION_NAME.down.sql"

touch $UP_MIGRATION_FILE
touch $DOWN_MIGRATION_FILE

echo "created $UP_MIGRATION_FILE"
echo "created $DOWN_MIGRATION_FILE"
{% endcodeblock %}

Ragtime lets you specify the type of migrations that you want to use, I wanted to use my sql files, so I just popped this into my `project.clj` and I was pretty much ready to go.

{% codeblock lang:clojure %}
:ragtime {:migrations ragtime.sql.files/migrations
:database "jdbc:postgresql://localhost:5432/database?user=username&password=password"}
{% endcodeblock %}

### clj-sql-up
I also tried [clj-sql-up](https://github.com/ckuttruff/clj-sql-up), but I after not being able to get it working in about an hour, I just moved on.

---

After trying out these 3 libraries, I'm going with Ragtime for this API, because of its ease of setup, and it has all of the functionality that I need, so it suits my needs for this API pretty well I think.
