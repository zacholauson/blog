---
layout: post
title: "Octopress on Dokku"
date: 2014-03-05 14:40

---
Today I was playing around with Dokku, which is a 'Docker powered mini Heroku'. I initially wanted to use it to host my Joodo app because I was having issues deploying it on Heroku, but I ended up having issues on Dokku as well.

Even though I didnt get my Joodo app up on Dokku, I still wanted to deploy something on it, so I decided I would put my blog up. I figured it would be as easy as adding the git remote just like Heroku or Github pages, but when I deployed it just gave me a blank page. I found a git repo with someone who forked Octopress to work with Dokku, but since I already had a Octopress setup I didnt want to have to migrate just to test it out. I took a look at what he had done and played around with it and got it working.

First you need to move most of your gems out of development in your `Gemfile`.

Before:
<script src="https://gist.github.com/zacholauson/c55c7e3fc1fce65d052a.js"></script>

After:
<script src="https://gist.github.com/zacholauson/82c48af0c4934bc8804d.js"></script>

Lastly, you need to include Public into git, so just remove it from your `.gitignore`.
<script src="https://gist.github.com/zacholauson/59fd35dffcefd2209810.js"></script>

Now your Octopress setup should work on Dokku.

I didnt end up switching to Dokku for hosting my Blog.
I dont really like how little control over my apps I have on it, but I didn't play with it too much so I may give it another shot later on.