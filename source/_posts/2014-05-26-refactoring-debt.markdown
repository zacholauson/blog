---
layout: post
title: "Refactoring Debt"
date: 2014-05-26 14:18

---

I am currently reading Refactoring by Martin Fowler, and he is talking about when you shouldn't refactor. He quotes Ward Cunningham's thought on unfinished refactoring.
Its like going into debt. Most companies need some debt in order to function efficiently, but that debt comes with interest payments, which if it becomes too great, it can be overwhelming.
I think this is a great way of thinking of this and recently ran into issues almost exactly like this.

I was working on this project recently that was using the repository pattern. It had a memory repository and an ActiveRecord repository.
When I started on the project everything was using the ActiveRecord Repository, even specs, which I thought was weird, but I didnt have time to get the memory repository up to date with the ActiveRecord repository so they could be substituted.
Now ~4-5 weeks later the memory repository is even farther behind. There is a overwhelming amount of debt associated with having substitable repositories, now I'm not sure if 4-5 weeks ago it would have been a managable amount of debt, but there would have been less.
I believe that if we are going to have a memory repository in the app, it should be kept up to date at all times so we can use it to run specs, I would guess that running specs on the memory repository would atleast cut test times in half.
I think this is a refactor that should happen, but it has gotten to the point where I almost feel that the memory repository and possibly the ActiveRecord repository will have to be rewritten to make sure they mirror each other.
Maybe it wont have to be re-written but I know this is a huge refactor, because half of the current specs fail when you use the memory repository over the active record repository.

I'm not really on that project anymore, but I am still playing with the idea of getting the memory repository up to the ActiveRecord repository's level.
I'm experimenting with using Surrogate to inforce a common interface between the repositories but I am running into some issues that I haven't been able to solve yet.
