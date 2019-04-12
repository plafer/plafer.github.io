---
layout:     post
title:      "Mathematical Notation From A Programmer's Perspective"
subtitle:   ""
date:       2019-04-11 22:00:00
author:     "Philippe Laferriere"
header-img: "img/rec-vs-ite-bg.jpg"
---

For the past six years, I've been studying computer science and writing various
kinds of software, from compilers and interpreters to a fully functional
operating system to self-driving car software. I have recently transitioned into
the study of rigorous mathematics, and found that mathematical notation is
confusing for one simple reason: [operator
overloading](https://en.wikipedia.org/wiki/Operator_overloading). This makes it
hard for the untrained eye to grasp the concept that's being communicated by a
mathematical expression.

Take for example the *distributivity of scalar multiplication with respect to
field addition* axiom for vector spaces (read my [article on linearity]({%
post_url 2019-03-14-linearity %}) for a full introduction to vector spaces). In
the following expression, let $$V$$ be a vector space over a field $$F$$.

$$ (a + b) * v = a*v + b*v \quad $$ for all $$ a,b \in F $$ and $$ v \in V $$

The confusion here lies in the fact that both the field and the vector space
define addition and multiplication operations, and they both use the symbols
$$+$$ and $$*$$ for it. Yet, the addition and multiplication operations for a
field and for a vector space are very different! The type of the scalar
multiplication operation of a vector space ($$*: F \times V \rightarrow V$$) is
not even the same as the type for the multiplication operation of a field ($$*:
F \times F \rightarrow F$$). In a nutshell, all that this type declaration
notation says is that the scalar multiplication operation for a vector space
multiplies a "number" (element of a field) with a vector to produce a vector,
while the multiplication operation for a field multiplies two numbers to produce
another number.

However, in order to fully grasp the meaning of an expression, it's essential to
distinguish between the different possible operators for each instance of a
symbol. And that's a skill that requires practice. The beginner's eyes is likely
to simply drop the type declarations at the end of the line and conclude that
this is a trivial statement because it looks exactly the same as the
*distributivity of multiplication over addition* for numbers. She will then
obviously not grasp the constraints that the axiom imposes on the scalar
multiplication and vector operations for a vector space. Clearly, operator
overloading contributes to the steepness of the learning curve for mathematics.

Here's what's actually expected of the reader of a mathematical
expression. First, look at the type declarations for the variables at the end of
the line. Then, perform type analysis on the expression. Last, extract meaning
based on the [context of each
operator](https://en.wikipedia.org/wiki/Context-free_grammar). We're essentially
asking the reader to be a compiler type checker.

As an exercise, let's step through the mathematical expression and assign the
appropriate operator (field vs. vector space) to each operator symbol. We will
use $$\bigoplus$$ for vector addition and $$\bigodot$$ for vector scalar
multiplication. Field operators will use the standard $$+$$ and $$*$$.

The expression will be parsed from left to right (basic parsing knowledge is
assumed). Underlined operators have not been assigned a type yet.

$$ (a \underline{+} b) \underline{*} v = a\underline{*}v \underline{+}
b\underline{*}v \quad $$ for all $$ a,b \in F $$ and $$ v \in V $$

#### Step 1

We look at the first addition symbol between the parentheses. It sums $$a$$
and $$b$$, which are declared to be field elements. This $$+$$ is thus a field
addition operator.

$$ (a + b) \underline{*} v = a\underline{*}v \underline{+}
b\underline{*}v \quad $$ for all $$ a,b \in F $$ and $$ v \in V $$

#### Step 2

We can now evaluate the first multiplication symbol. It multiplies a field
element $$a+b$$ and a vector $$v$$. It is thus a vector space scalar
multiplication operator.

$$ (a + b) \bigodot v = a\underline{*}v \underline{+}
b\underline{*}v \quad $$ for all $$ a,b \in F $$ and $$ v \in V $$

#### Step 3

Let's now turn to the right side of the equality symbol. We look at the first
$$*$$ symbol. It multiplies a field element $$a$$ with a vector $$v$$. It's a
vector space scalar multiplication operator.

$$ (a + b) \bigodot v = a \bigodot v \underline{+}
b\underline{*}v \quad $$ for all $$ a,b \in F $$ and $$ v \in V $$

#### Step 4

We now look at the last multiplication symbol. Similarly, it multiplies a field
element $$b$$ with a vector $$v$$. It's a vector space scalar multiplication
operator.

$$ (a + b) \bigodot v = a \bigodot v \underline{+} b \bigodot v \quad $$ for all
$$ a,b \in F $$ and $$ v \in V $$

#### Step 5

Finally, we look at the last addition symbol. It sums two vectors, and thus
is a vector addition operator.

$$ (a + b) \bigodot v = a \bigodot v \bigoplus b \bigodot v \quad $$ for all
$$ a,b \in F $$ and $$ v \in V $$

And we're done. Isn't this a lot less ambiguous now? Note that the original
expression had two $$+$$ symbols, each referring to a different operator!
Written this way, it is obvious that the expression demands that the vector
resulting from multiplying the number $$n=a+b$$ with the vector $$v$$ be equal
to the sum of the vectors $$w_1 = a \bigodot v$$ and $$w_2 = b \bigodot v$$. Of
course, everything that this equality entails is not immediately clear and
requires further study, but at least now the reader is set up for success.

Languages such as OCaml are moving away from operator overloading for
especially this reason: it's confusing. More specifically, `+` can only be used
to sum two integers, and `+.` can only be used to sum two floats. For example, the
expression `3 + 1.0` is illegal; the appropriate expression is `3.0
+. 1.0`. Annoying, you say? Well, for sums of literals, sure. But it's extremely
nice to know immediately that in `a + b`, `a` and `b` are integers, while in `c
+. d`, `c` and `d` are floats.

But then I'm not calling for an overhaul of mathematical notation; what we lose
in clarity we gain in generality. If for every new mathematical object that
needed an addition operation, we required the inventor to define a new, unused
symbol (that hopefully looks somewhat like $$+$$), we'd run out of symbols
pretty quickly. And to avoid absolutely any overloading, we'd need some
authority to define a global registry of which symbol is reserved by which
mathematical object (*a la*
[IANA](https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority) for
internet numbers such as ports). This becomes impractical fast. The advantage
that programming languages have over math is that the number of built-in types
that need operators is finite and known at language-design time, and therefore
the language designer can afford to carefully choose different but similar
symbols for analogous operators (such as `+` and `+.`). Yet, there isn't even
consensus as to whether operator overloading is a good thing or a bad thing in
the programming language community. Some languages such as C++ allow operator
overloading on user-defined types, which are by definition not known at
language-design time. These languages are bound to run into the same problems as
math. All in all, for math and programming languages alike, if you want the
generality, you lose the clarity.
