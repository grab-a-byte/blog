# Int Parser Challenge

### 2025-07-20

# The Challenge
The challenge idea is simple. You get a string (something like "123") and from that you must return a 32 bit integer. You can use everything in the language of your chosing except the built-in Int.Parse (or equivelent) method.

How you choose to handle errors is entirely up to you, you can return a null or optional value, you can throw an exception, you can return an error type, the choice is entirely yours.

Before I start to talk about the solution, I will challenge you to give this short coding problem a go before reading on to the solution. However I have also formatted this post in a particular order to explain the solution so if you are uncertain of where to start, you shoudl be able to read the next header and hopefully it will provide a hint as to the next step you are able to take.

All the code examples (up until the last one) are written in the Dart Programming language which can be found at [https://dart.dev/](https://dart.dev/)

## Characters are Integers
The first thing to know to be able to work along with this problem is to know about ASCII. The American Standard Code for Information Interchange (or ASCII) is a format that was created in 1963 and is the basic of what we will talk about today. For reference while most computers will use more modern text encoding formats such as UTF-8 or UTF-16, both of these have the same stargin encoding as ASCII making them backwards compatible; we will use ASCII in this post as it is a smaller subset and easier to deal with.

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
That means to be able to get the integer values of the actual character, we can substract 48 from the character to get the values.

If you run the following code at [dartpad.dev](https://dartpad.dev/) then you will be able to see the output.

```java
void main() {
  const strInt = "842";
  final values = strInt.codeUnits;
  print(values);
  
  final mappedValues = values.map((v) => v-48);
  print(mappedValues);
}
```

Again, while the `codeUnits` property of strings in Dart returns UTF-16 code units, as the start of this encoding and ASCII are the same, it works for our example.

## To move left is to multiply

## Adding on the next part after shifting

## Final Code
### Dart
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

### Zig
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
    const direction: i2 = switch (str[0]) {
        '+' => 1,
        '-' => -1,
        else => 1,
    };

    if (str[0] == '+' or str[0] == '-') {
        index += 1;
    }

    var result: u32 = 0;
    for (str[index..]) |char| {
        if (char >= '0' and char <= '9') {
            result *= 10;
            result += (char - '0');
        } else {
            return error.InvalidCharacter;
        }
    }
    if (result > maximumInt32) {
        return switch (direction) {
            1 => error.ValueTooHigh,
            -1 => error.ValueTooLow,
            else => unreachable,
        };
    }

    const signedResult: i32 = @intCast(result);
    const finalResult = signedResult * direction;
    return finalResult;
}


```

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

### Ascii Table
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
| 35      | #         | Number sign           |
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
