---
layout: post
title: "Сравние F# с C#: Простая сумма"
description: "Где пробуем сложить квадраты чисел от 1 до N без использования цикла"
nav: why-use-fsharp
seriesId: "Зачем использовать F#?"
seriesOrder: 3
categories: [F# vs C#]
---


Для того, чтобы увидеть, как выглядит реальный F#-код, давайте начнём с простой задачи: суммирование квадратов чисел от 1 до N.

Далее мы сравним реализацию на F# с реализацией на C#. Для начала, вот код на F#:

```fsharp
// объявление функции возведения в квадрат
let square x = x * x

// объявление функции суммирования квадратов
let sumOfSquares n =
   [1 .. n] |> List.map square |> List.sum

// пробуем; вызов функции
sumOfSquares 100
```

Таинственное сочетание символов `|>` называется *оператором-конвейером* (the *pipe operator*). Он просто передаёт выходное значение одного выражение на вход другого. Так что код для функции `sumOfSquares` читается следующим образом:

1. Создать список от 1 до n (список конструируется с помощью квадратных скобок).
2. Передать созданный список в библиотечную функцию под названием `List.map`, таким образом преобразуя входной список в выходной с использованием функции "square", объявленной ранее.
3. Передать результирующий список квадратов в библиотечную функцию `List.sum`. Доагадаетесь, что она делает?
4. Явная инструкция "return" не требуется. Выходное значение `List.sum` и есть окончательный результат функции.

Далее, вот реализация на C# в классическом (не функциональном) стиле C-подобного языка. (Более функциональная версия, использующая LINQ, будет приведена позднее.)

```csharp
public static class SumOfSquaresHelper
{
   public static int Square(int i)
   {
      return i * i;
   }

   public static int SumOfSquares(int n)
   {
      int sum = 0;
      for (int i = 1; i <= n; i++)
      {
         sum += Square(i);
      }
      return sum;
   }
}
```

В чём отличия?

* Код на F# более компактный.
* Код на F# не содержит ни одного объявления типа.
* Вести разработку на F# можно в интерактивном режиме) <!--- Добавить примечание о наличии на данный момент C# Interactive -->

Давайте рассмотрим каждый из этих пунктов по очереди.


### Меньше кода

Наиболее очевидное различие -- в C# варианте гораздо больше кода. 13 строкам кода на C# противопоставляется 3 строки F#-кода (без учёта комментариев). C#-код гораздо более "зашумлён" такими вещами, как фигурные скобки, точки с запятой и т.д. И в C# функции не могут быть объявлены отдельно -- необходимо объявлять их в каком-нибудь классе (например, "SumOfSquaresHelper"). В F# же функции могут быть самостоятельными, а вместо скобок в них используются пробелы; также в F# не нужны символы-ограничители строки.

В F# является общепринятым описывать функцию в одну строку, например как функцию "square". Функция "sumOfSquares" также может быть описана в пределах одной строки. В C# же подобное обычно считается плохой практикой.

Когда функция всё-таки знаимает несколько строк, в F# используют отступы для выделения блоков кода, что убирает необходимость в фигурных скобках (если вы когда-либо имели дело c Python, то должны быть знакомы с таким подходом). Так что функция `sumOfSquares` может быть также описана следующим образом:

> The most obvious difference is that there is a lot more C# code. 13 C# lines compared with 3 F# lines (ignoring comments). The C# code has lots of "noise", things like curly braces, semicolons, etc. And in C# the functions cannot stand alone, but need to be added to some class ("SumOfSquaresHelper"). F# uses whitespace instead of parentheses, needs no line terminator, and the functions can stand alone.

> In F# it is common for entire functions to be written on one line, as the "square" function is. The `sumOfSquares` function could also have been written on one line. In C# this is normally frowned upon as bad practice.

> When a function does have multiple lines, F# uses indentation to indicate a block of code, which eliminates the need for braces. (If you have ever used Python, this is the same idea). So the `sumOfSquares` function could also have been written this way:

```fsharp
let sumOfSquares n =
   [1 .. n]
   |> List.map square
   |> List.sum
```

Единственный недостаток здесь - необходимость быть внимательным с отступами в коде. Лично я считаю, что оно того стоит / это не проблема.
> The only drawback is that you have to indent your code carefully. Personally, I think it is worth the trade-off.


### Отсутствие необходимости в указании типов

Следующее отличие заключается в необходимости явно указывать все используемые типы в C#-коде. Например, параметр `int i` и тип возвращаемого значения в `int SumOfSquares`.
Да, C# позволяет использовать ключевое слово "`var`" во многих местах, но не в случае параметров и типов возвращаемых значений функций.

В F#-коде типы не объявляются вообще. Это важный важный момент: F# выглядит, как нетипизированный язык, но на самом деле он не менее типобезоапасный, чем C#, а, фактически, даже более!
В F# используется механизм под названием "вывод типов" для определения используемых типов из контекста. Этот механизм работает удивительно отлично в большинстве случаев и колоссально уменьшает степень сложности кода.

В этом случае, алгоритм вывода типов определяет, что объявлен список целых чисел. Затем, в свою очередь, предполгаает, что функция возведения в квадрат и функция суммы также должны оперировать целыми числами, а также и то, что конечное значение должно быть целочисленным. Вы можете видеть, какие типы выводятся, посмотря на запись о результате компиляции в интерактивном окне. Вы увидите нечто подобное:

> The next difference is that the C# code has to explicitly declare all the types used. For example, the `int i` parameter and `int SumOfSquares` return type.
Yes, C# does allow you to use the "var" keyword in many places, but not for parameters and return types of functions.

> In the F# code we didn't declare any types at all. This is an important point: F# looks like an untyped language, but it is actually just as type-safe as C#, in fact, even more so!
F# uses a technique called "type inference" to infer the types you are using from their context. It works amazingly very well most of the time, and reduces the code complexity immensely.

> In this case, the type inference algorithm notes that we started with a list of integers. That in turn implies that the square function and the sum function must be taking ints as well, and that the final value must be an int. You can see what the inferred types are by looking at the result of the compilation in the interactive window. You'll see something like:

```fsharp
val square : int -> int
```

что означает, что фунция "square" принимает целое число (`int`) и возвращает также целое число (`int`).

Если исходный список использовал бы числа с плавающей точкой, система вывода типов вывела бы, что функция возведения в квадрат также использует числа с плавающей точкой. Попробуйте сами и убедитесь:

> which means that the "square" function takes an int and returns an int.

> If the original list had used floats instead, the type inference system would have deduced that the square function used floats instead. Try it and see:

```fsharp
// объявления функции возведения в квадрат
let squareF x = x * x

// объявление функции суммы квадратов
let sumOfSquaresF n =
   [1.0 .. n] |> List.map squareF |> List.sum  // "1.0" -- число с плавающей точкой (float)

sumOfSquaresF 100.0
```

Проверка типов очень строга! Если вы попытаетесь использовать список чисел с плавающей точкой (`float`) - `[1.0..n]` в исходном примере `sumOfSquares` или же список целых чисел (`int`) в примере `sumOfSquaresF`, компилятор сообщит об ошибке несоответствия типов.

> The type checking is very strict! If you try using a list of floats (`[1.0..n]`) in the original `sumOfSquares` example, or a list of ints (`[1 ..n]`) in the `sumOfSquaresF` example, you will get a type error from the compiler.

### Интерактивная разработка (Interactive development)

В конечном счёте, для F# существует интерактивное окно, где вы можете сразу же проверить работу кода и поэкспериментировать с ним. В C# же нету простого способа решения этих задач. <!--- Примечание: не актуально, наверное, т.к. есть уже C# Interactive. Так что не помешает подкорректировать статью -->

Например, можно описать свою функцию возведения в квадрат и сразу же протестировать её:

> Finally, F# has an interactive window where you can test the code immediately and play around with it. In C# there is no easy way to do this.

> For example, I can write my square function and immediately test it:

```fsharp
// определение/задание функции возведения в квадрат
let square x = x * x

// тестирование
let s2 = square 2
let s3 = square 3
let s4 = square 4
```

Когда я убедился, что она работает, я могу продвинуться к следующей части кода.

Такой вид интерактивности подталкивает к инкрементальному кодированию, которое впоследствии может стать привычкой!

Более того, многие утверждают, что работа с кодом в интерактивном режиме прививает хорошие подходы к проектированию, такие как уменьшение связности и явное указание зависимостей, и, следовательно, код, пригодный для интерактивного исполнения, будет также легко тестируемым. И наоборот, код, работу которого нельзя проверить в интерактивном режиме, вероятно, также будет сложно и протестировать.

> When I am satisfied that it works, I can move on to the next bit of code.

> This kind of interactivity encourages an incremental approach to coding that can become addictive!

> Furthermore, many people claim that designing code interactively enforces good design practices such as decoupling and explicit dependencies,
and therefore, code that is suitable for interactive evaluation will also be code that is easy to test. Conversely, code that cannot be
tested interactively will probably be hard to test as well.

### Возвращение к C#-коду (The C# code revisited)

Мой первоначальный пример, который был написан, используя C# "старого образца". В C# было добавлено много функциональных возможностей (возможностей функционального программирования), и сейчас возможно переписать этот пример в более компактном виде, используя LINQ.

Поэтому вот очередная C#-версия, повторяющая F#-код "строка-в-строку".

> My original example was written using "old-style" C#.  C# has incorporated a lot of functional features, and it is possible to rewrite the example in a more compact way using the LINQ extensions.

> So here is another C# version -- a line-for-line translation of the F# code.

```csharp
public static class FunctionalSumOfSquaresHelper
{
   public static int SumOfSquares(int n)
   {
      return Enumerable.Range(1, n)
         .Select(i => i * i)
         .Sum();
   }
}
```

Как бы то ни было, вдобавок к назойливым фигурнам скобкам, точкам и точкам с запятой в C#-версии необходимо указывать типы параметров и возвращаемые типы, в отличии от F#-версии.

Многие C#-разработчики могут посчитать этот пример тривиальным, но затем снова вернуться к циклам, когда логика станет более сложной. В F# же вы почти никогда не увидите циклы явно.
Для примера посмотрите [статью про избавление от шаблонного кода сложных циклов](http://fsharpforfunandprofit.com/posts/conciseness-extracting-boilerplate/).

> However, in addition to the noise of the curly braces and periods and semicolons, the C# version needs to declare the parameter and return types, unlike the F# version.

> Many C# developers may find this a trivial example, but still resort back to loops when the logic becomes more complicated. In F# though, you will almost never see explicit loops like this.
> See for example, [this post on eliminating boilerplate from more complicated loops](http://fsharpforfunandprofit.com/posts/conciseness-extracting-boilerplate/).
