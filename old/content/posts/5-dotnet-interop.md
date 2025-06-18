---
title : 'Dotnet  Interop'
date : 2020-02-29T19:38:35Z
draft : false
toc: false
---

# What is DotNet?

DotNet is a whole stack. It can get very confusing when talking about .NET and what it is. The main part which actually makes all of the following possible is the .NET runtime.

What this runtime is in simple terms, is a layer which takes the compiled code, and runs it on the machine. It managed to do this as it reads what is know as Intermediate Language (IL) and translates this to be ran natively on the machine. This combination of IL and Runtime allow for some rather interesting things, and in this series of posts, we’ll be talking about Interoperability (Interop for short).

# What is Interop?

If you Google DotNet interop and open the first page, it’s likely Microsoft Documentation which states the following :-

“Interoperability enables you to preserve and take advantage of existing investments in unmanaged code. Code that runs under the control of the common language runtime (CLR) is called managed code, and code that runs outside the CLR is called unmanaged code. COM, COM+, C++ components, ActiveX components, and Microsoft Windows API are examples of unmanaged code.”

I personally find this a mouthful and confusing and as such I simply prefer to think of it as using libraries from other languages that can run in the CLR (which runs on the runtime, I never said .NET made any sense).

# Future plans

So, with the above said and done. I’m here to announce a small series I will be doing on .NET Interop. I will be covering using VB, C# and F# however I have also seen this done with C++ (my C++ is far too rusty to figure it out). All posts will be using C# and the other language and will be exploring how .NET manages languages with the same programmatical paradigm (C#. VB both Object-Oriented/Imperative) and how it deals with differences in paradigm’s (C# and F#, Imperative vs Functional).

Hope you enjoy the upcoming posts!