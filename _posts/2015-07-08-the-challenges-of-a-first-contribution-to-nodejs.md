---
layout:     post
title:      "The challenges of a first contribution to node.js"
subtitle:   "Getting involved in a new open-source project is not without challenges. I share my experience in the matter."
date:       2015-07-08 17:45:00
author:     "Philippe Laferriere"
header-img: "img/post-bg-01.jpg"
---

Joining an open-source project is not easy, especially if it's your first time.
The codebase is more often than not modularized in over 50 files, each 
containing up to a few thousand lines of code. Getting up to speed requires 
not only commitment, but also a structured working method. I describe
my experience in joining node.js.

## Walkthrough
I got interested in the project two months ago at the time of 
writing. I didn't know C++, and I knew basic JavaScript - enough to
work with the DOM, and that's about it. However, my goal was clear:
I wanted to understand how node.js worked from the ground up.

I started with getting up to speed with node.js at a high level. I read some
[blogs](http://www.toptal.com/nodejs/why-the-hell-would-i-use-node-js) 
about it, [stackoverflow answers](http://stackoverflow.com/a/14797359/3499862) and
watched Ryan Dahl talk about the [history of the project](https://www.youtube.com/watch?v=SAc0vQCC6UQ).
My goal was to know what node.js was *before* even thinking about getting
into studying the source code. This may sound blatantly obvious, but
I used to mistakenly think the more code I could possibly gulp, the better.
This used to make understanding the code a lot harder
for at least two reasons. In the first place, you don't get valuable information out of
variable names which often refer to a term of an underlying concept. Normally,
you would be able to predict where and how the variable is going to be used; but you don't.
Additionally, you can't be critical about what you're reading; how can you 
challenge the author on a code segment if you don't understand what they're trying to do?
Also, you won't get those *Right, here we need to change the state of the stream from paused to flowing since
a new data listener was added...* moments. 

Once I was familiar with the general concepts, I learned C++ along with the two
main libraries used in node.js, [libuv](http://docs.libuv.org/en/v1.x/) and
[Google's V8](https://developers.google.com/v8/intro). This allowed
me to start figuring out how the C++ part of the project works. My strategy was
a two-part one. First, start from the *main* method and work my way through the code
until I feel comfortable enough to go ahead and pick an [issue](https://github.com/joyent/node/issues)
to work on. Then... pick an issue and start working on it.

However, once I got to the second part, I realised *pretty much every issue was in the 
JavaScript part*. So I went on and followed a 
[thorough tutorial on ECMAScript](http://dmitrysoshnikov.com/ecmascript/chapter-1-execution-contexts/).
Even though the tutorial went in great detail, I have plans to read the ECMAScript-262
standard at some point in the future to make sure not to miss out on any detail.
All in all, this allowed me to carry on.

The [first issue I picked](https://github.com/joyent/node/issues/7581) was
about a peculiar behavior of the socket under an http connection.
I spent 70% studying the stream module, 25% net, and 5% http, since the problem
was likely to be in the underlying stream, but I also needed to see
how the socket interacted with the stream, and a bit of how http worked with
the socket. Turns out I didn't even find the answer in the code - I realised
the behavior in question was documented; I dropped a comment on the issue's thread 
and it got closed. Even though I didn't need to write any code, it didn't
feel like a waste of time at all because *it made me understand how streams work
very well*, let alone bits and pieces of the net and http modules.

The [next issue I chose to work on](https://github.com/joyent/node/issues/7273)
was related to *(drum roll...)* the stream module. The documentation of the *read()*
method was misleading with respect to how the stream handles the size argument. 
I happily wrote the fix. The thing is, I didn't blindly update the docs;
*I understood the change I was doing*, which I found very encouraging.

And... *That's all Folks*! At the time of writing, I'm working on my first *code contribution*, and
I'm having a whole lot of fun doing it.

## An interesting tool I used
To help me speed up my learning, I used [Anki](http://ankisrs.net/). This small
application simulates cards; on one side you write a question, and
on the other, the answer. It first shows the question, and when you're
ready, shows the answer. You then indicate whether or not you knew how to answer the question 
before the other side of the card was shown. Questions that were indicated as *hard* will be asked
again in a short amount of time, while *easy* questions will only be asked at a much later time.

I would ask myself questions like *In libuv, what are check handles?* or *In node.cc, what does
SetupProcessObject() do?*. The goal is to save time by knowing what most of the methods
do, understanding how the code is seperated in the files (i.e. what to find where), 
and being familiar with some general concepts. That way, you don't have to go look 
everything up when, for example, you encounter a method call; you just know what it does.
To top it all off, it doesn't cost a lot of time to review the cards since
you normally study them in your free time.

## A final note
I'd like to take a moment and stress just how much fun I'm having doing all this!
I am finally quenching my thirst for going down to a lower level of abstraction 
of what I am used to. Eric Lippert 
[presents the same idea](http://ericlippert.com/2015/06/04/its-not-magic/) using an anecdote.
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
<div style="margin-top:30px;" class="fb-like" data-href="http://plafer.github.io/2015/07/08/the-challenges-of-a-first-contribution-to-nodejs/" data-layout="standard" data-action="like" data-show-faces="true" data-share="true"></div>