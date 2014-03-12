---
layout: post
title: "Broken CtrlP"
date: 2014-03-12 11:22

---

I don't use the Vim plugin, CtrlP, too often, but this morning I realized it wasnt working. I tried forcing the default mapping and looking through the docs, but couldnt figure it out. I eventually started working through other plugins to see what was conflicting and found that a plugin that I recently installed, GoldenView, was overwriting my mapping.

I thought it should be able to force CtrlP to use `<C-p>` even though GoldenView was using it, but it wasn't sticking, I ended up just re-ordering my vim bundles so that CtrlP gets loaded after GoldenView.

I also may get rid of GoldenView, even though it is pretty cool, I'm not sure if its cool enough to keep around in my Bundles as I like to have as few plugins as possible so Vim doesnt get bogged down.
