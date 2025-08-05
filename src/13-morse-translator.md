# Morse Code Translation

### 16/7/2025

It's time for another short programming challenge. As usual, I'll be using a different language for this one and showcasing some of its features. I'll also give the outline of the challenge and allow you to give it a shot yourself before I explain how I went about it.

These challenges have been a fun little brain teaser for me and have been a nice departure from my day-to-day job, helping to keep the old mind alive. I hope you find the same thing for yourself.

Anything in _italics_ is a good stopping point for you to have a think about the challenge for yourself and maybe try the next part by copying the snippets in the post to that point. 

## Challenge constraints
_If you do not wish to partake in the challenge and you just want to read about the solution (written in Go), then please feel free to jump to the next header named [Solution](#solution)_

You will be given a string as input. You must determine if the string is a Morse code input (e.g. ".- .---") or an alphanumeric input (e.g. "Hello World"). From there, you must translate to the opposite type and output a new string. The encoding is as follows in both directions:

| Character | Morse |
| --------- | ----- |
| A         | .-    |
| B         | -...  |
| C         | -.-.  |
| D         | -..   |
| E         | .     |
| F         | ..-.  |
| G         | --.   |
| H         | ....  |
| I         | ..    |
| J         | .---  |
| K         | -.-   |
| L         | .-..  |
| M         | --    |
| N         | -.    |
| O         | ---   |
| P         | .--.  |
| Q         | --.-  |
| R         | .-.   |
| S         | ...   |
| T         | -     |
| U         | ..-   |
| V         | ...-  |
| W         | .--   |
| X         | -..-  |
| Y         | -.--  |
| Z         | --..  |
| 0         | ----- |
| 1         | .---- |
| 2         | ..--- |
| 3         | ...-- |
| 4         | ....- |
| 5         | ..... |
| 6         | -.... |
| 7         | --... |
| 8         | ---.. |
| 9         | ----. |
| ' '       | /     |

There are also the following test cases, which can be used to check your implementation:

### Morse to Text test cases

| Input                                                                | Output           | Expected Error                             |
|----------------------------------------------------------------------|------------------|--------------------------------------------|
| ./                                                                   | "A"              | None                                       |
| -...                                                                 | "B"              | None                                       |
| -..                                                                  | "D"              | None                                       |
| .                                                                    | "E"              | None                                       |
| .----                                                                | "1"              | None                                       |
| .... . .-.. .-.. --- / .-- --- .-. .-.. -..                          | "Hello World"    | None                                       |
| "  " (Two spaces)                                                    | None             | Error, double spaces not allowed           |
| ..-..-..-..-..-..                                                    | None             | Error, invalid morse character             |
| .- .- A                                                              | None             | Error, invalid character (A)               |
| _ (Underscore)                                                       | None             | Error, invalid morse character             |
| — (May need to copy string as it is a character that looks like a -) | None             | Error, invalid Morse character             |
| − (May need to copy string as it is a character that looks like a -) | None             | Error, invalid Morse character             |

When encoding to a string, the output should be all uppercase (due to Morse's case insensitivity)

### Text to Morse test cases

| Input                                                                | Output                                         | Expected Error                   |
|----------------------------------------------------------------------|------------------------------------------------|----------------------------------|
| A                                                                    | .-                                             | None                             |
| B                                                                    | -...                                           | None                             |
| D                                                                    | -..                                            | None                             |
| E                                                                    | .                                              | None                             |
| 1                                                                    | .----                                          | None                             |
| Hello World                                                          | .... . .-.. .-.. --- / .-- --- .-. .-.. -..    | None                             |
| "  " (Two spaces)                                                    | None                                           | Error, double spaces not allowed |
| ..-..-..-..-..-..                                                    | None                                           | Error, invalid morse character   |
| "A sentence."                                                        | None                                           | Error, invalid character (.)     |
| "@#$%"                                                               | None                                           | Error, invalid characters (@#$%) |
| _ (Underscore)                                                       | None                                           | Error, invalid morse character   |
| — (May need to copy string as it is a character that looks like a -) | None                                           | Error, invalid Morse character   |
| − (May need to copy string as it is a character that looks like a -) | None                                           | Error, invalid Morse character   |

When encoding as Morse, all letters will be separated by a space, and all words will be encoded as '/'. Also, Morse is case-insensitive, so you should be able to use both upper and lowercase characters for the message.
  
## Solution
So now we have the test cases and the parameters set, let's get on with the implementation! At any point, you can check out the full code for this at [my Github repo for this challenge](https://github.com/grab-a-byte/morse-translate/).

### Getting the translations in place
So first and foremost, we need to have some way to get these translations and store them in the application. Immediately, my first thought is a Golang map (many languages call them different things, such as `Dictionary`, but Go calls them maps!). But I also don't want to make a duplicate copy of these translations, where adding a new character means remembering to add it in both places. Ok, so I can loop over some key value pairs and put them into 2 dictionaries, one for encoding, and one for decoding. So what is the best way to go about this? It can't be done statically, or can it?

No, at least not in Go sadly! We can however use something that is very close and while not static, does allow us to initialize variables at runtime based on some logic without us calling it.

In Go, there is this magic function called 'init' which, when in a module, gets ran as soon as the module is loaded. This would allow us to do some dynamic processing, but without us having to explicitly call it, and make it invisible to any users of the library. So let's look at this.

```Go
var encryptMap map[rune]MorseCode = make(map[rune]MorseCode)
var decryptMap map[MorseCode]rune = make(map[MorseCode]rune)

func init() {
	codes := map[rune]MorseCode{
		'A': ".-",
		'B': "-...",
		'C': "-.-.",
		'D': "-..",
		'E': ".",
		'F': "..-.",
		'G': "--.",
		'H': "....",
		'I': "..",
		'J': ".---",
		'K': "-.-",
		'L': ".-..",
		'M': "--",
		'N': "-.",
		'O': "---",
		'P': ".--.",
		'Q': "--.-",
		'R': ".-.",
		'S': "...",
		'T': "-",
		'U': "..-",
		'V': "...-",
		'W': ".--",
		'X': "-..-",
		'Y': "-.--",
		'Z': "--..",
		'0': "-----",
		'1': ".----",
		'2': "..---",
		'3': "...--",
		'4': "....-",
		'5': ".....",
		'6': "-....",
		'7': "--...",
		'8': "---..",
		'9': "----.",
		' ': "/",
	}

	for c, v := range codes {
		encryptMap[c] = v
		decryptMap[v] = c
	}
}
```
_For reference, in Go, a character is called a `rune` so you can substitute the word `rune` with `character` if that helps with reading the code samples._

Just like that, the first time the module is loaded into the application, all this code runs and our private global module variables (`encryptMap` and `decryptMap`) are filled in.

Now it's time to use these translations. Let's start with the Text to Morse translations!

### Text to Morse

#### Happy pathing it

_If you're following along at home and you want to know a hint at what is to come before we go and do this, I'd recommend you read up on the following features of Go and see if you can work out from there_
- _Maps_
- _Range Loops_
- _the `strings` package_

_Hope this helps!_

So firstly, just to give us a little assistance with readability and to make us think about what we are returning, we're going to make a type for Morse Code. This will simply wrap a string, but it will at least make it easier for us to read in this small challenge. In a proper production system, we'd likely wrap this type with a function called 'New' (a convention I've seen around in Golang) to ensure it is valid Morse before converting to this type; however, for this small toy example, I'm just sticking with making the type a string.

```go
  type MorseCode string
```
If you would like to know more about creating these types or using type alias' in Go, I would recommend reading [this blog post](https://go.dev/blog/alias-names) on the Go blog for more information.

So with that out of the way, we need to know how to translate each letter. We know the length of the string, and so we can pre-allocate an array for a bit of a small performance boost for where we want to store our conversions. So we start with
```go
func EncodeMorse(input string) (MorseCode, error) {
    out := make([]string, len(input))
}
```
and we build from here.

We need to iterate for each character and do something, so let's add in the loop for each character and we end up with the following:
```go
func EncodeMorse(input string) (MorseCode, error) {
    out := make([]string, len(input))
    for i, c := range strings.ToUpper(input) {
      
    }
}
````
You may also notice that I've also added in the `strings.ToUpper` on the input, as the mapping only deals with UPPERCASE characters and not lowercase ones, so for us to find the relevant characters, we need to ensure we have each character as an uppercase. It probably would be more efficient to write this ourselves as we can guarantee ASCII and use the new [Iterators](https://gobyexample.com/range-over-iterators) from Go 1.23 to reduce some extra checking done by the standard library but for the sake of this small challenge, we can just use the one provided by the standard library for ease's sake.

So with that little implementation detail out of the way, we move on to the actual translation. We can look up our character in the map we made earlier in our init function! If we manage to find it, we need to add it to our output array for use later.
```go
func EncodeMorse(input string) (MorseCode, error) {
    out := make([]string, len(input))
    for i, c := range strings.ToUpper(input) {
        val, ok := encryptMap[c]
        if !ok {
          //A suspiciously empty if statement
        }
        out[i] = string(val)     
    }
}
```
and bingo. If all goes well, our output array should hold all of our translated Morse code characters. If you've never programmed in Go before, the setting of multiple values from indexing on the map may be strange, but this is standard Go. You get out the value and a boolean that tells you if it actually managed to find the value you asked for, if not, the `ok` variable is false. As Go complains about unused variables, the `ok` variable is used in an empty if statement which is intentionally left empty for future use.

Finally, we need to join our newfound set of characters together to get some Morse Code!
```go
func EncodeMorse(input string) (MorseCode, error) {
    out := make([]string, len(input))
    for i, c := range strings.ToUpper(input) {
        val, ok := encryptMap[c]
        if !ok {
              //An intentionally empty if statement
        }
        out[i] = string(val)
	  }

	return MorseCode(strings.Join(out, " ")), nil
}
```

And this is our main loop done. No Error handling but it will work given valid inputs. 

_At this point, I'd recommend having a web search on how to do errors in Go if you're following along and try and add the relevant errors for yourself._

#### Errors
So now that we have a basic working implementation, we need to look at handling errors. There are 2 main types of errors that we need to handle in this use case. One for an invalid character and one for if there are double spaces in the text.

We begin by defining these errors using the standard library's `errors` package, which allows us to make some new errors from strings and assign them as variables. This is useful as we can use these variables in our tests to ensure we are getting the right error returned. 

```go

var ErrorInvalidCharacterToMorse = errors.New("invalid character to be translated to morse")

var ErrorInvalidDoubleSpaces = errors.New("double space should not be in morse code input")
```

Quite simple to define, but where to use them? Let's start with the invalid characters error. You may have noticed that we left a suspicious gap in the `if` statement in the previous segment and this is the perfect place to return this error. If we failed to find the character in our `encryptMap`, then we obviously don't know how to convert it to Morse, and as such, we can return an error with a default value in the MorseCode typed return value, which leads us to the following code.
```go
func EncodeMorse(input string) (MorseCode, error) {
	out := make([]string, len(input))
	for i, c := range strings.ToUpper(input) {
		val, ok := encryptMap[c]
		if !ok {
			return "", ErrorInvalidCharacterToMorse
		}
		out[i] = string(val)
	}

	return MorseCode(strings.Join(out, " ")), nil
}
```
The empty string is the default value for strings and as is convention in Golang, we return the default value along with an error at the first sign of an invalid character.

The second error handling is much the same, except we simply need to see if the string contains any double spaces and return the error, leading to the final implementation of this code.
```go
func EncodeMorse(input string) (MorseCode, error) {
	if strings.Contains(input, "  ") {
		return "", ErrorInvalidDoubleSpaces
	}
	out := make([]string, len(input))
	for i, c := range strings.ToUpper(input) {
		val, ok := encryptMap[c]
		if !ok {
			return "", ErrorInvalidCharacterToMorse
		}
		out[i] = string(val)
	}

	return MorseCode(strings.Join(out, " ")), nil
}
```

Here we have a function that passes all our test cases for converting to Morse Code and handles errors. You could say that it could be more efficient by checking for a space character in the loop and then using the index to check if `input[i+1]` is also a space character which would prevent looping over the entire input twice. In a battle-hardened implementation, this would probably be preferable; however, for a small challenge like this with small inputs, the basic `strings.Contains` check is absolutely fine. 

### Morse to Text

_Here we are about to try and do the reverse, given everything you have read so far, you can try and work out how to do the reverse yourself, given we have set up most of the infrastructure needed and it uses most of the same patterns._

So I don't make this blog post turn into an entire textbook, I'm going to quickly describe the differences between converting to Morse and converting from Morse before posting the full code for the decryption function.

1) We do not need to join the strings with spaces (spaces will be decoded from decoding '/')
2) We need to use the decryptMap variable instead
3) We need to return a different, more specific error to the user of the function
4) We do not need to worry about casing of the input

With these 4 differences aside and a bit of blog post magic, here is the final function

```go
func DecodeMorse(input MorseCode) (string, error) {
	if strings.Contains(string(input), "  ") {
		return "", ErrorInvalidDoubleSpaces
	}
	out := make([]string, len(input))
	codes := strings.Split(string(input), " ")
	for i, m := range codes {
		val, ok := decryptMap[MorseCode(m)]
		if !ok {
			return "", ErrorInvalidCharacterFromMorse
		}
		out[i] = string(val)
	}

	return strings.Trim(strings.Join(out, ""), " "), nil
}
```
Looks very very similar, and that's because it is! It's still a conversion from one thing to another just with some small changes sprinkled in.

When I showed this to a friend he said "They are so similar, why not just make a reusable function and pass in the different dictionaries, etc", and it's a fair point. However, I do feel that there are a couple of reasons not to do that
1) Just because we can reuse doesn't mean we should.
    - It allows us to keep the implementations completely decoupled from each other
2) Go has a mantra of "a little copying is better than a little dependency".
    - While it's not a dependency in the normal sense, it's a dependency between these 2 functions, so no harm in copying and making adjustments.
    - If you want to know more about these, see the [Go Proverbs](https://go-proverbs.github.io/) website, which lists them and also links to the video where Rob Pike (one of the creators of Go) talks about them.

(There are probably more than these, but these are 2 of the main ones I can think of right now.)

However, in my haste when making this challenge, I've actually created an inefficiency in this `DecodeMorse` function. I'll add in the appendix at the bottom of the post for those interested but I would urge you to have a look and see if you can figure it out for yourself.


## Wrapping into utilities
Now that we've made this little library to be able to go to and from Morse Code, it's useful to be able to use it in some way. For this, I took it and created a couple of different ways to interact with this. A CLI tool and an API endpoint.

The CLI tool has a smaller amount of code and checks so I'll quickly show its implementation here; it's also a good way to show off how good Go is for CLIs.
```go
package main

import (
	"flag"
	"fmt"
	"morse/morse"
	"os"
)

func main() {
	typePtr := flag.String("type", "", "Specifies the type of the requested translation")
	flag.Parse()
	input := flag.Arg(0)

	if *typePtr == "MorseCode" {
		res, err := morse.DecodeMorse(morse.MorseCode(input))
		if err != nil {
			fmt.Printf("%s\n", err)
			os.Exit(43)
		}

		fmt.Println(res)
		return
	} else if *typePtr == "Text" {
		res, err := morse.EncodeMorse(input)
		if err != nil {
			fmt.Printf("%s\n", err)
			os.Exit(41)
		}

		fmt.Println(res)
		return
	}
:
	res, err := morse.Translate(input)
	if err != nil {
		fmt.Printf("%s\n", err)
		os.Exit(100)
	}

	fmt.Println(res)
}
```

Here we are simply importing our `morse` package and then based on a flag named "type" we are able to determine which way to convert.  Here the use of the flag package is what allows for simple and easy parsing of command line arguments and it also allows a few forms that may be familar to you if you have used many command line tools in the past.
```
  -flag
  --flag   // double dashes are also permitted
  -flag=x
  -flag x  // non-boolean flags only
```
(These are stolen directly from the flag package's documentation as of the time of writing this.)

Each path changes the function being used and simply prints out any errors from translating. There is also a catch-all case which uses a generic `Translate()` function, which is a function I've implemented that tries to determine which one we are translating from and calls the correct function. I'd recommend you give this generic function a try yourself, as there could be many different ways to be able to determine which type of string it is. 

You can take a look at the source code for both tools at the following:
- [Command Line](https://github.com/grab-a-byte/morse-translate/blob/main/cmd/cmd/cmd.go)
- [API](https://github.com/grab-a-byte/morse-translate/blob/main/cmd/api/api.go)

This follows the Go standard convention of putting entrypoints in the `cmd` folder and having a folder for each of the specialisms for the main core logic of the code.

## Things we have learned
This article has been a long one, but there have been many techniques and tools of the Go Programming Language, which are as follows:
- `init` functions
- Module level variables
- How Go handles errors
- Standard library packages (`strings`, `flag` and `errors`)
- Initialising arrays with a predefined length for performance
- Using Go maps and handling missing values

And of course we also learned a bit about Morse Code along the way also. 

## Summary
If you are here, thanks for reading through the whole post and sticking with it to the end. If you have any feedback, make an issue on the repo to let me know and I'll get back to you. Again, thanks for sticking through this long post!

Furthermore, if you'd like to see this broken down in C# and into a bit more detail, you can find some posts from my friend over on Illumonos:
- [String to Morse](https://illumonos.com/csharp/morse-code-converter)
- [Morse to String](https://illumonos.com/csharp/morse-code-parser)

## Appendix

### Decode Inefficiency
If we take a look at the `DecodeMorse` function posted above, you may have noticed that I had added a `strings.Trim` function call which probably didn't need to be there. This was a hint to the direction of the inefficiency/bug that was included in that implementation.

The problem in question is the line `out := make([]string, len(input))` which is creating an array with the length of the Morse input. However, all Morse is multiple characters per single character in the output. As such, we should really be splitting on the space character (our separator for Morse) and doing an array based on the length of this split, as that is how many single characters we will actually translate. As such, an amended and more efficient/correct version of the implementation looks as follows:
```go
func DecodeMorse(input MorseCode) (string, error) {
	if strings.Contains(string(input), "  ") {
		return "", ErrorInvalidDoubleSpaces
	}

	codes := strings.Split(string(input), " ")
	out := make([]string, len(codes))
	for i, m := range codes {
		val, ok := decryptMap[MorseCode(m)]
		if !ok {
			return "", ErrorInvalidCharacterFromMorse
		}
		out[i] = string(val)
	}

	return strings.Join(out, ""), nil
}
```
