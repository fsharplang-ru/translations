# Parsing a single float

Parsing input with FParsec involves two steps:

1. Building a parser
2. Applying the parser to the input.

Let's start with a simple example: parsing a single floating-point number in a string.

In this case the first step, building the parser, is trivial, because the [`CharParsers`](http://www.quanttec.com/fparsec/reference/charparsers.html) module already comes with a built-in float parser:

```fsharp
val pfloat: Parser<float,'u>
```

The generic type [`Parser<'Result,'UserState>`](http://www.quanttec.com/fparsec/reference/primitives.html#members.Parser) is the type of all parsers in FParsec. 
If you follow the hyperlink into the reference, you'll see that `Parser` is a type abbreviation for a function type. 
However, at this point we don't need to go into the details of the `Parser` type. It's enough to note that the first type argument represents the type of the parser result. 
Thus, in the case of [`pfloat`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.pfloat) the type tells us that if the parser succeeds it returns a floating-point number of type `float`. 
We won't use a "user state" in this tutorial, so you can just ignore the second type argument for the time being.

To apply the `pfloat` parser to a string, we can use the [`run`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.run) function from the `CharParsers` module:

```fsharp
val run: Parser<'Result, unit> -> string -> ParserResult<'Result,unit>
```

`run` is the simplest function out of [several](http://www.quanttec.com/fparsec/reference/charparsers.html#interface.runparser-functions) provided by the `CharParsers` module for running parsers on input. 
Other functions allow you, for example, to run parsers directly on the contents of a file or a `System.IO.Stream`.

`run` applies the parser passed as the first argument to the string passed as the second argument and returns the return value of the parser in form of a `ParserResult` value. 
The [`ParserResult`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.ParserResult) type is a discriminated union type with the two cases:  `Success` and `Failure`. 
In case the parser succeeds, the `ParserResult` value contains the result value, otherwise it contains an error message.

To simplify testing we write a little helper function that prints the result value or error message:

```fsharp
let test p str =
    match run p str with
    | Success(result, _, _)   -> printfn "Success: %A" result
    | Failure(errorMsg, _, _) -> printfn "Failure: %s" errorMsg
```

With this helper function in place, we can test `pfloat` by executing

```fsharp
test pfloat "1.25"
```
which produces the output

```fsharp
Success: 1.25
```

Testing `pfloat` with a number literal that has an invalid exponent

```fsharp
test pfloat "1.25E 3"
```

yields the error message

```fsharp
Failure: Error in Ln: 1 Col: 6
1.25E 3
     ^
Expecting: decimal digit
```