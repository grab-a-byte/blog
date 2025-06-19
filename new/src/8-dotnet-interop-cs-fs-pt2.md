# C# and F# Pt. 2

### 2020-07-03

This is Part 3 in my series of Blog Posts on DotNet interop.

Continuing on from where we left off last time, we will try to cover off the rest of F#’s features that play really well and simply when being consumed from a C# codebase.

## Values
F# can have values places directly inside modules, almost ‘global’ values in the module. See an example below :-

```js
module FunctionalParadigms
    let aNumber = 5
```

As we know from the last post, modules expose themselves as static classes in C#. As such, this value just becomes a static readonly property of that static class. As such, C# can consume it as follows.

```cs
var num = FunctionalParadigms.aNumber;
```

## Functions
Functions are quite core to F# and functional programming in case you hadn’t guessed. So surely they should be easy enough to consume from C#? The answer here is kind of, if you stick with using types as parameters and not pass around functions (which is normal in F#). The following example takes a string and returns its length (very trivial I know)

```js
module FunctionalParadigms =
    let aFunction (string:string) =
        string.Length
```

It is consumed by C# as a static function on a static class :-

```cs
var strLength = FunctionalParadigms.aFunction("Thing"); // Returns 5
```

## Sequences
F# uses a lot of sequences. It is exposed to C# using the IEnumerable interface and as such can be treated as one without any issues.

## Discriminated Union
Discriminated Unions in F# are a ‘choice type’. They act in slightly different ways depending on how they are used. if we define one of them using integers, It will compile down to a C# enum type. And example is as follows :-

```Haskell
type LogLevels =
    | Error = 1
    | Warning = 2
    | Info = 3
```

Compiles sown to C# as this :-
```cs
public enum LogLevels
{
    Error = 1,
    Warning = 2,
    Info = 3
}
```

However, if we use any types within the Discriminated Union, it will create almost ‘factory functions’ for the sub-types. For Example:

```haskell
type OtherLogLevels =
    | Error of int
    | Warning of struct(int * string)
    | Info of string
```

Gets the following interface in C#:
```cs
public abstract class OtherLogLevels : IEquatable<OtherLogLevels>, IStructuralEquatable, IComparable<OtherLogLevels>, IComparable, IStructuralComparable
{
    public bool IsInfo { get; }
    public bool IsWarning { get; }
    public bool IsError { get; }
    public int Tag { get; }

    public static OtherLogLevels NewError(int item);
    public static OtherLogLevels NewInfo(string item);
    public static OtherLogLevels NewWarning((int, string) item);
    public sealed override int CompareTo(OtherLogLevels obj);
    public sealed override int CompareTo(object obj);
    public sealed override int CompareTo(object obj, IComparer comp);
    public sealed override bool Equals(object obj);
    public sealed override bool Equals(object obj, IEqualityComparer comp);
    public sealed override bool Equals(OtherLogLevels obj);
    public sealed override int GetHashCode();
    public sealed override int GetHashCode(IEqualityComparer comp);
    public override string ToString();

    public static class Tags
    {
        public const int Error = 0;
        public const int Warning = 1;
        public const int Info = 2;
    }
    public class Warning : OtherLogLevels
    {
        public (int, string) Item { get; }
    }
    public class Error : OtherLogLevels
    {
        public int Item { get; }
    }
    public class Info : OtherLogLevels
    {
        public string Item { get; }
    }
}
```

## Unit
As F# hates the idea of null, it has a special type called unit. however, this means it effectively becomes interchangeable. If a F# function receives a unit, you will likely pass a null. And if a Unit is the return type from a F# function, in C# it interops as returning void. Simples.

## Arrays
I don’t have anything clever to say here. It’s an array…

## Conclusion
This bring me to the end of the “works really well” section of these blog posts. Next post will still be in the F# – C# Interop space, but will focus on thing that work… with  few work-arounds.