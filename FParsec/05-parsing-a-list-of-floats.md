# Разбор списка чисел с плавающей точкой

Мы уже в течение трех глав обсуждаем как сделать синтаксический разбор одного числа с плавающей запятой, так что пришло время попробовать что-то более амбициозное: синтаксический разбор списка чисел с плавающей точкой.

Давайте вначале предположим, что нам нужно, делать синтаксический разбор последовательности чисел с плавающей точкой в скобках, т.е. текст в формате [расширенной формы Бэкуса-Наура](https://ru.wikipedia.org/wiki/Расширенная_форма_Бэкуса_—_Наура) (далее РБНФ): `("[" float "]")*`. Допустимыми входными строками в этом формате будут, например, такие: `""`, `"[1.0]"`, `"[2][3][4]"`.

Поскольку у нас уже есть синтаксический анализатор для числа с плавающей точкой в скобках, нам нужен только способ многократно применять этот синтаксический анализатор для разбора последовательности. Именно для этого предназначен комбинатор [`many`](http://www.quanttec.com/fparsec/reference/primitives.html#members.many):
```fsharp
val many: Parser<'a,'u> -> Parser<'a list,'u>
```

Синтаксический анализатор `many p` повторно применяет синтаксический анализатор `p` до тех пор пока `p` не потерпит неудачу, другими словами он "жадно" разбирает максимально возможное количество вхождений `p`. Результаты `p` возвращаются в виде списка в порядке появления.

Несколько простых тестов показывают, что `many floatInBrackets` работает, как ожидалось:
```fsharp
> test (many floatBetweenBrackets) "";;
Success: []

> test (many floatBetweenBrackets) "[1.0]";;
Success: [1.0]

> test (many floatBetweenBrackets) "[2][3][4]";;
Success: [2.0; 3.0; 4.0]
```

Если в результате *потребления входных данных* `floatBetweenBrackets` вызывает исключение, то комбинированный синтаксический анализатор также вызывает исключение:

```fsharp
> test (many floatBetweenBrackets) "[1][2.0E]";;

Failure: Error in Ln: 1 Col: 9
[1][2.0E]
        ^
Expecting: decimal digit
```

Обратите внимание, что `many` также успешно исполняется для пустой последовательности. Если вы хотите потребовать наличие хотя-бы одного элемента, вы можете использовать для этого [`many1`](http://www.quanttec.com/fparsec/reference/primitives.html#members.many1):
```fsharp
> test (many1 floatBetweenBrackets) "(1)";;
Failure: Error in Ln: 1 Col: 1
(1)
^
Expecting: '['
```

**_Примечание_**
> Если вы предпочитаете, чтобы последнее сообщение об ошибке было сформулировано в терминах высокоуровневого синтаксического анализатора `floatBetweenBrackets` вместо низкоуровневого синтаксического анализатора `str "["`, вы можете использовать оператор `<?>`, как в следующем примере:
>```fsharp
>> test (many1 (floatBetweenBrackets <?> "float between brackets")) "(1)";;
>Failure: Error in Ln: 1 Col: 1
>(1)
>^
>Expecting: float between brackets
>```
> Пожалуйста, загляните в [главу 5.8](http://www.quanttec.com/fparsec/users-guide/customizing-error-messages.html) руководства пользователя, чтобы узнать больше о настройке сообщений об ошибках.

Если вы хотите пропустить последовательность не получая список результатов, то вы можете использовать оптимизированные комбинаторы [`skipMany`](http://www.quanttec.com/fparsec/reference/primitives.html#members.skipMany) и [`skipMany1`](http://www.quanttec.com/fparsec/reference/primitives.html#members.skipMany1) вместо `many` и `many1` .

Другим часто используемым комбинатором для синтаксического разбора последовательностей является [`sepBy`](http://www.quanttec.com/fparsec/reference/primitives.html#members.sepBy):

```fsharp
val sepBy: Parser<'a,'u> -> Parser<'b,'u> -> Parser<'a list, 'u>
```

`sepBy` принимает два параметра синтаксический анализатор «элемент» и «разделитель» и возвращает синтаксический анализатор для списка элементов. В нотации РНБН `sepBy p pSep` может быть записана как `(p (pSep p)*)?`. Подобно `many`, существует [несколько вариантов](http://www.quanttec.com/fparsec/reference/primitives.html#interface.sepBy-parsers)<sup>en</sup> `sepBy`.

С помощью `sepBy` мы можем сделать синтаксический разбор более реального формата списка чисел, где числа с плавающей точкой разделены запятой. Запись в формате РНБН:

```EBNF
floatList: "[" (float ("," float)*)? "]"
```

Допустимыми строками являются, например: `"[]"`, `"[1.0]"`, `"[2,3,4]"`.

Дословной реализацией будет следующий синтаксический анализатор:

```fsharp
let floatList = str "[" >>. sepBy pfloat (str ",") .>> str "]"
```

Тестирование `floatList` с корректными строками дает ожидаемый результат:
```fsharp
> test floatList "[]";;
Success: []
> test floatList "[1.0]";;
Success: [1.0]
> test floatList "[4,5,6]";;
Success: [4.0; 5.0; 6.0]
```

Тестирование с не корректными строками показывает, что `floatList` создает полезные сообщения об ошибках:
```fsharp
> test floatList "[1.0,]";;
Failure: Error in Ln: 1 Col: 6
[1.0,]
     ^
Expecting: floating-point number

> test floatList "[1.0,2.0";;
Failure: Error in Ln: 1 Col: 9
[1.0,2.0
        ^
Note: The error occurred at the end of the input stream.
Expecting: ',' or ']'
```