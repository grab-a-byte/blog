---
title: 'Is syntax holding back functional programming?'
date: 2024-05-29T20:30:30+01:00
draft: true
---

Recently I've been looking at a few programming languages and in particula few of the functional programming languages out there in the wild and noticed that quite a few of them, while having some very good benefits and being quite the joy to use and write in, they seem to be not very popular. As you may have guessed from the title, I think a large part of this is down to syntax and I'd like to try and explain why I think this may be. I'd Ike to preface it with it not being the only reason I think but it's for sure one of my larger ideas as to why this may be

## Most Popular, also most C-Like. Coincidence?

Using the website [Languish](https://tjpalmer.github.io/languish/) as a means of measuring popularity, the top 8 (as of writing this) are as follows:
- Python
- Javascript
- Typescript
- Java
- C++
- C#
- Go
- C

All of this with the exception of Python (which si imagine is coming from data science and the ever growing popularity of AI) have their basis in a C-Like way of writing code (including unsuprisingly, C...).
My definition of a C-like language is one that:
- Uses braces `'{}'` to denote scope.
- Uses parenthesis `()`to denote function calls.
- Use of an explicit return keyword.
- Has type definitions used in the function signatures.

These all check at least 3 of theose 4 boxes and that is enoug for me to call them C-Like. If you want to take away from this that they are mostly languages that encourage the use of Object-Oriented deisgn then you may be on the right track with this article.

A final point on these languages is that none of them will claim to have come from a functional background and while some may have some functional features in their modern incsanations it was not a thought when they first came about.

## Most popular languages with Functional Concepts
Both of these have a c-like syntax.
- Scala
    - C-Like syntax (scala 2)
    - Java Interop
- Rust

## Continuation of C-Like languages. So, What Happens When We Take Modern, Functional-Like Languages and Add C-Like Syntax?
Modern Langauages using a C-like syntax still
 - Zig
 - Gleam

## Benefits of functional programming
- Readability (sometimes)
- Describe what you want to be done instead of how to do it
- Immutability
- Parrallelisation

Used at some large comapnies
- Twitter use Scala
- Meta use Haskell
- Jane Street OCaml

So why is it not the norm?
https://youtu.be/QyJZzq0v7Z4?si=nIQjntdT_2Rvshsx - Richard Feldman - Why isnt functional programming the norm.
