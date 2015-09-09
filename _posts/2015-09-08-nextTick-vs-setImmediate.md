---
layout:     post
title:      "Node.js: setImmediate vs. process.nextTick"
subtitle:   "An in-depth analysis of the difference between setImmediate and process.nextTick"
date:       2015-09-08 22:45:00
author:     "Philippe Laferriere"
header-img: "img/post-nodejs.jpg"
---
>If you want everything to be familiar, you will never learn anything new because it can't be
>significantly different from what you already know.<br>
><span style="float:right;">Rich Hickey</span>

Node.js offers many options to postpone code execution until *later*. Two of these,
`setImmediate` and `process.nextTick`, are similar in the way that they make the code run
*soon*... Pretty unclear, *heh*? Let's get right to it.

## SetImmediate
Anyone who is the least bit familiar with node.js has heard of the event loop, a
[magical place filled with unicorns and rainbows](https://nodesource.com/blog/understanding-the-nodejs-event-loop).
Even though it can seem daunting at first, it is quite a simple concept. 

![The event loop](/img/event-loop.png "The Event Loop")

In a nutshell, the event loop is a sequence of actions that run until an end 
condition is met. *Handles* of different types are registered on the loop with an associated
callback. Particularly, *check* handles will be of interest to us: their associated callback 
function runs once per loop iteration, right after the loop polls for I/O.

Now, let's look at how `setImmediate` works. Consider the following snippet.
    
    setImmediate(function() {
	  console.log('Print this immediately!');
    });

First of all, the `callback` function gets internally queued. Next, a *check* handle
is registered on the event loop; its associated callback is a simple function that runs 
all the queued `callback` functions. Therefore, whenever the loop
hits the *check* handles part, the internal queue is emptied and all the `callback`s are executed.

Consequently, the whole point of `setImmediate` is to *postpone the execution of code until
immediately after polling for I/O*. Note that this definition does not take into account 
where we currently are in the event loop. Thus, it is not guaranteed that a `setImmediate` will
run the callback on the current loop iteration. However, since *check* handles' `callback`s are the
second to last to run right before close callbacks, it is safe to assume that *just about* all `callback`s
will be executed in the current loop iteration. A good rule of thumb is: "setImmediate callbacks 
will be the last to run on the current event loop iteration". 

## Process.nextTick
In a like manner, let's examine how `process.nextTick` works:
it schedules a function to be executed when the current *tick* ends. However, in order to dig deeper,
we first need to be familiar with one of node's central C++ function, `MakeCallback`, which takes 
as parameter a JavaScript function along with its arguments and its calling object (*i.e.* the 
value of `this` inside the function). Essentially, its process boils down to two things: 

1. Execute the JavaScript function.
2. End the *tick*.

The reason we say it's central to node is that *every JavaScript `callback`
gets executed using `MakeCallback` whenever it comes off the event loop*. For example,
when a TCP server receives a new connection, the `connectionListener` gets executed using 
`MakeCallback`. Therefore, a *tick* comprises all the code that runs in response to an 
event fired from the event loop - the only exception to this is the first *tick*, which is 
started when the program initially runs.

## Putting it all together
The major difference between `setImmediate` and `process.nextTick` is 
that `setImmediate` queues its callbacks on the event loop while `process.nextTick`
doesn't. Let's look at an example which uses both of them.

	console.log('Program started.');

	setImmediate(function() {
	  console.log('in immediate.');
	  // another tick ends here
	});

    process.nextTick(function() {
      console.log('in nextTick.');
      // no tick ends here - another process.nextTick
      // would just append to the current tick's queue
    });

	console.log('end of first tick.');
	// first tick ends here

    Output:
    Program started.
    end of first tick.
    in nextTick.
    in immediate.

Since `process.nextTick` callbacks execute before going back to the event loop,
*they run before `setImmediate` callbacks*. And when `setImmediate` callbacks get
fired from the event loop using `MakeCallback`, *they end a tick*!