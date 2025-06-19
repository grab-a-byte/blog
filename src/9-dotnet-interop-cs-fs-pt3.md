# C# and F# Pt. 3

### 2020-08-29

This is Part 4 in my series of Blog Posts on DotNet interop.

So over the last couple of posts, we’ve looked at what does work well in C# from F#, and most of it seems to go pretty smoothly. But there are occasions where the languages refuse to play nice with each other. That’s what we’ll be discussing in this final post.

## Options
Option in F# are a way to get around the idea of null by providing two ‘sub-types’ called Some and None. Imagine trying to parse an Int from a String, if you provide a valid string such as “42”, it succeeds and the method should return Some(42), if you provided a invalid string, it may return none to indicate that it has indeed failed. An example being as follows:
```haskell
let parseInt x =
    match System.Int32.TryParse(x) with
    | success, result when success -> Some(result)
    | _ -> None
```

When consuming this from C#, you get a FSharpOption type (which I believe is in FSharpCore.dll) and you get access to a particular property called Value. If you succeeded and managed to return the Some type, this will have the value it succeeded in getting (42 in the above example), however if it returns None, C# interprets this as null and as such, you lose the safety that comes from using Options in F# and still will be required to insert null checks from that point forward. E.g.

```cs
var integerOption = FunctionalParadims.parseInt("42");
var val = integerOption.Value; //val is 42

var integerOption2 = FunctionalParadigms.parseInt("Blah Blah Blah"); // integerOption2 is null
var val2 = integerOption.Value //Throws null reference excpetion
```

## F# Lists
F# has the idea of a list however, this differs from the C# type of List. This can cause confusion however the FSharpCore.dll comes to the rescue again as it allows CSharp to consume a FSharpList type. This does taint the C# code however and this is something you may wish to avoid. To avoid importing the F# DLL (or nuget package) into your project, the F# lists implement IReadOnlyCollection which is in System.Collection.Generic and as such is normal to

```haskell
let getAList() = [1;2;3]
```
```cs
IReadOnlyCollection<int> list = FunctionalParadigms.getAList();
```

## Discriminated Unions
I know I already said Discriminated Unions work well in the last part so why am I re-covering them? Well there is one case where DU’s don’t play nice in C# and that is if you don’t provide a type or an integer on the methods and have it ‘raw’

```haskell
type Animal =
    | Dog
    | Cat
    | Parrot
```
In this situation, it creates a bunch of “Tags” which you either need to switch on, if else, or some other method. The class and an example of their usage is down below.

```cs
public sealed class Animal : IEquatable<Animal>, IStructuralEquatable, IComparable<Animal>, IComparable, IStructuralComparable
{
    public static class Tags
    {
        public const int Dog = 0;

        public const int Cat = 1;

        public const int Parrot = 2;
    }

    public int Tag { get; }
    public static Animal Dog { get; }
    public bool IsDog { get; }
    public static Animal Cat { get; }
    public bool IsCat { get; }
    public static Animal Parrot { get; }
    public bool IsParrot { get; }
    public override string ToString();
    public sealed int CompareTo(Animal obj);
    public sealed int CompareTo(object obj);
    public sealed int CompareTo(object obj, IComparer comp);
    public sealed int GetHashCode(IEqualityComparer comp);
    public sealed override int GetHashCode();
    public sealed bool Equals(object obj, IEqualityComparer comp);
}
```
```cs
private static void SwitchingOnNoTypeDUs()
{
    Animal animal = Animal.Cat;
    switch (animal.Tag)
    {
        case Animal.Tags.Dog:
            Console.WriteLine("dog");
            break;
        case Animal.Tags.Cat:
            Console.WriteLine("cat"); // prints cat
            break;
        case Animal.Tags.Parrot:
            Console.WriteLine("polly want a cracker");
            break;
        default:
            throw new ArgumentOutOfRangeException();
    }
}
```

The final part of this post is something that baffles me beyond belief but is unfortunately true. The one thing I found that doesn’t work well in the C# realms.

## Functions
Yes. As a functional language, in F# you pass functions into functions and get functions returned from functions and it works a charm. Trying to pass a function from C#, not so friendly. My first thought when trying to do so was the following.

Say I have a function in F# like so :
```js
let doAsStringFunction stringFunc =
    stringFunc "Something"
```
Quite simple, takes in a function that takes a string and returns the result of that function on the word “Something”. I tried to call it like this:

```cs
FunctionalParadigms.doAStringFunction<int>((x) => x.Length());
```

Which to me seems logical but this doesn’t work. I will now present the actual solution and end this post before I start rambling beyond compare.

```cs
FunctionalParadigms.doAStringFunction(FSharpFunc<string, int>.FromConverter((x) => x.Length));
```