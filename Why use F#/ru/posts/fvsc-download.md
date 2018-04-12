---
layout: post
title: "Сравнение F# и C#: Загрузка веб-страницы"
description: "Где мы увидим, что F# превосходит в обратных вызовах, а также мы познакомимся с ключевым словом 'use'"
nav: why-use-fsharp
seriesId: "Зачем использовать F#?"
seriesOrder: 5
categories: [F# vs C#]
---

В этом примере мы сравним код для загрузки веб-страницы, использующий обратный вызов для обработки текстового потока, на F# с аналогичным кодом на C#.

Начнём с простой реализации на F#:

> In this example, we will compare the F# and C# code for downloading a web page, with a callback to process the text stream.

> We'll start with a straightforward F# implementation.

```fsharp
// "open" делает видимыми типы в указанном пространстве имён .NET (аналогично директиве using в C#)
open System.Net
open System
open System.IO

// Извлечение содержимого веб-страницы
let fetchUrl callback url =
    let req = WebRequest.Create(Uri(url))
    use resp = req.GetResponse()
    use stream = resp.GetResponseStream()
    use reader = new IO.StreamReader(stream)
    callback reader url
```

Давайте рассмотрим этот код:

* Использование "open" в начале позволяет использовать "WebRequest" вместо "System.Net.WebRequest". Подобно "`using System.Net`" заголовку в C#.
* Далее, мы задаём функцию `fetchUrl`, которая принимает два аргумента: обратный вызов для обработки потока и URL извлекаемой страницы.
* Затем мы "оборачиваем" строку с URL в объект `Uri`. F# имеет строгую проверку типов, поэтому если бы вместо этого мы написали: `let req = WebRequest.Create(url)`, компилятор "пожаловался" бы, что не знает, какую из версий `WebRequest.Create` использовать.
* При объявлении значений `response`, `stream` и `reader` используется ключевое слово "`use`" вместо ключевого слова "`let`". Это может использоваться только в сочетании с классами, реализующими `IDisposable`. Оно диктует компилятору автоматически освободить ресурсы, когда значение выходит из области видимости. Это эквивалентно оператору "`using`" в C#.
* В последней строке вызывается функция обратного вызова со StreamReader и URL в качестве параметров. Заметьте, что нет необходимости где-либо явно указывать тип функции обратного вызова.

А теперь вот аналогичная C#-реализация.

> Let's go through this code:

> * The use of "open" at the top allows us to write "WebRequest" rather than "System.Net.WebRequest". It is similar to a "`using System.Net`" header in C#.
> * Next, we define the `fetchUrl` function, which takes two arguments, a callback to process the stream, and the url to fetch.
> * We next wrap the url string in a Uri. F# has strict type-checking, so if instead we had written:
`let req = WebRequest.Create(url)`
the compiler would have complained that it didn't know which version of `WebRequest.Create` to use.
> * When declaring the `response`, `stream` and `reader` values, the "`use`" keyword is used instead of "`let`". This can only be used in conjunction with classes that implement `IDisposable`.
  It tells the compiler to automatically dispose of the resource when it goes out of scope. This is equivalent to the C# "`using`" keyword.
> * The last line calls the callback function with the StreamReader and url as parameters.  Note that the type of the callback does not have to be specified anywhere.

> Now here is the equivalent C# implementation.

```csharp
class WebPageDownloader
{
    public TResult FetchUrl<TResult>(
        string url,
        Func<string, StreamReader, TResult> callback)
    {
        var req = WebRequest.Create(url);
        using (var resp = req.GetResponse())
        {
            using (var stream = resp.GetResponseStream())
            {
                using (var reader = new StreamReader(stream))
                {
                    return callback(url, reader);
                }
            }
        }
    }
}
```

Как обычно, C#-версия более "зашумлена".

* Здесь десять строк занимают лишь фигурные скобки, а визуальную сложность добавляют 5 уровней вложенности.
* Типы всех параметров необходимо указывать явно, и обобщённый тип `TResult` приходится повторно указывать три раза.

<sub>* Это верно, что в данном конкретном примере, когда все конструкции `using` являются смежными, [лишние фигурные скобки и отступы могут быть убраны](https://stackoverflow.com/questions/1329739/nested-using-statements-in-c-sharp),
но в более общем случае они необходимы.</sub>

> As usual, the C# version has more 'noise'.

> * There are ten lines just for curly braces, and there is the visual complexity of 5 levels of nesting*
> * All the parameter types have to be explicitly declared, and the generic `TResult` type has to be repeated three times.

> <sub>* It's true that in this particular example, when all the `using` statements are adjacent, the [extra braces and indenting can be removed](https://stackoverflow.com/questions/1329739/nested-using-statements-in-c-sharp),
> but in the more general case they are needed.</sub>

## Тестирование кода (Testing the code)

Возвращаясь обратно в мир F#, мы теперь можем протестировать этот код в интерактивном режиме:

> Back in F# land, we can now test the code interactively:

```fsharp
let myCallback (reader : IO.StreamReader) url =
    let html = reader.ReadToEnd()
    let html1000 = html.Substring(0,1000)
    printfn "Downloaded %s. First 1000 is %s" url html1000
    html      // return all the html

// проверка
let google = fetchUrl myCallback "http://google.com"
```

Наконец, мы вынуждены прибегнуть к явному указанию типа для параметра "reader" (`reader : IO.StreamReader`). Это необходимо ввиду того, что компилятор F# не может определить тип параметра "reader" автоматически.

Очень полезной/удобной особенностью F# является возможность "встроить" параметры в функцию, чтобы их не приходилось передавать в неё каждый раз. Вот почему параметр `url` был помещён последним, а не первым, как в C#-версии.
Функция обратного вызова может быть настроена один раз, тогда как URL меняется от вызова к вызову.

> Finally, we have to resort to a type declaration for the reader parameter (`reader:IO.StreamReader`). This is required because the F# compiler cannot determine the type of the "reader" parameter automatically.

> A very useful feature of F# is that you can "bake in" parameters in a function so that they don't have to be passed in every time. This is why the `url` parameter was placed *last* rather than first, as in the C# version.
> The callback can be setup once, while the url varies from call to call.

```fsharp
// создание функции со "встроенной в неё" функцией обратного вызова
let fetchUrl2 = fetchUrl myCallback

// проверка
let google = fetchUrl2 "http://www.google.com"
let bbc    = fetchUrl2 "http://news.bbc.co.uk"

// проверка на списке сайтов
let sites = ["http://www.bing.com";
             "http://www.google.com";
             "http://www.yahoo.com"]

// обработка каждого из сайтов в списке
sites |> List.map fetchUrl2
```

Последняя строка (в которой используется `List.map`) демонстрирует простоту использования новой функции в сочетании с функциями обработки списков для загрузки всего списка сразу.

Ниже приведён аналогичный тестовый код на C#:

> The last line (using `List.map`) shows how the new function can be easily used in conjunction with list processing functions to download a whole list at once.

> Here is the equivalent C# test code:

```csharp
[Test]
public void TestFetchUrlWithCallback()
{
    Func<string, StreamReader, string> myCallback = (url, reader) =>
    {
        var html = reader.ReadToEnd();
        var html1000 = html.Substring(0, 1000);
        Console.WriteLine(
            "Downloaded {0}. First 1000 is {1}", url,
            html1000);
        return html;
    };

    var downloader = new WebPageDownloader();
    var google = downloader.FetchUrl("http://www.google.com",
                                      myCallback);

    // проверка на списке сайтов
    var sites = new List<string> {
        "http://www.bing.com",
        "http://www.google.com",
        "http://www.yahoo.com"};

    // обработка каждого из сайтов в списке
    sites.ForEach(site => downloader.FetchUrl(site, myCallback));
}
```

Опять же, этот код несколько более "зашумлён", чем вышеприведённый F#-код, в нём много явных указаний типов. Что более важно, в C#-коде нельзя просто встроить некоторые из параметров в функцию, так что функция обратного вызова должна каждый раз вызываться явно.

> Again, the code is a bit noisier than the F# code, with many explicit type references. More importantly, the C# code doesn't easily allow you to bake in some of the parameters in a function, so the callback must be explicitly referenced every time.
