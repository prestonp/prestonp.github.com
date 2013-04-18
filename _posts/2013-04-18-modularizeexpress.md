---
layout: post
title: Modularize Express Screencast by TJ Holowaychuk
category: posts
---

They say hindsight is 20/20.

Mannnnnnnn!!! One of the great things about express is how
liberal it is when it comes to structuring your app. Unfortunately,
one of the worst things about express is how liberal it is when it 
comes to structuring your app.

After stumbling around this past week or so, nailing down the design
of edibls has been tougher than I expected. I found a great screencast
of how to modularize apps using express' app mounting feature.

[Modular web applications with Node.js and Express](http://vimeo.com/56166857)

In a nutshell, you can design fragments of your app in tiny express() instances
that handle their own routing, views, and module specific middleware. 

I'm most likely going to set up a branch to refactor the edibls codebase.
But I think the investment of a well designed app will pay itself later on..
however, buyers beware: 
[analysis paralysis](http://en.wikipedia.org/wiki/Analysis_paralysis)
