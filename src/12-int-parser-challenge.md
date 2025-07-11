# Int Parser Challenge

### 2025-07-20

# The Challenge
The challenge idea is simple. You get a string (something like "123") and from that you must return a 32 bit integer. You can use everything in the language of your choosing except the built-in Int.Parse (or equivalent) method.

To clarify, it must match as expected: just converting to any old integer won't do, e.g. "123" -> 123 and "427" -> 427. This means you can't implement it as `input.length` or as my wonderful wife craftily found in Scala:

```scala
  input.compareTo("123")
```

How you choose to handle errors is entirely up to you, you can return a null or optional value, you can throw an exception, you can return an error type, the choice is entirely yours.

Before starting to talk about the solution, I will challenge you to give this short coding problem a go yourself before reading on to the solution. This post is formatted in steps to explain the solution; so if you are uncertain of where to start, you should be able to read the next section and hopefully it will provide a hint as to the next step you are able to take.

All the code examples (up until the last one) are written in the Dart Programming language which can be found at [https://dart.dev/](https://dart.dev/)

## Characters are Integers
The first thing to know to be able to work along with this problem is to know about ASCII. The American Standard Code for Information Interchange (or ASCII) is a encoding that was created in 1963 and is the builing block of what we will talk about today. For reference while most computers will use more modern text encoding formats such as [UTF-8](https://www.w3schools.com/charsets/ref_utf_basic_latin.asp) or [UTF-16](https://www.fileformat.info/info/charset/UTF-16/list.htm), both of these have the same starting encoding as ASCII making them backwards compatible. We will use ASCII in this post as it is a smaller subset and easier to deal with.

Below is a subset of the full ASCII table, you can see the full table at the bottom of the post under the Appendix.

| Decimal | Character | Description           |
|---------|-----------|-----------------------|
| 46      | .         | Period                |
| 47      | /         | Slash                 |
| 48      | 0         | Digit zero            |
| 49      | 1         | Digit one             |
| 50      | 2         | Digit two             |
| 51      | 3         | Digit three           |
| 52      | 4         | Digit four            |
| 53      | 5         | Digit five            |
| 54      | 6         | Digit six             |
| 55      | 7         | Digit seven           |
| 56      | 8         | Digit eight           |
| 57      | 9         | Digit nine            |
| 58      | :         | Colon                 |
| 59      | ;         | Semicolon             |
| 60      | <         | Less-than sign        |

From this table, note that the digit characters in the string are actually represented in memory as the integers 48 to 57.
That means to be able to get the integer values of the actual character, we can subtract 48 from the character to get the values.


```java
void main() {
  const strInt = "842";
  final values = strInt.codeUnits;
  print(values);
  
  final mappedValues = values.map((v) => v-48);
  print(mappedValues);
}

// OUTPUT:
// [56, 52, 50]
// (8, 4, 2)
```

While the `codeUnits` property of strings in Dart returns UTF-16 code units, as the start of this encoding and ASCII are the same, it works for our example.

You can put the above code into [dartpad.dev](https://dartpad.dev/) to have a play around and improve your understanding of this section.

## To move left is to multiply

So now we have the individual digits as integers, we need to figure out how to be able to turn that into a number that we can actually use at the right place in our final integer. By this I mean if we take the example of "123", we have the numbers 1, 2, and 3 individually but not 100 or 20 which is what we really need. So we need to figure out how to make each of these turn into the correct number.

If we need to make 2 turn into 20, we can multiply by 10, to make 1 turn into 100 we can multiply by 100. You may start to see a pattern here. The number we need to make is always some multiple of 10; 10,  100, 1000 etc. Something oddly useful in this is that the powers of 10 link to the exact numbers we need. For example

- 10 ^ 0 = 1
- 10 ^ 1 = 10
- 10 ^ 2 = 100

Seems rather handy.

So if we were to table the index of our numbers with this we see a problem

| Index | Number | 10 ^ index | Resulting |
|-------|--------|------------|-----------|
| 0     | 1      | 1          | 1         |
| 1     | 2      | 10         | 20        |
| 2     | 3      | 100        | 300       |

Immediately we see that our 300 is higher than the actual number we are trying to get (123) so this clearly isn't right. Have a think about how this could be changed to be correct before reading the next part.

The answer is to reverse our numbers before indexing them, doing so results in the following:

| Index | Number | 10 ^ index | Resulting |
|-------|--------|------------|-----------|
| 0     | 3      | 1          | 3         |
| 1     | 2      | 10         | 20        |
| 2     | 1      | 100        | 100       |

Now none of these numbers are over our expected amount.

A code example for getting these numbers could look like the following:

```java
import 'dart:math';

void main() {
  const intValues = [1,2,3];
  List<int> mapped = [];
  for(int i = 0; i < intValues.length; i++){
    // The '-1' is to stop the off by 1 error while indexing
    final value = intValues[intValues.length - i - 1];
    final multiplier = pow(10, i);
    mapped.add((value * multiplier) as int);
  }
  
  print(mapped);
}

// OUTPUT:
// [3, 20, 100]
```

You can place the code code into [dartpad.dev](https://dartpad.dev/) to have a play around with it.

## Putting it all together

Finally, we need to put them all together to find our actual final number. In the case of our example, we can simply add them all together into a final variable. This can be done with a for loop or a more functional concept (e.g. fold). An example of this code would be:

```java
void main() {
  const intValues = [3, 20, 100];
  var result = 0;
  for (var value in intValues){
    result += value;
  }
  print(result);
  print(result.runtimeType);
}
// OUTPUT:
// 123
// int

```
Putting all of these steps together gives you the following program (variable names may be different from examples).

```java
import 'dart:math';

void main() {
  String number = "123";
  
  //Section 1: Characters are Integers
  var numbers = number.codeUnits.map((v) => v - 48).toList();
  List<int> values = [];
  
  //Section 2: To move left is to multiply
  for(int i = 0; i < numbers.length; i++){
    // The '-1' is to stop the off by 1 error while indexing
    final value = numbers[numbers.length - i - 1];
    final multiplier = pow(10, i);
    values.add((value * multiplier) as int);
  }
  
  //Part 3: Putting it all together
  var result = 0;
  for(var value in values){
    result += value;
  }
  
  print(result);
  print(result.runtimeType)
}
// OUTPUT:
// 123
// int
```

I have added comments that link to each section so that the full implementation can be linked back to the parts of this post.

And just like that we have solved our problem. Now to be fair this is how the code ended up by breaking it down and discovering each step. When taking this challenge on myself, the code I produced was different and is in the next section with more explanation, but we do have a working function that will convert a string to an integer without using the built in parsing method. 

## Final Code

### Dart
In Dart, I made 2 versions, a naive version (the function named `unsafeParseInt`) which assumes we are being passed a valid input as a string. The second does checks to ensure they are all numbers and if it encounters anything not in that range, it returns null instead. This was my chosen error handling method as I also set myself the further challenge of not allowing myself to assign a variable directly and do it all with method chaining. It makes the code much harder to read but was a fun [code golf](https://en.wikipedia.org/wiki/Code_golf) challenge to myself.

```java
const AsciiZero = 48;
const AsciiNine = 57;

int unsafeParseInt(String input) =>
    input.codeUnits.map((x) => x - AsciiZero).fold(0, (a, b) => (a * 10) + b);

int? safeParseInt(String input) => input.codeUnits
    .map((x) => (x < AsciiZero || x > AsciiNine) ? null : (x - AsciiZero))
    .fold(0, (acc, value) => value == null ? null : (acc! * 10) + (value));

void main() {
  final value = unsafeParseInt("123");
  print(value);
  print(value.runtimeType);
}
```

Some questions you could ask yourself on this could be:
1) What other restrictions could I add to make this more difficult?
2) Could I implement it in another language?
3) What better ways of handling errors could there be?
4) Should I handle negative numbers?

I asked myself some of these and that lead me to the second implementation below.

### Zig
For this part, I took questions 2, 3 and 4 from the above and chose to implement it in a language that supports native errors as values. The language I chose was [Zig](https://ziglang.org/) (apologies on the syntax highlighting, it is not supported in my site generator of choice). Here you can see how not limiting yourself allows you to be more explicit with the errors to state exactly what went wrong. In this example, I also added support for negative integers (starting with a '-' ) which the Dart version did not.
```js
const std = @import("std");
const expect = std.testing.expect;

pub fn main() !void {
    const v = try parseInt("-2147483648");
    std.debug.print("Main: {}", .{v});
}

const maximumInt32 = std.math.maxInt(i32);

fn parseInt(input: ?[]const u8) !i32 {
    if (input == null) return error.NullInput;

    const str = std.mem.trim(u8, input.?, " \n\r");

    if (str.len == 0)
        return error.EmptyParameter;

    var index: usize = 0;
    //Determine if negative or positive
    const direction: i2 = switch (str[0]) {
        '+' => 1,
        '-' => -1,
        else => 1,
    };

    //Skip the starting char if it is '+' or '-'
    if (str[0] == '+' or str[0] == '-') {
        index += 1;
    }

    //Using a u32 to allow for going over max i32 value
    var result: u32 = 0;
    for (str[index..]) |char| {
        if (char >= '0' and char <= '9') {
            result *= 10;
            //Zig allows substracting of characters as they are all u8's
            result += (char - '0');
        } else {
            return error.InvalidCharacter;
        }
    }

    //Determine if number is too large to go in int32
    if (result > maximumInt32) {
        return switch (direction) {
            1 => error.ValueTooHigh,
            -1 => error.ValueTooLow,
            else => unreachable,
        };
    }

    //Some type shenanigans to make compiler happy
    const signedResult: i32 = @intCast(result);
    //Multiply by direction to give a negative number if started with '-'
    const finalResult = signedResult * direction;
    return finalResult;
}

```
Many comments have been added to the above code to help with following along for those both unfamiliar to the problem space and to Zig.

You can find the test suite for both of these in the appendix.

## Conclusion
I hope you have enjoyed reading this and I also hope you gave this challenge a try yourself before reading. If you did, feel free to share your solution with me by leaving a comment on my gist available [here](https://gist.github.com/grab-a-byte/9d8887e5dab435a389b34a02470f45ec).


## One more thing!
If you enjoyed this post or you'd like to see either a more Test Driven (TDD) approach or just see the same problem addressed in C#, you may enjoy this post, [Parse Integers Manually](https://illumonos.com/csharp/parse-integers-manually), from my friend over at [Illumonos](https://illumonos.com/).

(If you came from there, thanks for checking out my post also, apologies for the looping links but feel free to read Illumonos's post again!)

## Appendix

### Dart Final Code Tests
```java
import 'package:test/test.dart';

import 'main.dart';

void main() {
  group("unsafe parse int", () {
    final testCases = [
      ("1", 1),
      ("123", 123),
    ];

    testCases.forEach((testCase) {
      test("Given '${testCase.$1}' with type ${testCase.$1.runtimeType}, returns '${testCase.$2}' with type ${testCase.$2.runtimeType}", () {
        var result = unsafeParseInt(testCase.$1);
        expect(result, testCase.$2);
      });
    });
  });

  group("safe parse int", () {
    final testCases = [
      ("123", 123),
      ("101", 101),
      ("1", 1),
      ("    ", null),
      ("\n", null),
      ("abc", null)
    ];

    testCases.forEach((testCase) {
      test("Given '${testCase.$1}' with type ${testCase.$1.runtimeType}, returns '${testCase.$2}' with type ${testCase.$2.runtimeType},", () {
        var result = safeParseInt(testCase.$1);
        expect(result, testCase.$2);
      });
    });
  });
}
```

### Zig Final Code Tests
```js
test "parses successfully" {
    const result = try parseInt("1");
    try expect(result == 1);

    const result2 = try parseInt("123");
    try expect(result2 == 123);

    const result3 = try parseInt("   456");
    try expect(result3 == 456);

    const result4 = try parseInt("457    ");
    try expect(result4 == 457);

    const result5 = try parseInt("    458    ");
    try expect(result5 == 458);

    const result6 = try parseInt("-789");
    try expect(result6 == -789);

    const result7 = try parseInt("+42");
    try expect(result7 == 42);

    const result8 = try parseInt("0");
    try expect(result8 == 0);

    const result9 = try parseInt("00");
    try expect(result9 == 0);
}

test "parses unsuccessfully" {
    _ = parseInt(null) catch |err| switch (err) {
        error.NullInput => try expect(true),
        else => try expect(false),
    };

    _ = parseInt("") catch |err| switch (err) {
        error.EmptyParameter => try expect(true),
        else => try expect(false),
    };

    _ = parseInt("   ") catch |err| switch (err) {
        error.EmptyParameter => try expect(true),
        else => try expect(false),
    };

    _ = parseInt("abc") catch |err| switch (err) {
        error.InvalidCharacter => try expect(true),
        else => try expect(false),
    };

    _ = parseInt("12a3") catch |err| switch (err) {
        error.InvalidCharacter => try expect(true),
        else => try expect(false),
    };

    _ = parseInt("--123") catch |err| switch (err) {
        error.InvalidCharacter => try expect(true),
        else => try expect(false),
    };

    _ = parseInt("2147483648") catch |err| switch (err) {
        error.ValueTooHigh => try expect(true),
        else => try expect(false),
    };

    _ = parseInt("-2147483648") catch |err| switch (err) {
        error.ValueTooLow => try expect(true),
        else => try expect(false),
    };
}
```

### Full Ascii Table
| Decimal | Character | Description           |
|---------|-----------|-----------------------|
| 0       | NUL       | Null character        |
| 1       | SOH       | Start of Header       |
| 2       | STX       | Start of Text         |
| 3       | ETX       | End of Text           |
| 4       | EOT       | End of Transmission   |
| 5       | ENQ       | Enquiry               |
| 6       | ACK       | Acknowledge           |
| 7       | BEL       | Bell                  |
| 8       | BS        | Backspace             |
| 9       | TAB       | Horizontal Tab        |
| 10      | LF        | Line Feed             |
| 11      | VT        | Vertical Tab          |
| 12      | FF        | Form Feed             |
| 13      | CR        | Carriage Return       |
| 14      | SO        | Shift Out             |
| 15      | SI        | Shift In              |
| 16–31   |           | Control characters    |
| 32      | SPACE     | Space                 |
| 33      | !         | Exclamation mark      |
| 34      | "         | Double quote          |
| 35      | #         | Hash sign             |
| 36      | $         | Dollar sign           |
| 37      | %         | Percent sign          |
| 38      | &         | Ampersand             |
| 39      | '         | Single quote          |
| 40      | (         | Left parenthesis      |
| 41      | )         | Right parenthesis     |
| 42      | *         | Asterisk              |
| 43      | +         | Plus sign             |
| 44      | ,         | Comma                 |
| 45      | -         | Hyphen                |
| 46      | .         | Period                |
| 47      | /         | Slash                 |
| 48      | 0         | Digit zero            |
| 49      | 1         | Digit one             |
| 50      | 2         | Digit two             |
| 51      | 3         | Digit three           |
| 52      | 4         | Digit four            |
| 53      | 5         | Digit five            |
| 54      | 6         | Digit six             |
| 55      | 7         | Digit seven           |
| 56      | 8         | Digit eight           |
| 57      | 9         | Digit nine            |
| 58      | :         | Colon                 |
| 59      | ;         | Semicolon             |
| 60      | <         | Less-than sign        |
| 61      | =         | Equals sign           |
| 62      | >         | Greater-than sign     |
| 63      | ?         | Question mark         |
| 64      | @         | At sign               |
| 65      | A         | Uppercase A           |
| 66      | B         | Uppercase B           |
| 67      | C         | Uppercase C           |
| 68      | D         | Uppercase D           |
| 69      | E         | Uppercase E           |
| 70      | F         | Uppercase F           |
| 71      | G         | Uppercase G           |
| 72      | H         | Uppercase H           |
| 73      | I         | Uppercase I           |
| 74      | J         | Uppercase J           |
| 75      | K         | Uppercase K           |
| 76      | L         | Uppercase L           |
| 77      | M         | Uppercase M           |
| 78      | N         | Uppercase N           |
| 79      | O         | Uppercase O           |
| 80      | P         | Uppercase P           |
| 81      | Q         | Uppercase Q           |
| 82      | R         | Uppercase R           |
| 83      | S         | Uppercase S           |
| 84      | T         | Uppercase T           |
| 85      | U         | Uppercase U           |
| 86      | V         | Uppercase V           |
| 87      | W         | Uppercase W           |
| 88      | X         | Uppercase X           |
| 89      | Y         | Uppercase Y           |
| 90      | Z         | Uppercase Z           |
| 91      | [         | Left square bracket   |
| 92      | \         | Backslash             |
| 93      | ]         | Right square bracket  |
| 94      | ^         | Caret                 |
| 95      | _         | Underscore            |
| 96      | `         | Grave accent          |
| 97      | a         | Lowercase a           |
| 98      | b         | Lowercase b           |
| 99      | c         | Lowercase c           |
| 100     | d         | Lowercase d           |
| 101     | e         | Lowercase e           |
| 102     | f         | Lowercase f           |
| 103     | g         | Lowercase g           |
| 104     | h         | Lowercase h           |
| 105     | i         | Lowercase i           |
| 106     | j         | Lowercase j           |
| 107     | k         | Lowercase k           |
| 108     | l         | Lowercase l           |
| 109     | m         | Lowercase m           |
| 110     | n         | Lowercase n           |
| 111     | o         | Lowercase o           |
| 112     | p         | Lowercase p           |
| 113     | q         | Lowercase q           |
| 114     | r         | Lowercase r           |
| 115     | s         | Lowercase s           |
| 116     | t         | Lowercase t           |
| 117     | u         | Lowercase u           |
| 118     | v         | Lowercase v           |
| 119     | w         | Lowercase w           |
| 120     | x         | Lowercase x           |
| 121     | y         | Lowercase y           |
| 122     | z         | Lowercase z           |
| 123     | {         | Left curly brace      |
| 124     | \|        | Vertical bar          |
| 125     | }         | Right curly brace     |
| 126     | ~         | Tilde                 |
| 127     | DEL       | Delete                |
