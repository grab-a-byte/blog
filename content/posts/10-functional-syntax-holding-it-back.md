---
title: 'Is syntax holding back functional programming?'
date: 2024-05-29T20:30:30+01:00
draft: true
toc: false
---

Recently I've been looking at a few programming languages and in particula few of the functional programming languages out there in the wild and noticed that quite a few of them, while having some very good benefits and being quite the joy to use and write in, they seem to be not very popular. As you may have guessed from the title, I think a large part of this is down to syntax and I'd like to try and explain why I think this may be. I'd Ike to preface it with it not being the only reason I think but it's for sure one of my larger ideas as to why this may be

For reference, this post is purely a food for thought kind of post and is not an argument either way.

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

All of this with the exception of Python (which I imagine is coming from data science and the ever growing popularity of AI) have their basis in a C-Like way of writing code (including unsuprisingly, C...).
My definition of a C-like language is one that:
- Uses braces `{}` to denote scope.
- Uses parenthesis `()` to denote function calls.
- Use of an explicit `return` keyword.
- Has type definitions used in the function signatures.

These all check at least 3 of theose 4 boxes and that is enoug for me to call them C-Like. If you want to take away from this that they are mostly languages that encourage the use of Object-Oriented deisgn then you may be on the right track with this article.

A final point on these languages is that none of them will claim to have come from a functional background and while some may have some functional features in their modern incsanations it was not a thought when they first came about.

## Most popular languages with Functional Concepts

To further the evisence that a C-like syntax is prevalent, I'd like to shift my attention to a couple of other popular languages that have been designed with some of these functional programming concepts all built in from the get-go of their design.

### Scala
Scala is one of the most popular functional languages around and can be found at [https://www.scala-lang.org/](https://www.scala-lang.org/).
I suppose I should very much specify that when I am talking about scala I am talking about scala versions 1 & 2 (https://docs.scala-lang.org/overviews/scala-book/introduction.html) as scala 3 has removed a lot of the syntax likeness to C that I am going to discuss. The fact that the language is still on a decline on the Languish website despite these changes only strengthens the arguemnt I have about it.

At the time of writing this, Scala sits at position 31 on the Languish website which is quite a feat given the 507 languahges the page currently tracks (even if some are not proper programming languages). That is indeed quite the acomplishment however its syntax is very simila to Java. I would imagine that a large part of this is due to the language running on the JVM and needing to interop with Java code so adding some syntactical sugar over what Java is able to do would seem like a sensible way to bring the language to the masses of Java developers in the world.

As a prime example of how Scala can make the code more concise, we can look at the following example of a data class (Only get properties and a constructor requireing all arguments)

Java:
```java
public class Person {
    private final String firstName;
    private final String lastName;
    public Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
}
```

```scala
class Person(val firstName: String, val lastName: String)
```

As you can see, despite the conciseness, the syntax itself is still similar the that of the Java constructor and the C-like styling.
If you would like to see more examples of this, have a google for examples of Scala vs Java code and you can find many more examples where Scala just looks like a 'cleaner' Java.

### Rust
Of course we need to speak about the elephant in the room here regarding a language with functional concepts built in from the start. Rust is a systems programming language that has taken the world by storm and is used in the Firefox Javascript and WebAssembole engine and has [made it into the Linux kernal](https://en.wikipedia.org/wiki/Rust_for_Linux) along with Microsoft investing [one million dollars](https://thenewstack.io/microsofts-1m-vote-of-confidence-in-rusts-future/) into the foundatino beind it.
Furthermore, This currently sits in position number 10 on the language and is a roaring success of a programming language.

However, the compiler itself shows a telling story of how much the syntax (as well as familiarity with the language) can have a huge impact. Back in its first incarnation, the Rust compiler was written in [OCaml](https://ocaml.org/) as can be seen [here](https://github.com/rust-lang/rust/tree/ef75860a0a72f79f97216f8aaa5b388d98da6480/src/boot) in a commit from 2011, just before the compiler was re-written in itself.

Using the Github metrics graphs, from 2013 onwards, we see a sharp uptick in the number of contributers to the Rust compiler as shown by this graph.
![Rust Contributers Graph](/functional-syntax/rust-contributer-graph.png)

Obviously this is not all just due to the syntax and probably has a large amount to do with people who were writing rust could now contribute but I would argue its that syntax that gave them the reason to learn Rust in the first place.


## Continuation of C-Like languages. So, What Happens When We Take Modern, Functional-Like Languages and Add C-Like Syntax?
Modern Langauages using a C-like syntax still
 - Zig
 - Gleam

## Not to say "ML" inspired languages aren't used
I should probably reiterate that non-C-like languages do 100% have a place in the world and can be very useful for particular sceanrios, right tool for the job and all that. Furthermore, I'd be amiss to not point out that the benefits functional languages can provide have made their cases shown in the form of some very large companies using these non-C-like languages and having great success with it. Examples of this include:

- Twitter uses Scala in it's "Algorithm" (https://github.com/twitter/the-algorithm)
- Meta uses Haskell to filter out spam (https://engineering.fb.com/2015/06/26/security/fighting-spam-with-haskell/)
- Jane Street use OCaml for ...well everything... (https://blog.janestreet.com/why-ocaml/)


## Final tidbit
As a final tidbit of information, I think the reason for the above is due to the rise of Object-Oriented programming and it's tendancy to be C-like with a It of them being C deriveratives.
Functional programming is less popular and for more on that topic si would point you to this great talk be Richard Feldman, the creator of the Roc prgramming language (another language with a non-c-like syntax).
https://youtu.be/QyJZzq0v7Z4?si=nIQjntdT_2Rvshsx - Richard Feldman - Why isnt functional programming the norm.

Thank you for reading, any comments or questions please reach out to me on my github.