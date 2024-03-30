---
title: 'Dotnet Interop:- C# and F#'
date: 2020-04-29T22:04:54Z
draft: false
---

## What makes F# special?
In the Dotnet world, F# stand out as being the Functional based language. This might sound strange, and I on’t have time to go into the full difference between Function and Object-Oriented languages and paradigms here, please do a google if you wish to learn more.

Some of the main features of F# that are relevant to this series are as follows:-

- Higher Order Functions
- Modules
- Records
- Sequences
- Discriminated Unions
- Options

I put them in this order for a reason. This is the list I will be discussing and showing how the Dotnet runtime allows these two languages of different paradigms to happily (in most cases) work together.

## Modules and Namespaces
Lets start with Namespaces, while F# can use namespaces, it will normally use Modules. However, if you decide to add a namespace declaration to the top of your F# file, when you reference the output library in your C# project, you will notice no difference when importing the namespace, you still use ‘using FSharp.Namespace’, or whatever you called your library.

So why bother with Modules at all? Firstly, Modules are more in line with the way F# has been built up and as such, you will find it used in most F# libraries instead, we can still use these Modules in C#.

When a Module is imported, it represents itself as a static class in C#. This allows for seamless usage between F# and C# code on both sides as in F#, we use ‘open’ statements on C# imports.

For example, this code here:

```js
namespace FSharpClassLib

module FunctionalParadigms =
    let aNumber = 5
```

```haskell
type x = | One | Two
```

Can be used in a C# console app as follows:

```cs
using FSharpClassLib;
namespace CSharpConsole
{
    class Program
    {
        static void Main(string[] args)
        {
          var test = FunctionalParadigms.aNumber;
        }
    }
}
```

Above, you also see an example of standalone values being used from F# in C#. again simply showing itself as a static field.

## Records
In F#,  [“Records represent simple aggregates of named values, optionally with members. They can either be structs or reference types. They are reference types by default.”](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/records). That seems like a mouthful, and basically it means, it’s an object that holds a combination of values. In C# terms, it’s effectively a class with a bunch of properties on it. Whilst that sounds simple, something to know about F# is it uses Structural Equality and by default, it’s data structures are Immutable. For those unfamiliar with these terms, they are as follows:


1. Structural Equality : A object is equal if all it’s properties match and not if it is the same reference in memory.
2. Immutable : The object cannot change over time.

With these defined, it may come as no surprise that to instantiate a F# record in C#, you use an ‘All Arguments Constructor’. This guarantees known values, it also allows the Properties consumed by C# to be read only and s such, keep in line with the Immutability aspect of F#.

The Structural Equality also get implemented by default even on the C# side. So using a record made in F#, and using
```cs
var a = new Thing(1, true);
var b = new Thing(1, true);
```
in C#, the objects a and b are equal.

To how this in action here’s a simple F# Record

```haskell
namespace FSharpClassLib

module FunctionalParadigms =
   type TypeTwo = {
      isTrue : bool
      SomeNumber: double
  }
```

It is used in a C# Console App as follows:

```cs
using FSharpClassLib;
using System;

namespace CSharpConsole
{
    class Program
    {
        static void Main(string[] args)
        {
            var a = new FunctionalParadigms.TypeTwo(true, 1.1);
            var b = new FunctionalParadigms.TypeTwo(true, 1.1);

            Console.WriteLine(a.Equals(b)); // This prints True!
            Console.ReadKey();
        }
    }
}
```

And when decompiled, shows the following

```cs
public sealed class TypeTwo : IEquatable<TypeTwo>, IStructuralEquatable, IComparable<TypeTwo>, IComparable, IStructuralComparable
{
    public TypeTwo(bool isTrue, double someNumber);

    [CompilationMapping(SourceConstructFlags.Field, 0)]
    public bool isTrue { get; }
    [CompilationMapping(SourceConstructFlags.Field, 1)]
    public double SomeNumber { get; }
    [CompilerGenerated]
    public sealed override int CompareTo(TypeTwo obj);
    [CompilerGenerated]
    public sealed override int CompareTo(object obj);
    [CompilerGenerated]
    public sealed override int CompareTo(object obj, IComparer comp);
    [CompilerGenerated]
    public sealed override bool Equals(object obj, IEqualityComparer comp);
    [CompilerGenerated]
    public sealed override bool Equals(TypeTwo obj);
    [CompilerGenerated]
    public sealed override bool Equals(object obj);
    [CompilerGenerated]
    public sealed override int GetHashCode(IEqualityComparer comp);
    [CompilerGenerated]
    public sealed override int GetHashCode();
    [CompilerGenerated]
    public override string ToString();
}
```

Neat huh?

I think this should be enough information for now, however, in the next post we will continue exploring this interaction between the two languages and how it plays well together.

Any questions comment below or feel free to use the contact me page.

See you next time!