# Разбор единственного числа с плавающей точкой

Парсинг с помощью `FParsec` состоит из двух шагов:

1. Построение парсера
2. Применение парсера к входным данным

Давайте начнем с простого примера - разбор единственного числа с плавающей точкой из строки.
В этом случае первый шаг будет тривиальным, так как модуль [`CharParsers`](http://www.quanttec.com/fparsec/reference/charparsers.html) уже включает парсер чисел с плавающей точкой:

```fsharp
val pfloat : Parser<float,'u>
```

Обобщенный тип [`Parser<'Result,'UserState>`](http://www.quanttec.com/fparsec/reference/primitives.html#members.Parser) это тип всех парсеров из `FParsec`. 
Если вы перейдете по ссылке к справочнику, то вы увидите, что `Parser` это псевдоним для типа-функции. 
Однако сейчас нам не нужно вдаваться в детали типа `Parser`. Достаточно отметить, что первый аргумент типа представляет тип результата парсинга. 
Таким образом, в случае с [`pfloat`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.pfloat), тип говорит нам, что если разбор будет успешным, то парсер вернет число с плавающей точкой типа `float`. 
Мы не будем использовать `UserState` в этом туториале, поэтому поначалу вы можете просто игнорировать второй аргумент.

Для того, чтобы применить парсер `pfloat` к строке мы можем использовать функцию [`run`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.run) из модуля `Charparsers`:

```fsharp
val run: Parser<'Result, unit> -> string -> ParserResult<'Result,unit>
```

`run` - простейшая из [нескольких](http://www.quanttec.com/fparsec/reference/charparsers.html#interface.runparser-functions) функций, предоставляемых модулем `CharParsers` для запуска парсинга ко входным данным.
Другие функций позволяют вам, например, запустить парсер непосредственно к содержимому файла или для `System.IO.Stream`.

`run` применяет парсер, переданный в качестве первого аргумента функции к строке, передаваемой вторым аргументом и возвращает результат парсинга в виде `ParseResult`. 
Тип [`ParserResult`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.ParserResult) это размеченное объединение с двумя вариантами - `Success и Failure` (успех и неудача соответственно). 
В случае удачного разбора, `ParseResult` содержит результирующее значение, в противном случае он содержит сообщение об ошибке.

Для упрощения тестирования мы напишем небольшую вспомогательную функцию, которая выводит значение или сообщение об ошибке:

```fsharp
let test p str =
    match run p str with
    | Success(result, _, _)   -> printfn "Success: %A" result
    | Failure(errorMsg, _, _) -> printfn "Failure: %s" errorMsg
```

Теперь мы можем протестировать `pfloat` выполнив

```fsharp
test pfloat "1.25"``
```

что даст следующий результат 

```fsharp
Success: 1.25
```

Тестирование `pfloat` с числом, имеющим некорректную экспоненту

```fsharp
test pfloat "1.25E 3"
```

выводит сообщение об ошибке

```fsharp
Failure: Error in Ln: 1 Col: 6
1.25E 3
     ^
Expecting: decimal digit
```