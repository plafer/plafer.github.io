---
layout:     post
title:      "The challenges of a first contribution to node.js"
subtitle:   "Getting involved in a new open-source project is not without challenges. I share my experience in the matter."
date:       2015-07-02 12:00:00
author:     "Philippe Laferriere"
header-img: "img/post-bg-01.jpg"
---

Joining an open-source project is not easy, especially if it's your first.
The codebase is more often than not modularized in over 50 files, each 
containing up to a few thousand lines of code. Getting up to speed requires 
not only commitment, but also a structured working method. I describe
my experience in integrating with the node.js open-source project.

## Walkthrough
I got interested in the project two months ago at the time of 
writing. I didn't know C++, and I knew basic JavaScript - enough to
work with the DOM, and that's about it. However, my goal was clear:
I wanted to understand how node.js worked from the ground up.

I started with getting up to speed with node.js at a high level. I read some
[blogs](http://www.toptal.com/nodejs/why-the-hell-would-i-use-node-js) 
about it, [stackoverflow answers](http://stackoverflow.com/a/14797359/3499862) and
watched Ryan Dahl talk about the [history of the project](https://www.youtube.com/watch?v=SAc0vQCC6UQ).
My goal was to know what node.js did *before* even thinking about getting
into studying the source code. This may sound blatantly obvious, but
I used to skip this step when reading other people's code even though I wouldn't
be familiar with the underlying concepts. This made understanding the code a lot harder
for at least two reasons. In the first place, you don't get valuable information out of
variable names which often refer to a term of an underlying concept. Normally,
you would be able to predict where and how the variable is going to be used; but you don't.
In addition, you can't be critical about what you're reading; how can you 
challenge the author if you don't understand what he's trying to do? Also, you won't get those
*Right, here we need to change the state of the stream from paused to flowing since
a new data listener was added...* moments. 

Once I was familiar with the general concepts, I learned C++ along with the two
main libraries used in node.js, [libuv](http://docs.libuv.org/en/v1.x/) and
[Google's V8 engine](https://developers.google.com/v8/intro). This allowed
me to start figuring out how the C++ part of the project works. My strategy was
a two-part one. First, start from the *main* method and work my way through the code
until I'm comfortable to pick an [issue](https://github.com/joyent/node/issues)
and start working on it. Then... pick an issue and start working on it.
That's exactly what I did, but when I got to the second part, I realised
that *pretty much every issue was in the JavaScript part*.

So I went on and followed a 
[thorough tutorial on ECMAScript](http://dmitrysoshnikov.com/ecmascript/chapter-1-execution-contexts/).
Even though the tutorial went in great detail, I have plans to read the ECMAScript-262
standard at some point in the future to make sure not to miss out on any detail.
All in all, this allowed me to carry on with my plan.

The [first issue I picked](https://github.com/joyent/node/issues/7581) was
concerning a peculiar behavior of the socket under an http connection.
I spent 70% studying the stream module, 25% net, and 5% http, since the problem
was likely to be in the underlying stream, but I also needed to see
how the socket worked with the stream, and a bit of how http worked with
the socket. Turns out I didn't even find the answer in the code - I realised
the behavior was documented; I commented on the issue and it got closed.
Even though no actual code was needed on my part, it didn't feel like a
waste of time at all because *it made me understand how streams work
very well*, let alone the net and http modules.

The [next issue I chose to work on](https://github.com/joyent/node/issues/7273)
was related to *(drum roll...)* the stream module. A clarification was
needed in the documentation of the *read* method, which I provided. The thing is,
I didn't blindly update the docs; *I understood the change I was doing*,
which I found very encouraging.

At the time of writing, I'm working on my first *code contribution*, and
I'm having a whole lot of fun.

## An interesting tool I used
To help me speed up my learning, I used [Anki](http://ankisrs.net/). This small
application simulates cards; on one side you write a question, and
on the other, the answer. It first shows you the question, and when you're
ready, shows you the answer. You then tell it whether or not you knew the
answer before it was shown. Questions that you indicated as *hard* will be asked
in a short amount of time, while *easy* questions will only be asked at a much later time.
I would ask questions like *In libuv, what are check handles?* or *In node.cc, what does
SetupProcessObject() do?*. The goal is to save time by knowing what most of the methods
do, understanding how the code is seperated in the files (i.e. what to find where), 
and being familiar with some general concepts. That way, you don't have to go look 
everything up when, for example, you encounter a method call; you just know it.
To top it all off, it's not like it costs a lot of time to review the cards;
you just study them in your free time.

## A final note
I'd like to take a moment and stress just how much fun I'm having doing all this!
I am finally quenching my thirst for going down to a lower level of abstraction 
of what I am used to. Eric Lippert 
[presents the same idea](http://ericlippert.com/2015/06/04/its-not-magic/) with an anecdote.
He tells about a candidate he once interviewed; he was asking the interviewee about how he
would go about writing code in a database implementation that auto-generates the
primary key on a table.

> The candidate was completely taken aback, and just stared at me for a moment before
> saying “wow, I never before thought about the fact that someone had to write code
> that does that.” Apparently in his world creating primary keys is done by the primary
> key pixies who live in the b-tree forest. And it turns that out a lot of people think
> that GUIDs are generated by the GUID goblins, that random numbers are created by the
> RNG ogres, and so on.


I am aware that for system programmers, node.js is not *that low* down the levels of
abstraction; but hey, *you gotta start somewhere*!


<!-- Facebook like & share-->
<div style="margin-top:30px;" class="fb-like" data-href="http://plafer.github.io/2015/07/05/the-challenges-of-contributing-to-nodejs/" data-layout="standard" data-action="like" data-show-faces="true" data-share="true"></div>