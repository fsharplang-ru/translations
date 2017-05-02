#Preliminaries

FParsec is built as two DLLs: `FParsec.dll` and `FParsecCS.dll`. To use FParsec in your project, you can either let [NuGet](http://nuget.org) install one of the [NuGet packages](), or you can build the two FParsec DLLs from source. The easiest way to build FParsec from source is using the Visual Studio solution files in the `=Build/VS..` folders of the [source code package](), e.g. in [=Build/VS11] for Visual Studio 2012. Any project that uses FParsec has to reference both DLLs. See [Download and Installation]() for more details.

All FParsec types and modules are declared in the `FParsec` namespace. This namespace contains some basic classes (such as `CharStream` and `Reply`) and four F# modules, namely

- `Primitives`, containing basic type definitions and parser combinators
- `CharParsers`, containing parsers for chars, strings and numbers, and functions for applying parsers to input streams
- `Error`, containing types and helper functions for creating, processing and formatting parser error messages
- `StaticMapping`, containing functions for compiling static key-value mappings into optimized functions.

All code snippets in this tutorial assume that you've opened the `FParsec` namespace:

```fsharp
open FParsec
```

Opening the `FParsec` namespace also automatically opens the `Primitives`, `CharParsers` and `Error` modules.


>All code snippets in this tutorial are contained in the [=Samples/Tutorial] project. Having this project open while reading the tutorial can be quite helpful. For example, you can hover the mouse over an identifier to get an Intellisense popup with the inferred type. And if you're curious how a library function is implemented, you can click the /Go to definition/ context menu option to view its source code.
