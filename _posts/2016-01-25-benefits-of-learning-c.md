---
layout:     post
title:      "Benefits of learning C after Java"
subtitle:   "A new look on programming"
date:       2016-01-25 17:45:00
author:     "Philippe Laferriere"
header-img: "img/post-c.jpg"
---

For most of my life, I've used computers for basic everyday things:
write papers for school, browse the internet, play games,
etc. Although I was very comfortable with the user interface provided
by Windows and its various applications like most kids born in the
'90s, I didn't have a clue of what was going on under the hood. When I
got to choose a university program two years ago, I decided to enroll
in software engineering by pure fascination of how computers work and
how to manipulate them with what they called programming languages.

Why am I rambling about all of this, you ask? Bear with me, we'll get
there.

Like most other computer science undergrad in North America, I was
taught Java as a first programming language. There's been some
discussion as to if this is a good or bad thing. However, whether Java
should or shouldn't be taught as a first programming language, or
whether universities should or shouldn't include C in their curriculum
is not the purpose of this article. Instead, it will focus on the
benefits of learning C complementary to the concepts acquired from
learning Java.

As I got comfortable with Java, I realized that it was too abstract of
a language to use as a tool to learn how computers work under the
hood. I wanted to play with *bytes*. So I went on to learn C, and
*wow*, this has been my greatest computer revelation up to this point,
followed by my operating systems course.

Still reading? Good. But before we continue, I'll introduce some
related discussion that is taking place here and there on the internet.

# Discussion

Dr. Robert B.K. Dewar and Dr. Edmond Schonberg [published an
article](http://static1.1.sqspcdn.com/static/f/702523/9242013/1288741087497/200801-Dewar.pdf)
criticizing the use of Java as a first programming language. "Students
found it hard to write programs that did not have a graphic interface,
had no feeling for the relationship between the source program and
what the hardware would actually do, and (most damaging) did not
understand the semantics of pointers at all, which made the use of C
in systems programming very challenging". However, this was
[criticized
heavily](https://blogs.kent.ac.uk/mik/2008/01/the-pitfalls-of-java-as-a-first-programming-language-a-response/)
by writer mik on University of Kent's blog. Although he agrees that
Java indeed doesn't teach this material, he states that in many good
institutions, it has been moved to other, more advanced courses:
"blaming the programming language on bad curriculum design is a
simplistic and misleading argument that lacks real insight". Even
though they disagree on the fact that Java should be the language of
choice for introductory courses in programming, they both agree that
concepts inherent in C are essential and should be taught to students.

Dewar and Schonberg also made interesting points about what different
languages teach. More specifically, they claim that it's important to
learn C notably because "performance analysis is more straightforward,
because the cost of every software statement is clear". I vouch for
that. But don't listen to me; listen to Joel Spolsky, cofounder of
Trello, Fog Creek Software, and CEO of Stack Exchange. In his article
about the importance of going [back to
basics](http://www.joelonsoftware.com/articles/fog0000000319.html), he
analyses the performance of string concatenation in C. Without going
too much into detail, he explains why using the standard `strcat` to
concatenate `n` strings has n-squared performance and how this can be
easily brought down to linear performance. He uses this example to
stress the importance of knowing how things work at the byte level in
order to make good top-level decisions. He ends the article with a
rather colorful criticism of why teaching Java as a first programming
language is a bad idea.

> I am actually physically disgusted that so many computer science
   programs think that Java is a good introductory language, because
   it's "easy" and you don't get confused with all that boring
   string/malloc stuff but you can learn cool OOP stuff which will
   make your big programs ever so modular. This is a pedagogical
   disaster waiting to happen. Generations of graduates are descending
   on us and creating Shlemiel The Painter algorithms right and left
   and they don't even realize it, since they fundamentally have no
   idea that strings are, at a very deep level, difficult, even if you
   can't quite see that in your perl script. If you want to teach
   somebody something well, you have to start at the very lowest
   level. It's like Karate Kid. Wax On, Wax Off. Wax On, Wax Off. Do
   that for three weeks. Then Knocking The Other Kid's Head off is
   easy.

I promised I wouldn't get in the middle of this debate, so I'll stop there.

# What C taught me that Java didn't

At the beginning of this article, I mentioned how much of a
non-programmer I was before starting university. I had preconceived
notions about programming that limited my ability to grasp computer
science at a low-level. Java did not help me deepen my understanding,
but C did.

## What are strings, really?

An array of bytes terminated by a `'\0'`. That's it. When
interpreted as `char`, these bytes are really just indices into the
ASCII table, from where we get the visual representation
(i.e. letters) we're used to. C makes it easy to understand that
because you have to manually null-terminate your array of bytes, even
when using standard functions like `strcpy`. As discussed in Joel Spolsky's
aforementioned article, knowing how strings are implemented makes it
painfully easy to see that most string operations in C have linear
complexity.

The cool part is: you can apply this knowledge to higher level
languages like Java! You are now confident as to why the following
Java code, which would look perfectly fine to an unexperienced
programmer, is plainly awful.

    public String makeSentence(String[] words)
    {
        if (words == null) return null;
        String sentence = "";
        for (int i = 0; i < words.length; i++)
        {
            sentence += words[i] + " ";
        }
        return sentence;
    }

No, this is not horrendous code because the resulting sentence ends
with a space rather than a dot. Think of what's really happening at every
iteration of the `for` loop, translated into C:

1. Calculate the size of `sentence`, `words[i]` and the extra space
character - `O(n)`
2. Allocate memory for the new string - undetermined
3. Use `strcpy` to copy `sentence` to the new buffer - `O(n)`
4. Use `strcat` to concatenate `words[i]` to `sentence` - `O(n)`
5. Use `strcat` to concatenate the space character to the new buffer - `O(n)`
6. null-terminate the string - `O(1)`

Pretty awful, right? Next time, consider using
[`StringBuilder`](https://docs.oracle.com/javase/7/docs/api/java/lang/StringBuilder.html).

## Pointers

Oh pointers... You probably saw that one coming. Well, here's
my claim: being comfortable with pointers in C makes me a better
programmer in any language. Why? Because pointers are everywhere, even
if you hide them like all high level languages do.

As a novice Java programmer, I once wrote a method that looked like
this:

    public void getNode(TreeNode root, TreeNode outNode)
    {
        if (root == someNodeThatIWant)
        {
            outNode = root; // ouch
            return;
        }
        getNode(root.left, outNode);
        getNode(root.right, outNode);
    }

    // somewhere else in the code...
    public void doSomethingWithTreeNodes()
    {
        // ...
        TreeNode someNodeIWant = null;
        getNode(root, someNodeIWant);
        // use someNodeIWant
    }

The idea was to pass in `someNodeIWant` in `getNode(2)`,
cross my fingers, and hope that `someNodeIWant` points to a specific
node. Ouch. This is a direct consequence of having no fundamental
understanding of what pointers are. Granted, you could have never
written a single line of C and still be able to understand why this
code snippet is a big no-no. My point is: if you really understand how
pointers work, you will *never ever* write anything like this, no
matter the language you are writing it in (given that parameters are
passed by value like in C, obviously).

Let's revisit the code snippet. Actually, before we get to that, let's
get something out of the way: no matter how bad introductory Java
textbooks want you to think that primitive types are passed by value
and objects are passed by reference, the fact remains: *everything is
passed by value*! The only difference is that variables that hold an
object *hold a pointer (an address) to that object*. On the other
hand, variables that hold a primitive type hold the actual
value. When you call a method and pass a variable that holds an
object, you really are passing *by value* the address of that object.

Back to the snippet. Since `someNodeIWant` is an object, we know that
the variable holding it is a pointer to the actual `TreeNode`
object. We also know that passing this variable in `getNode(2)` will
copy the address held in `someNodeIWant` to the `outNode` variable in
`getNode(2)`. My erroneous assumption was that if I assigned the
pointer held by `root` to `outNode`, then the change would also be
reflected in `someNodeIWant`. But obviously that's not what's really
happening; `outNode` now points to an object, and `someNodeIWant`
still points to where it was originally pointing to; null in this case.

## Casting

When I was originally introduced to casting in Java, I carefully
learned the dos and don'ts. *Do* cast from a subclass to a
superclass. *Don't* cast a Car to a Plant or you'll have the JVM scream
at you. But hey, don't get me wrong, I did understand the logic behind
those rules. It wouldn't make sense to cast from a Car to a Plant
because then how would you access the car's `drive()` method?  Plants
don't drive, and that's a good thing. However, my understanding was
limited to classes and methods. I didn't appreciate what was going on
down there at the byte level. C on the other hand doesn't care about
the underlying data at an address; it only cares what you decide to
represent it as. You want to cast a pointer to `struct potato` to a
pointer to `struct banana`? Sure, go for it!  Also, if you do it
carefully, you can actually do pretty cool stuff (see [type
punning](https://en.wikipedia.org/wiki/Type_punning))!  Will all this
help me be a better programmer in high level languages?  Probably
not. I just find it cool to understand why Java simply won't
allow casting between objects that don't share a common interface.

## The role of the operating system

As soon as you write your first C program that does more than
crunching numbers and playing with pointers, you'll find yourself
having to talk directly to the operating system. This was the source
of a healthy dose of *aha* moments to me. Not only did I see many
operating system concepts in action, I learned about how the OS
manages resources such as file descriptors. Linux's famous *everything
is a file* finally got crystal clear to me. I got an in-depth
understanding of IPC with `stdin`, `stdout` and `stderr`, and how
central they are to Linux (e.g. piping tons of programs together to
get a desired output). I discoved how the file system manages files
*i.e.* what inodes are. The point that I'm trying to get across here
is that what you will learn from interfacing with the OS goes beyond
the lines of code you write.