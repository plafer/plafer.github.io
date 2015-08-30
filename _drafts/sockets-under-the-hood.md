---
layout:     post
title:      "Node.js: Sockets under the hood"
subtitle:   "An overview of how sockets are implemented in node.js"
date:       2015-08-05 17:45:00
author:     "Philippe Laferriere"
header-img: "img/post-sample-image.jpg"
---

Idea: Describe how sockets work under the hood using duplex stream
	-> write, get written to event loop, on read, bubble up to js, etc etc
	-> They don't share the same internal buffer
	-> Include diagram of the loop
	-> in intro, "This will probably not help you directly write better node.js programs... I'm writing this for the curious"
	-> Duplex stream: even though it supports reading & writing, they are two seperate entities put under one interface. We will explore them as such (two seperate entities). 