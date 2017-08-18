+++
weight = 2

# Publication type.
publication_types = ["7"]

# Publication name and optional abbreviated version.
date = "2017-01-28"
title = "Глава 2. Синтаксический анализатор числа с плавающей точкой"
publication = "Учебник библиотеки FParsec"

# Does this page contain LaTeX math? (true/false)
math = false

# Does this page require source code highlighting? (true/false)
highlight = true

# Is this a selected publication? (true/false)
selected = false

# Links (optional)
url_source = "http://www.quanttec.com/fparsec/tutorial.html#parsing-a-single-float"
+++

Синтаксический разбор входного потока включает два этапа:
  1. построение синтаксического анализатора и
  1. применение анализатора к входному потоку.

Давайте начнем с простого примера: синтаксический разбор строки представляющей собой отдельное число с плавающей точкой.
В этом случае первый шаг &mdash; построение синтаксического анализатора, просто, поскольку модуль [`CharParsers`](http://www.quanttec.com/fparsec/reference/charparsers.html) уже поставляется со встроенным анализатором числа с плавающей точкой:

```fsharp
val pfloat: Parser<float,'u>
```

Универсальный тип `Parser<'Result,'UserState>` является типом всех синтаксических анализаторов в библиотеке FParsec. Если вы перейдете по следующей гиперссылке, вы увидите, что [`Parser`](http://www.quanttec.com/fparsec/reference/primitives.html#members.Parser) является [синонимом](https://msdn.microsoft.com/ru-ru/library/dd233246.aspx) для функционального типа. Однако, на данный момент мы не должны вдаваться в подробности типа `Parser`. Достаточно отметить, что первый аргумент типа представляет тип результата синтаксического анализа. Итак, в рассматриваемом случае тип функции [`pfloat`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.pfloat) говорит нам, что, если синтаксический анализ успешно завершен, функция возвращает число с плавающей точкой типа `float`. Мы не будем использовать пользовательское состояние синтаксического анализатора (`UserState`) в этом уроке, так что, на данный момент, вы можете просто игнорировать второй аргумент типа.

Чтобы применить синтаксический анализатор `pfloat` к строке, мы можем использовать функцию [`run`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.run) из модуля `CharParsers`:

```fsharp
val run: Parser<'Result, unit> -> string -> ParserResult<'Result,unit>
```

Функция `run`, простейшая из [ряда] (http://www.quanttec.com/fparsec/reference/charparsers.html#interface.runparser-functions) функций модуля `CharParsers`, используется для запуска синтаксических анализаторов по входным данным. Другие функции позволяют вам, например, запускать синтаксические анализаторы по содержимому файла или потоку ввода-вывода (`System.IO.Stream`).

Функция `run` применяет синтаксический анализатор, переданный в качестве первого аргумента к строке переданной в качестве второго аргумента и возвращает возвращенное синтаксическим анализатором значение в виде `ParserResult`. Тип [`ParserResult`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.ParserResult) является типом размеченного объединения с двумя вариантами: `Success` и `Failure`. В случае, если синтаксический анализатор успешно выполнен, `ParserResult` содержит результирующее значение, в противном случае он содержит сообщение об ошибке.

Для упрощения тестирования мы напишем маленькую вспомогательную функцию, которая выводит на печать значение результата или сообщение об ошибке:

```fsharp
let test p str =
    match run p str with
    | Success(result, _, _)   -> printfn "Success: %A" result
    | Failure(errorMsg, _, _) -> printfn "Failure: %s" errorMsg
```

Используя вспомогательную функцию, мы можем протестировать `pfloat` выполнив:

```fsharp
test pfloat "1.25"
```

что дает в результате:

```
Success: 1.25
```

Тестирование `pfloat` с числовым литералом, который имеет недопустимый знак экспоненты

```fsharp
test pfloat "1.25E 3"
```

дает сообщение об ошибке

```
Failure: Error in Ln: 1 Col: 6
1.25E 3
     ^
Expecting: decimal digit
```
