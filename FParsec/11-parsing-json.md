# Синтаксический анализ JSON"

Now that we have discussed the basics of FParsec we are well prepared to work through a real world parser example: a JSON parser.

JSON (JavaScript Object Notation) is a text-based data interchange format with a simple and lightweight syntax. You can find descriptions of the syntax on [json.org](http://json.org) and in [RFC 4626](http://www.ietf.org/rfc/rfc4627.txt).

In many applications one only has to deal with JSON files describing one particular kind of object. In such a context it sometimes can be appropriate to write a specialized parser just for that specific kind of JSON file. In this tutorial, however, we will follow a more general approach. We will implement a parser that can parse any general JSON file into an AST, i.e. an intermediate data structure describing the contents of the file. Applications can then conveniently query this data structure and extract the information they need. This is an approach comparable to that of XML parsers which build a data structure describing the document tree of an XML document. The great advantage of this approach is that the JSON parser itself becomes reusable and the document specific parsing logic can be expressed in the form of simple functions processing the AST of the JSON document.

The natural way to implement an AST in F# is with the help of a discriminated union type. If you look at the [JSON specification](http://json.org), you can see that a JSON value can be a string, a number, a boolean, null, a comma-separated list of values in square brackets, or an object with a sequence of key-value pairs in curly brackets.

In our parser we will use the following union type to represent JSON values:

```fsharp
type Json = JString of string
          | JNumber of float
          | JBool   of bool
          | JNull
          | JList   of Json list
          | JObject of Map<string, Json>
```

Here we've chosen the F# `list` type to represent a sequence of values and the `Map` type to represent a sequence of key-value pairs, because these types are particularly convenient to process in F#.[fn If you need to parse huge sequences and objects, it might be more appropriate to use an array and dictionary for JList and JObject respectively.] Note that the `Json` type is recursive, since both `JList` and `JObject` values can themselves contain `Json` values. Our parser will have to reflect this recursive structure.

___
###Tip
If you're new to FParsec and have a little time, it would be a good exercise to try to implement the JSON parser on your own (with the help of the reference documentation). This tutorial already covered almost everything you need and the JSON grammar is simple enough that this shouldn't take too much time. Of course, you can always peek at the implementation below if you get stuck.
___

We start the actual parser implementation by covering the simple `null` and boolean cases:

```fsharp
let jnull = stringReturn "null" JNull
let jool =      (stringReturn "true"  (JBool true))
            <|> (stringReturn "false" (JBool false))
```

Handling the number case is just as simple, because the JSON number format is based on the typical floating-point number format used in many programming languages and hence can be parsed with FParsec's built-in `pfloat` parser:

```fsharp
let jnumber = pfloat |>> JNumber
```

(Note that F# allows us to pass the object constructor `JNumber` as a function argument.)

If you compare the precise number format supported by `pfloat` with that in the JSON spec, you'll see that `pfloat` supports a superset of the JSON format. In contrast to the JSON format the `pfloat` parser also recognizes `NaN` and `Infinity` values, accepts a leading plus sign, accepts leading zeros and even supports the hexadecimal float format of Java and C99. Depending on the context this behaviour can be considered a feature or a limitation of the parser. For most applications it probably doesn't matter, and the JSON RFC clearly states that a JSON parser may support a superset of the JSON syntax. However, if you'd rather only support the exact JSON number format, you can implement such a float parser rather easily based on the configurable `numberLiteral` parser (just have a look at how this is currently done in the `pfloat` source).

The JSON string format takes a little more effort to implement, but we've already parsed a similar format with the `stringLiteral` parsers in [Parsing string data](#Parsing string data), so we can just adapt one of those parsers for our purpose:

```fsharp
let stringLiteral =
    let escape =  anyOf "\"\\/bfnrt"
                  |>> function
                      | 'b' -> "\b"
                      | 'f' -> "\u000C"
                      | 'n' -> "\n"
                      | 'r' -> "\r"
                      | 't' -> "\t"
                      | c   -> string c // every other char is mapped to itself

    let unicodeEscape =
    	/// converts a hex char ([0-9a-fA-F]) to its integer number (0-15)
        let hex2int c = (int c &&& 15) + (int c >>> 6)*9

        str "u" >>. pipe4 hex hex hex hex (fun h3 h2 h1 h0 ->
            (hex2int h3)*4096 + (hex2int h2)*256 + (hex2int h1)*16 + hex2int h0
            |> char |> string
        )

    let escapedCharSnippet = str "\\" >>. (escape <|> unicodeEscape)
    let normalCharSnippet  = manySatisfy (fun c -> c <> '"' && c <> '\\')

    between (str "\"") (str "\"")
            (stringsSepBy normalCharSnippet escapedCharSnippet)
```

`stringLiteral` parses string literals as a sequence of normal char snippets separated by escaped char snippets. A normal char snippet is any sequence of chars that does not contain the chars `'"'` and `'\\'`. An escaped char snippet consists of a backslash followed by any of the chars `'\\'`, `'\"'`, `'/'`, `'b'`, `'f'`, `'n'`, `'r'`, `'t'`, or an Unicode escape. An Unicode escape consists of an `'u'` followed by four hex chars representing an UTF-16 code point.

[#createParserForwardedToRef-example]
The grammar rules for JSON lists and objects are recursive, because any list or object can contain itself any kind of JSON value. Hence, in order to write parsers for the list and object grammar rules, we need a way to refer to the parser for any kind of JSON value, even though we haven't yet constructed this parser. Like it is so often in computing, we can solve this problem by introducing an extra indirection:

```fsharp
let jvalue, jvalueRef = createParserForwardedToRef<Json, unit>()
```

As you might have guessed from the name, `createParserForwardedToRef` creates a parser (`jvalue`) that forwards all invocations to the parser in a reference cell (`jvalueRef`). Initially, the reference cell holds a dummy parser, but since the reference cell is mutable, we can later replace the dummy parser with the actual value parser, once we have finished constructing it.

The JSON RFC sensibly only permits spaces, (horizontal) tabs, line feeds and carriage returns as whitespace characters, which allows us to use the built-in `spaces` parser for parsing whitespace:

```fsharp
let ws = spaces
```

Both JSON lists and objects are syntactically represented as a comma-separated lists of "elements" between brackets, where whitespace is allowed before and after any bracket, comma and list element. We can conveniently parse such lists with the following helper function:

```fsharp
let listBetweenStrings sOpen sClose pElement f =
    between (str sOpen) (str sClose)
            (ws >>. sepBy (pElement .>> ws) (str "," >>. ws) |>> f)
```

This function takes four arguments: an opening string, a closing string, an element parser and a function that is applied to the parsed list of elements.

With the help of this function we can define the parser for a JSON list as follows:

```fsharp
let jlist   = listBetweenStrings "[" "]" jvalue JList
```

JSON objects are lists of key-value pairs, so we need a parser for a key-value pair:

```fsharp
let keyValue = stringLiteral .>>. (ws >>. str ":" >>. ws >>. jvalue)
```

(Remember, the points on both sides of `.>>.` indicate that the results of the two parsers on both sides are returned as a tuple.)

By passing the `keyValue` parser to `listBetweenStrings` we obtain a parser for JSON objects:

```fsharp
let jobject = listBetweenStrings "{" "}" keyValue (Map.ofList >> JObject)
```

[#json-value-parser]
Having defined parsers for all the possible kind of JSON values, we can combine the different cases with a `choice` parser to obtain the finished parser for JSON values:

```fsharp
do jvalueRef := choice [jobject
                        jlist
                        jstring
                        jnumber
                        jtrue
                        jfalse
                        jnull]
```

The `jvalue` parser doesn't accept leading or trailing whitespace, so we need to define our parser for complete JSON documents as follows:

```fsharp
let json = ws >>. jvalue .>> ws .>> eof
```

This parser will try to consume a complete JSON input stream and, if successful, will return a `Json` AST of the input as the parser result

And that's it, we're finished with our JSON parser. If you want to try this parser out on some sample input, please take a look at the JSON project in the **Samples** folder.