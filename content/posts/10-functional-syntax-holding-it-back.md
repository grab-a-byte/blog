---
title: 'Is syntax holding back functional programming?'
date: 2024-07-12T20:30:30+01:00
draft: false
toc: false
---
While looking through many programming languages over the years, I have come to appreciate the ones which are based around the theory of functional programming. I find they are a joy to write and allow for some very expressive code in few characters. However, when looking at the popularity of many of these languages I've noticed their popularity seems to be low. As you may have guessed from the title, I think a large part of this is down to syntax and I'd like to try and explain why I think this may be. I'd like to preface it with it not being the only reason I think but it's one of the primary reasons as to why this may be.

For reference, this post is purely a food for thought kind of post and is not an argument in any way.

## Most Popular Languages are also the most C-Like. Coincidence?

Using the website [Languish](https://tjpalmer.github.io/languish/) as a means of measuring popularity, the top 8 (as of writing this) are as follows:
- Python
- JavaScript
- Typescript
- Java
- C++
- C#
- Go
- C

All of this with the exception of Python (which I imagine is coming from data science and the ever growing popularity of AI) have their basis in a C-Like way of writing code (including unsurprisingly, C...).

My definition of a C-like language is one that:
- Uses braces `{}` to denote scope.
- Uses parenthesis `()` to denote function calls.
- Use of an explicit `return` keyword.
- Has type definitions used in the function signatures.

These all check at least 3 of those 4 boxes and that is enough for me to call them C-Like. If you want to take away from this that they are mostly languages that encourage the use of Object-Oriented design then you may be on the right track with this article.

A final point on these languages is that none of them will claim to have come from a functional background and while some may have some functional features in their modern incarnations it was not a thought when they first came about.

#### Side Note: Meta-Language (ML) Syntax
ML style languages are what I would name the opposite of C-like and they tend to have the following attributes:
- Use whitespace indentation to denote blocks.
- Heavier use on type inference to determine types (leading to less type definitions being written).
- No parenthesis to call functions, opting for a whitespace delimited list of arguments instead.
- Uses the last evaluated expression as a return without the need of a specific return keyword.

## Most Popular Languages with Functional Concepts
To further the evidence that a C-like syntax is prevalent, I'd like to shift my attention to a couple of other popular languages that have been designed with some of these functional programming concepts all built in from the get-go of their design.

### Scala
Scala is one of the most popular functional languages around and can be found at [https://www.scala-lang.org/](https://www.scala-lang.org/).
I suppose I should very much specify that when I am talking about Scala I am talking about [Scala versions 1 & 2](https://docs.scala-lang.org/overviews/scala-book/introduction.html) as [Scala 3](https://docs.scala-lang.org/scala3/book/scala-features.html) has removed a lot of the syntax likeness to C that I am going to discuss. The fact that the language is still on a decline on the Languish website despite these changes only strengthens the argument I have about it.

At the time of writing this, Scala sits at position 31 on the Languish website which is quite a feat given the 507 languages the page currently tracks (even if some are not proper programming languages). That is indeed quite the accomplishment however its syntax is very similar to Java. I would imagine that a large part of this is due to the language running on the JVM and needing to interop with Java code. So adding some syntactical sugar over what Java is able to do would seem like a sensible way to bring the language to the masses of Java developers in the world.

As a prime example of how Scala can make the code more concise, we can look at the following example of a data class (Only get properties and a constructor requiring all arguments)

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
Scala:
```scala
class Person(val firstName: String, val lastName: String)
```

As you can see, despite the conciseness, the syntax itself is still similar the that of the Java constructor and the C-like styling.
If you would like to see more examples of this, have a Google for examples of Scala vs Java code and you can find many more examples where Scala just looks like a 'cleaner' Java.

### Rust
Of course we need to speak about the elephant in the room here regarding a language with functional concepts built in from the start. Rust is a systems programming language that has taken the world by storm and is used in the Firefox's JavaScript and WebAssembly engine and has [made it into the Linux kernel](https://en.wikipedia.org/wiki/Rust_for_Linux) along with Microsoft investing [one million dollars](https://thenewstack.io/microsofts-1m-vote-of-confidence-in-rusts-future/) into the foundation behind it.
Furthermore, This currently sits in position number 10 on the language and is a roaring success of a programming language.

However, the compiler itself shows a telling story of how much the syntax (as well as familiarity with the language) can have a huge impact. Back in its first incarnation, the Rust compiler was written in [OCaml](https://ocaml.org/) as can be seen [here](https://github.com/rust-lang/rust/tree/ef75860a0a72f79f97216f8aaa5b388d98da6480/src/boot) in a commit from 2011, just before the compiler was re-written in Rust itself.

Using the Github metrics graphs, from 2013 onwards, we see a sharp uptick in the number of contributors to the Rust compiler as shown by this graph.
![Rust Contributors Graph](/functional-syntax/rust-contributer-graph.png)

Obviously this is not all just due to the syntax and probably has a large amount to do with people who were writing rust could now contribute but I would argue its that syntax that gave them the reason to learn Rust in the first place.


## So is this trend going to continue?
I fully believe this trend is going to continue down this line and will continue to go in this direction and I have a couple of language examples to prove this.

### Zig
Zig is an up-and-coming language that while being in Beta, was deemed to be good enough to be used in two different fully fledged products known as [Tigerbeetle](https://tigerbeetle.com/) and [Bun](https://bun.sh/).

Zig aims to be a systems level language similar to Rust listed above but with less guardrails around memory management making it closer to being able to be a drop in replacement for C.
Obviously coming from that domain will lead to the language trying to replicate C in its syntax and it does with the addition of some Functional features such as:

- [Typed Errors](https://ziglang.org/documentation/0.13.0/#Errors)
- [Unions and Tagged Unions](https://ziglang.org/documentation/0.13.0/#Errors)
- [Exhaustive Switching](https://ziglang.org/documentation/0.13.0/#Errors)

And this is just to name a few. However the uptake of Zig seems to lean furthermore into this idea of a C-like syntax will bring more people who are willing to try the language and make it more successful. 

### Gleam
Gleam is a really interesting language in that it uses the Erlang BEAM Virtual Machine. Erlang is a well known functional language that has been around for many many years (since 1986!) and currently sits at position 99 on the languish website, again a fairly notable achievement given it's functional and white-spaced nature. 

The reason I mention Erlang is because Gleam is a language that had it's Version 1.0 release on the 4th March 2024 and has skyrocketed up to position 174 in the Languish website. While that may not seem impressive, it still puts it in the top 50% of all languages tracked and it is less than double the language that was built for the platform it runs on! Not too shabby for a new language. But also if we take a look at a sample of Gleam code:

```gleam 
import gleam/io

pub fn main() {
  io.debug(double(10))
}

fn double(a: Int) -> Int {
  multiply(a, 2)
}

fn multiply(a: Int, b: Int) -> Int {
  a * b
}
```

As you can see, while not 100% on the C-like front, we still have the parenthesis for function arguments and the braces for denoting scope. This is for a pure functional language that is running on a VM built for a functional language and we still see parts of C trying to crawl back into the syntax. 

I suppose this is my final point on the matter of showing how languages continue to be driven by the syntax of C and how this syntax could be tied to the popularity of new programming languages.

## Not to say "ML" inspired languages aren't used 

I should probably reiterate that non-C-like languages do 100% have a place in the world and can be very useful for particular scenarios, right tool for the job and all that. Furthermore, I'd be amiss to not point out that the benefits functional languages can provide have made their cases shown in the form of some very large companies using these non-C-like languages and having great success with it. Examples of this include:

- Twitter uses Scala in it's "Algorithm" (https://github.com/twitter/the-algorithm)
- Meta uses Haskell to filter out spam (https://engineering.fb.com/2015/06/26/security/fighting-spam-with-haskell/)
- Jane Street use OCaml for ...well everything... (https://blog.janestreet.com/why-ocaml/)


## Final tidbit
As a final tidbit of information, I think the reason for the above is due to the rise of Object-Oriented programming and it's tendency to be C-like with a lot of them being C derivatives.
Functional programming is less popular and for more on that topic I would point you to this great talk be Richard Feldman, the creator of the Roc programming language (another language with a non-c-like syntax).
https://youtu.be/QyJZzq0v7Z4?si=nIQjntdT_2Rvshsx - Richard Feldman - Why isn't functional programming the norm.

Thank you for reading, any comments or questions please reach out to me on my Github.
