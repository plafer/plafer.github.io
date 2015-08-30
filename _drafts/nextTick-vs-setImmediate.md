---
layout:     post
title:      "Node.js: setImmediate vs. process.nextTick"
subtitle:   "An in-depth analysis of the difference between setImmediate and process.nextTick"
date:       2015-08-20 19:45:00
author:     "Philippe Laferriere"
header-img: "img/post-sample-image.jpg"
---

Understand difference libuv-wise, with event-loop pictures & ...yeah

#########################
# process.nextTick()
#########################
- in src/node.js, process.nextTick is a function which takes callback + args,
creates a tickObject (simple POCO), and pushes that on nextTickQueue
- process._tickCallback is written. All it does is call all the cbs + handling error
- process._tickCallback is called in src/env.cc with Environment::KickNextTick()
- Environment::KickNextTick() is called in src/node.cc's node::MakeCallback()
=> Still trying to figure out where node.cc's MakeCallback is called, and why there
is a MakeCallback in all async-wrap (so all objects)... How does node.js know the current
code is done so that it can call MakeCallback?


QUOTE
- If you want everything to be familiar, you will never learn anything new because it can't be
significantly different from what you already know
	Rich Hickey