# Gameboy ROM disassembler (Zig vs Go)

### 2025-02-01

# Why bother?

I know this may seem like a strange title to start the blog post off with but I
thought I would like to explain why I thought this could be an interesting
concept.

Zig first and foremost is essentially trying to be a modernised C. After years
of learning how not to do C and how to do C, Zig looks to be trying to keep C's
simplicity and amount of control with some more modern features in it's type
system and tooling.

Go on the other hand is a programming language with a garbage collector and less
control over the hardware in the end. It is again designed to be simple and easy
to learn and work in and involves some of the people who were involved with
making C.

So given these two viewpoints, I thought it would be interesting to see how the
two compared as they are both smaller languages with a C based background, just
one has a garbage collector and less control and the other closer to C's
inherent need to understand the hardware. Does having the garbage collector and
so forth actually make much of a difference to speed, binary size and other
factors.

To do this, I create a Gameboy ROM disassembler using the information at
[PanDocs](https://gbdev.io/pandocs/) and the repo can be found on my [Github[(https://github.com/grab-a-byte/) under the ['gameboy'](https://github.com/grab-a-byte/gameboy) repository.

# Zig
## Pros

### Error Handling

I found the error handling in Zig to be amazingly simple to read. If you want to
make a function return an error value, just put a '!' in front of the return
type. All error that can be returned are automatically combined to make a set
that guarantees you handle all cases of that when you work with it. Handling
these errors on the other side is also very easy to read by using either the
'try' keyword to just pass the error back up the call chain (similar to the '?'
operator in Rust) or by using the catch keyword to set a default value/ match on
the error.

Here's an example stolen from the unofficial [zig book](https://pedropark99.github.io/zig-book/Chapters/09-error-handling.html).

```JS
fn print_name() !void {
    const stdout = std.getStdOut().writer();
    try stdout.print("My name is Pedro!", .{});
}
```

Another point that I didn't use in this project is you can use a 'if' statement
with returned error unions and if you receive an error type in response, you will
immediately jump to the else branch where you can switch on the returned error
tpe to return a default value and ensure you handle all cases.

### Optionals

While no optionals remained in my final working project I found myself happy
with how they operated in terms of ergonomics as a developer. Similar to the
Errors, this is denoted by simply putting a '?' before the return type to denote
it as being optional. With this, you can then use the 'orelse' keyword to give a
default if it is null. Again, these also work with if expressions to allow
making behaviour based on if a value is defined or not.

Here are a couple of examples of Zig's optionals again stolen unashamedly from the unofficial [zig book](https://pedropark99.github.io/zig-book/Chapters/05-pointers.html#optionals-and-optional-pointers):
```JS
const num: ?i32 = 5;
if (num) |not_null_num| {
    try stdout.print("{d}\n", .{not_null_num});
}
```

```JS
const x: ?i32 = null;
const dbl = (x orelse 15) * 2;
try stdout.print("{d}\n", .{dbl});
```

### Allocators

While not being able to fully appreciate the allocator model given this project
and the minimal amount of use of allocators, I found the idea to be very
interesting. Giving the developer control over where memory is allocated and how
it is allocated along with also providing some helpful test allocators to ensure
no memory leaks are expected. I can see this being a great experience for someone
who needs/wants to worry about these sorts of things (e.g. embedded devices) and
it also gives me a better appreciation for when memory is allocated as opposed to
other languages that hide this fact from you.

### Exhaustiveness

This is one of my favourite features in Zig, for switch expressions they are
exhaustive so in my implementations of the project, as the Gameboy has a full
description for instructions, it ensured I had hit every instruction to be
disassembled which also caught some mistakes I had made while porting over from
the Go implementation (which I did first).

## Cons

### Strings

So in Zig, strings do not really exist as a type. They are represented as being
a Array or Slice of bytes (u8 in zig terms). While this is fine and I could
happily get along wit this model, one part of it always annoys me. 'const'.
That's it, this one keyword when working with strings drove me up the wall as I
was never really able to fully determine if something needed to be a '[]u8', a
'[]const u8' or something else.
§
```JS
pub fn main() !void {
    Example("ABCD")
}

pub fn Example(input: []u8) void {
   std.debug.print("{}", .{input});
}
```

This to me looks like it should work but it doesn't as the Example function
should take a '[]const u8'. To this day, I still don't fully understand handling
strings in Zig.

### Tooling

While I understand that Zig is pre 1.0 and is still a work in progress, the
tooling around it seems to be very lacklustre. No in-editor errors for anything
that is not a syntax error can be forgotten about and you will be back and forth
to your terminal to rebuild and find out what you've done wrong. This is not a
great developer experience loop and there is not 'watch' mode in order to keep
this build running and see the errors you have made (though I believe that the
master branch has experimental support for this).

Given other languages I've used that are also quite young (take Gleam for
example) and it has given me a want to have better tooling upfront for a
language. Perhaps this will be the case once the language is more stable with
breaking changes happening all the time but it definitely makes it difficult to
recommend except for enthusiasts (though many real-world projects use it such as
the Ghostty terminal and TigerBeetle Database).

# Go

## Pros

### Tooling

Go's tooling is sublime. I don't think there's any other way to put it. Using
basically any IDE they are all able to identify and use 'gopls' (the go language
server) and the 'go fmt' command is built right in to the binary. No additional
things needed. In VS Code especially, you are prompted to download and install
all the relevant tools when you first install the plugin if you do not have them
installed already.

### Simplicity

Go is a very simple language to learn. It reminds me of C, not much in there but
it works and yes you can still shoot yourself in the foot with it. However, I
think this also makes the Go code really easy to read and understand. No
inheritance, No polymorphism, until recently there was even no Generics (and I
also haven't used them in my implementations).

For me coming from a C# Web Dev background, this feels very much like a breath of fresh air and forces me to think more about the problem instead of how I can make it as generic as possible.

## Cons

### Non mandatory error handling

This one bit me a few times. Unlike Zig, go's errors are returned as a second
value. This makes it great for writing scripts where you just want to throw away
the error, however when trying to ensure you are building a proper system, it
makes it to easy for you to **think** you know better than the compiler.

I had many a time where I was certain I was not going to have an error and yet
on many occasions, I found myself wondering why I was getting incorrect input
and it was due to not handling the error and accidentally using a zero value (Go
usually give you and error **and** a zero value return when using functions that
can error)

Here is an example:
```go
func CanError() int, error {
  if rand.IntN(100) < 50 {
    return 0, fmt.Errorf('Sorry no')
  }
  return 42, nil
}

//Capturing the error
meaningOfLife, err := CanError()
if err != nil {
  //Do something
}

// _ discards error and now I have no way to check it
defaultValue, _ := CanError()
```

### Statements over expressions

This may just be me being to used to the languages that have expressions for
many types of language constructs (e.g. 'for' in Scala, match in F# and Rust,
Even 'switch' in Zig). This however for me was frustrating. Having to go back to
declaring a variable before doing a switch and ensuring it is set before
returning it.

Given Go's target at being a simple language, it seems odd to me that they have
both a 'switch' and an 'if' as I feel the if handles all the cases (albeit not
the most visually appealing code). This is a small part but was a bit of a gripe
for me.

### Exhaustiveness

There is none, enough said, I wish they had it for cases like switch over a
integer or other simple manners. Given Go emphasised the use of the 'errors as
values' for one of it's key selling points, seems odd to have missed something
that could be a massive save for developers dealing with larger switch
statements.

For Example, I would expect the following to yield a warning or an error:
```go
x := 42
switch x {
  1 => fmt.Println("The one and only")
}
//No error and I've not caught a case it should have
```

Another reason this annoys me is in Go, you do an 'enum' by just creating a list of '[iota](https://go.dev/wiki/Iota)' values which are just integers and I cannot prove I have handled all cases that can be valid.

# Comparisons

## Binary Type (static vs dynamically linked)
As far as I am aware, both languages produce static binaries. I know there are cases where go will not create a static binary however I also believe these are few and far between. To ensure this is not the case, you can disable any CGO using 'CGO_ENABLED=0' before a go build command (at least on MacOS and Linux, I am uncertain on windows but you can look this up if you are on Windows)

## Binary Size

| Language | Bytes     | Mb  |
| -------- | --------- | --- |
| Go       | 2,409,472 | 2.4 |
| Zig      | 814,776   | 0.8 |

Go does have further optimisations you can do such as stripping out debug
symbols however this only took the binary size down to 1.6Mb (using flags -s -w)

The difference here is both surprising and not surprising.

As we have determined above, they are both static binaries which include
everything required to make it work. So in the case of Zig, you get... well you
get just the binary however with Go, we have an entire runtime that we need to
consider. Yes it can strip out all of the standard library we don't use but
there is still a garbage collector to consider.

The main part that does surprise me is the sheer size difference here, Zig is
less than 1/3 the size of the Go program which for space constrained
environments (microcontrollers etc) would be very beneficial. However I think
that Go's 2.5Mb is still a very small binary all things considered (at least to
my very limited view at binary sizes over my years of development)

## Execution Time and usage

### Timings

| Language | Total Time | CPU %age |
| -------- | ---------- | -------- |
| Go       | 0.228s     | 20       |
| Zig      | 0.180s     | 94       |

#### Raw time command outputs (aligned)

- GoLang : ./gogb 0.04s user 0.01s system 20% cpu 0.228 total
- Zig : ./ziggb 0.06s user 0.11s system 94% cpu 0.180 total

These benchmarks were done on a MacBook with an M4 Pro chip.

So this here does surprise me the most. The CPU usage on both seem very high for
what the program is doing however this could be some optimisations kicking in or
it could've been just be some overhead from reading in the file, I'm uncertain. If
anyone has any idea how I can do a better job digging into this for my own
understanding also, please let me know as these numbers do surprise me.

Still, ignoring the CPU usage, we see that Go (at 0.228 seconds) is around 20% slower than Zig (at 0.180). Again not too surprising given the garbage collector and I also give props to both languages for being this fast.

It is possible this difference could be a game breaker and could get larger as a program grows, however I think that for smaller programs, you should not be making the language choice based on speed.

# Outro
I'm not going to make any sweeping conclusions about which is better, they each have their place in the world and they are both good for different reason. Yes I have a preference and so probably would anyone who had a chance to experience both languages and I encourage you to go and have a play with them and hopefully you find things that are similarly interesting to you to compare and if not, at leas you tried something new.

Thanks for reading and i'll see you in the next one.