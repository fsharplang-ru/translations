+++
weight = 9

# Publication type.
publication_types = ["7"]

# Publication name and optional abbreviated version.
date = "2017-01-28"
title = "Глава 9. Использование альтернативных синтаксических анализов"
publication = "Учебник библиотеки FParsec"

# Does this page contain LaTeX math? (true/false)
math = false

# Does this page require source code highlighting? (true/false)
highlight = true

# Is this a selected publication? (true/false)
selected = false

# Links (optional)
url_source = "http://www.quanttec.com/fparsec/tutorial.html#parsing-alternatives"
+++

В главе 7 "[Синтаксический анализатор строковых данных](../07-parsing-string-data)" мы вкратце представили комбинатор выбора [`<|>`](http://www.quanttec.com/fparsec/reference/primitives.html#members.:60::124::62:)<sup>en</sup> :

```fsharp
val (<|>): Parser<'a,'u> -> Parser<'a,'u> -> Parser<'a,u>
```

Этот комбинатор позволяет вам поддерживать несколько альтернативных вариантов.

Например, в [главе 7](../07-parsing-string-data) мы использовали `<|>` для объединения синтаксического анализатора для неэкранированных символов и анализатора для экранированных символов в новый анализатор, который поддерживает оба варианта: `normalChar <|> escapedChar`.

Другим примером, показывающим, как работает `<|>` является следующий синтаксический анализатор для разбора строкового представления булевых значений:

```fsharp
let boolean = 
  (stringReturn "true"  true) <|>
  (stringReturn "false" false)
```

Здесь мы использовали синтаксический анализатор [`stringReturn`](http://www.quanttec.com/fparsec/reference/charparsers.html#members.stringReturn)<sup>en</sup>, который берет в качестве первого аргумента строку, и, в случае успеха, возвращает значение, указанное в качестве второго аргумента.

Протестируем синтаксический анализатор `boolean` на примерах:

```fsharp
> test boolean "false";;
Success: false
> test boolean "true";;
Success: true
> test boolean "tru";;
Failure: Error in Ln: 1 Col: 1
tru
^
Expecting: 'false' or 'true'
```

Поведение комбинатора `<|>` имеет две важные характеристики:
* `<|>` Правая часть синтаксического анализатора выполняется, если синтаксический анализатор левой части завершился с не фатальной ошибкой и без изменения состояния. Комбинатор не реализует правило самого длинного совпадения.
* Однако, комбинатор пытается использовать правый синтаксический анализатор, если левый синтаксический анализатор при завершении вызывает *нет данных для обработки*.

Следствием второго пункта является то, что следующий тест завершился неудачно, потому что синтаксический анализатор в левой части `<|>` получает пробелы перед сбоем:

```fsharp
> test ((ws >>. str "a") <|> (ws >>. str "b")) " b";;
Failure: Error in Ln: 1 Col: 2
 b
 ^
Expecting: 'a'
```

К счастью, мы можем легко исправить этот синтаксический анализатор, выделив `ws`:

```fsharp
> test (ws >>. (str "a" <|> str "b")) " b";;
Success: "b"
```

Если вам интересно, почему `<|>` ведет себя таким образом и как вы можете обрабатывать ситуации, в которых вам нужно чтобы  `<|>` пытался исполнять альтернативный синтаксический анализатор, даже если первый синтаксический анализатор вызвал ошибку после обработки входных данных смотрите главы [5.6 Parsing alternatives](http://www.quanttec.com/fparsec/users-guide/parsing-alternatives.html) и [5.7 Looking ahead and backtracking](http://www.quanttec.com/fparsec/users-guide/looking-ahead-and-backtracking.html) в руководстве пользователя.

Если вы хотите использовать более двух альтернативных синтаксических анализаторов, вы можете комбинировать несколько операторов `<|>`, например, в `p1 <|> p2 <|> p3 <|> ...`, или вы можете использовать комбинатор [`choice`](http://www.quanttec.com/fparsec/reference/primitives.html#members.choice)<sup>en</sup>, который принимает последовательность синтаксических анализаторов в качестве аргумента, например `choice [ p1 ; p2 ; p3 ; ... ]`.