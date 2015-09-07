---
layout:     post
title:      "Node.js: setImmediate vs. process.nextTick"
subtitle:   "An in-depth analysis of the difference between setImmediate and process.nextTick"
date:       2015-08-20 19:45:00
author:     "Philippe Laferriere"
header-img: "img/post-nodejs.jpg"
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
- All calls from the libuv wraps (e.g. tcp_wrap's onconnection) to js use 
AsyncWrap's MakeCallback - once all the js code to run runs, the tick ends.
- All js entry points have an end tick after JS runs
	-> end of main program in Module.runMain
	-> every time new JS is run, proc'ed by event loop


#########################
# setImmediate()
#########################
In setImmediate...
1- Append Immediate object, where callback is store in immediate._onImmediate, to immediateQueue.
2- If process._needImmediateCallback is false, set it to true. This property's setter will 
uv_check_start() (along with a uv_idle_start just to make the loop not poll for I/O).
3- The check handle's callback calls node.cc's MakeCallback(env, env->process_object(), env->immediate_callback_string()).
4- The callback calls all queue setImmediate and sets process._needImmediateCallback to 
false, unless another setImmediate was queued in the callbacks.

** setImmediate ends a tick (in step 3). 


QUOTE
- If you want everything to be familiar, you will never learn anything new because it can't be
significantly different from what you already know
	Rich Hickey