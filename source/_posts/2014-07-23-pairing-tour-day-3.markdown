---
layout: post
title: "Pairing Tour: Day 3"
date: 2014-07-23
---
Today I paired with Malcolm for the third day of my Pairing Tour. I was able to visit him at a client site, which was a cool experience, as I got to meet some of the people he works with every day. For the first hour or so he showed me around the app, so I could get a better understanding of what he was working on and how things went together.

The task for the day was to work on tests he and Elizabeth, another craftsmen from 8th Light, started writing yesterday. The first couple of tests came pretty easily, but we ran into some issues with some objects not saving. We found out that we could just set an associated object, and save it to get the proper object that we wanted. Unfortunatly, this made our test time skyrocket. When we saved the object, it didn't just save that object. It created a large save-chain, that took about 40 seconds to complete. 

I didn't expect this to be a huge issue. The spec suite in general took a long time, but unfortunatly `before :all` wouldn't work to only build this object once. This meant, for every test we wrote, another 40 seconds were added to the suite run time. We decided to push ahead and finish the rest of the specs, which didn't take too long. When we were done, the spec suite ended up taking more than 13 minutes to complete. We didn't figure out a good solution to combat the crazy slow tests by the end of the day. I really wish I could have worked on the tests some more to optimize them, but I'm not on that client app. I obviously understand why I can't keep working on it, but it kind of bums me out that I can't continue to work on solving that issue.