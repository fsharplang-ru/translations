---
layout: page
title: "Зачем использовать F#?"
description: "Почему стоит попробовать F# в Вашем следующем проекте"
nav: why-use-fsharp
hasIcons: 1
image: "/assets/img/four-concepts2.png"
---

Хотя F# хорош для использования в специальных областях, таких как научные исследования или анализ даных, он также отлично подходит для разработки корпоративных приложений.
 _Although F# is great for specialist areas such as scientific or data analysis, it is also an excellent choice for enterprise development._
Ниже перечислены пять веских причин рассмотреть использование F# для Вашего следующего проекта.
 _Here are five good reasons why you should consider using F# for  your next project._

## ![](../assets/img/glyphicons/glyphicons_030_pencil.png) Краткость (Conciseness)

F# не загружен [лишними конструкциями в коде](../posts/fvsc-sum-of-squares.md), такими как фигурные скобки, точки с запятыми и т.п.
 _F# is not cluttered up with [coding "noise"](../posts/fvsc-sum-of-squares.md) such as curly brackets, semicolons and so on._

Вам очень редко придётся явно указывать тип объекта, благодаря мощной [системе вывода типов](../posts/conciseness-type-inference.md).
 _You almost never have to specify the type of an object, thanks to a powerful [type inference system](../posts/conciseness-type-inference.md)._

И, по сравнению с C#, обычно требуется требуется [меньше кода](../posts/fvsc-download.md), чтобы решить одну и ту же задачу.
And, compared with C#, it generally takes [fewer lines of code](../posts/fvsc-download.md) to solve the same problem.


```fsharp
// "одно-строчники" (one-liners)
[1..100] |> List.sum |> printfn "sum=%d"

// никаких точек с запятой, фигурных или круглых скобок (no curly braces, semicolons or parentheses)
let square x = x * x
let sq = square 42

// простые типы -- в одну строку (simple types in one line)
type Person = {First:string; Last:string}

// сложные типы -- лишь в несколько строк (complex types in a few lines)
type Employee =
  | Worker of Person
  | Manager of Employee list

// вывод типов (type inference)
let jdoe = {First="John";Last="Doe"}
let worker = Worker jdoe
```

## ![](../assets/img/glyphicons/glyphicons_343_thumbs_up.png) Удобство (Convenience)

Многие общие задачи программирования решаются гораздо проще с использованием F#. Среди них такие, как создание и использование [сложных объявлений типов](../posts/conciseness-type-definitions.md), [обработка списков](../posts/conciseness-extracting-boilerplate.md), [сравнение и равенство](../posts/convenience-types.md), [конечные автоматы/машины состояний](../posts/designing-with-types-representing-states.md) и многое другое.
Many common programming tasks are much simpler in F#.  This includes things like creating and using
 [complex type definitions](../posts/conciseness-type-definitions.md), doing [list processing](../posts/conciseness-extracting-boilerplate.md),
 [comparison and equality](../posts/convenience-types.md), [state machines](../posts/designing-with-types-representing-states.md), and much more.

А т.к. функции в F# -- объекты первого класса, становится очень просто проектировать мощный и переиспользуемый код, создавая [функции, параметрами которых являются другие функции](../posts/conciseness-extracting-boilerplate.md), или же [комбинируя существующие функции](../posts/conciseness-functions-as-building-blocks.md) для получения новой функциональности.
And because functions are first class objects, it is very easy to create powerful and reusable code by creating functions
that have [other functions as parameters](../posts/conciseness-extracting-boilerplate.md),
or that [combine existing functions](../posts/conciseness-functions-as-building-blocks.md) to create new functionality.

```fsharp
// автоматически реализуемые эквивалентность и сравнение (automatic equality and comparison)
type Person = {First:string; Last:string}
let person1 = {First="john"; Last="Doe"}
let person2 = {First="john"; Last="Doe"}
printfn "Equal? %A"  (person1 = person2)

// простая работа с IDisposable с использованием ключевого слова "use" (easy IDisposable logic with "use" keyword)
use reader = new StreamReader(..)

// простая композиция функций (easy composition of functions)
let add2times3 = (+) 2 >> (*) 3
let result = add2times3 5
```

## ![](../assets/img/glyphicons/glyphicons_150_check.png) Правильность/точность/безошибочность (Correctness)

F# имеет [мощную систему типов](../posts/correctness-type-checking.md), которая препятствует возникновению многих распространённых шибок, таких как [null-исключения](../posts/the-option-type.md#option-is-not-null).
F# has a [powerful type system](../posts/correctness-type-checking.md) which prevents many common errors such
as [null reference exceptions](../posts/the-option-type.md#option-is-not-null).

Значения [по умолчанию неизменяемы](../posts/correctness-immutability.md), что предупреждает большое множество ошибок.
Values are [immutable by default](../posts/correctness-immutability.md), which prevents a large class of errors.

В дополнение к вышеописанному, Вы часто можете описать бизнес-логику, используя [систему типов](../posts/correctness-exhaustive-pattern-matching.md) таким образом, чтобы было буквально [невозможно написать ошибочный/некорректный код](../posts/designing-for-correctness.md), а то и по возможности использовать механизм [единиц измерений](../posts/units-of-measure.md), таким образом заметно уменьшив необходимость в модульных тестах (юнит-тестах).
In addition, you can often encode business logic using the [type system](../posts/correctness-exhaustive-pattern-matching.md) itself in such a way
that it is actually [impossible to write incorrect code](../posts/designing-for-correctness.md)
or mix up [units of measure](../posts/units-of-measure.md), greatly reducing the need for unit tests.


```fsharp
// строгая провека типов (strict type checking)
printfn "print string %s" 123 //compile error

// все значения неизменяемы по умолчнанию (all values immutable by default)
person1.First <- "new name"  //ошибка присвоения (assignment error)

// нет необходимости делать проверку на null (never have to check for nulls)
let makeNewString str =
   // всегда можно без опасений дополнить str (str can always be appended to safely)
   let newString = str + " new!"
   newString

// описывайте бизнес-логику в типах/представляйте бизнес-логику в типах (embed business logic into types)
emptyShoppingCart.remove   // compile error!

// механизм единиц измерений (units of measure)
let distance = 10<m> + 10<ft> // error!
```

## ![](../assets/img/glyphicons/glyphicons_054_clock.png) Параллелизм (Concurrency)

Для F# существует ряд встроенных библиотек для решения задач, в которых необходимо реализовать одновременное выполнение нескольких действий.
F# has a number of built-in libraries to help when more than one thing at a time is happening.

[И асинхронность, и параллелизм](../posts/concurrency-async-and-parallel.md) реализовать очень просто/очень просты в реализации.
Asynchronous programming is [very easy](../posts/concurrency-async-and-parallel.md), as is parallelism.

F# также имеет встроенную [модель актёров/акторов](../posts/concurrency-actor-model.md), отличную поддержку обработки событий и и [функционального реактивного программирования](../posts/concurrency-reactive.md).
F# also has a built-in [actor model](../posts/concurrency-actor-model.md), and excellent support for event handling
and [functional reactive programming](../posts/concurrency-reactive.md).

И, конечно же, благодаря неизменямым по умолчанию структурам данных, разделять состояне и избегать блокировок гораздо проще.
And of course, because data structures are immutable by default, sharing state and avoiding locks is much easier.

```fsharp
// простая асинхронная логика с использованием ключевого слова/конструкции "async" (easy async logic with "async" keyword)
let! result = async {something}

// простая параллельная обработка (easy parallelism)
Async.Parallel [ for i in 0..40 ->
      async { return fib(i) } ]

// очереди сообщений (message queues)
MailboxProcessor.Start(fun inbox-> async{
    let! msg = inbox.Receive()
    printfn "message is: %s" msg
    })
```

## ![](../assets/img/glyphicons/glyphicons_280_settings.png) Полнота (Completeness)

Хотя F#, по сути, и функциональный язык, он поддерживает и другие _стили/парадигмы_, которые не чисты на 100%, что обеспечивает гораздо более простое взаимодействие с "нечистым" миром веб-сайтов, баз данных, сторонних/других приложений и т.п.
Although it is a functional language at heart, F# does support other styles which are not 100% pure,
which makes it much easier to interact with the non-pure world of web sites, databases, other applications, and so on.

В частности, F# разрабатывался как гибридный функциональный/объектно-ориентированный язык, в результате чего он позволяет делать [практически всё то же самое, что и C#](../posts/completeness-anything-csharp-can-do.md).
In particular, F# is designed as a hybrid functional/OO language, so it can do [virtually everything that C# can do](../posts/completeness-anything-csharp-can-do.md).


И конечно же, F# -- [_часть/представитель_ экосистемы .NET](../posts/completeness-seamless-dotnet-interop.md), что предоставляет Вам беспрепятственный доступ ко всем сторонним .NET-библиотекам и инструментам.
Of course, F# is [part of the .NET ecosystem](../posts/completeness-seamless-dotnet-interop.md), which gives you seamless access to all the third party .NET libraries and tools.
Он работает/запускается на большинстве платформах, включая Linux и мобильные платформы (_с помощью/через_ Mono и новый .NET Core).
It runs on most platforms, including Linux and smart phones (via Mono and the new .NET Core).


В конечном счёте, он хорошо интегрирован с Visual Studio (Windows) и Xamarin (Mac), а значит, Вы можете использовать _отличную/качественную/полноценную_ IDE с поддержкой IntelliSense, отладчиком, а также множеством расширений для модульного тестирования (юнит-тестов), контроля версий и прочих задач процесса разработки.
Finally, it is well integrated with Visual Studio (Windows) and Xamarin (Mac), which means you get a great IDE with IntelliSense support, a debugger,
and many plug-ins for unit tests, source control, and other development tasks.
А в случае Linux вместо вышеперечисленного Вы можете использовать IDE MonoDevelop. Or on Linux, you can use the MonoDevelop IDE instead.

```fsharp
// "нечистый" код, если он нужен (impure code when needed)
let mutable counter = 0

// создавайте C#-совместимые классы и интерфейсы (create C# compatible classes and interfaces)
type IEnumerator<'a> =
    abstract member Current : 'a
    abstract MoveNext : unit -> bool

// методы-расширения (extension methods)
type System.Int32 with
    member this.IsEven = this % 2 = 0

let i=20
if i.IsEven then printfn "'%i' is even" i

// код создания и работы с графическим пользовательским интерфейсом (UI code)
open System.Windows.Forms
let form = new Form(Width= 400, Height = 300,
   Visible = true, Text = "Hello World")
form.TopMost <- true
form.Click.Add (fun args-> printfn "clicked!")
form.Show()
```

## Цикл статей "Зачем использовать F#?" (The "Why Use F#?" series)

Следующая серия статей демонстрирует каждое из вышеперечисленных преимуществ F# с приведением отдельных фрагментов F#-кода (а часто и с фрагментами C#-кода для сравнения).

* [Введение в цикл статей "Зачем использовать F#"](../posts/why-use-fsharp-intro.md). Обзор преимуществ F#.
* [Синтаксис F# за 60 секунд](../posts/fsharp-in-60-seconds.md). Очень краткий обзор о том, как читать F#-код.
* [Сравнение F# и C#: Просто суммирование](../posts/fvsc-sum-of-squares.md). Здесь мы пробуем просуммировать квадраты чисел от 1 до N без использования цикла.
* [Сравнение F# и C#: Сортировка](../posts/fvsc-quicksort.md). Здесь мы увидим, что F# более декларативный, чем C#, а также познакомимся с механизом сопоставления с образцом (pattern matching).
* [Сравнение F# и C#: Загрузка веб-страницы](../posts/fvsc-download.md). Здесь мы увидим, что F# превосходит в работе с обратными вызовами, а также познакомимся с ключевым словом "use".
* [Четрые ключевых понятия](../posts/key-concepts.md). Понятия, отличающие F# от _стандартного/типичного_ императивного языка.
* [Краткость](../posts/conciseness-intro.md). Почему важна краткость?
* [Вывод типов](../posts/conciseness-type-inference.md). Как не дать себя запутать сложным синтаксисом для работы с типами.
* [Малые накладные расходы при объявлении типов](../posts/conciseness-type-definitions.md). Никаких неудобств при создании новых типов.
* [Использование функций во избежание шаблонного (boilerplate) кода](../posts/conciseness-extracting-boilerplate.md). Функциональный подход к принципу DRY _(Don't Repeat Yourself -- не повторяйcя)_.
* [Использование функций как строительных блоков](../posts/conciseness-functions-as-building-blocks.md). Композиция функций и мини-языки делают код более читаемым.
* [Сопоставление с образцом _для/ради_ краткости](../posts/conciseness-pattern-matching.md). Сопоставление с образцом позволяет находить и связывать в одно действие.
* [Удобство](../posts/convenience-intro.md). Возможности, сокращающие рутинное программирование и шаблонный код.
* [Поведение типов "из коробки"](../posts/convenience-types.md). Неизменяемость и встроенная эквивалентность без кодирования.
* [Функции как интерфейсы](../posts/convenience-functions-as-interfaces.md). Шаблоны проектирования ООП могут оказатсья тривиальными, _если использовать функции_/_когда в дело идут функции_.
* [Частичное применение](../posts/convenience-partial-application.md). Как _закрепить_/_зафиксировать_/_"запомнить"_ некоторые параметры функции.
* [Активные _образцы_/_шаблоны_ (Active patterns)](../posts/convenience-active-patterns.md). (???) Динамические образцы для эффективного сопоставления (???). (Dynamic patterns for powerful matching)
* [Правильность/Корректность](../posts/correctness-intro.md). Как писать "модульные тесты времени компиляции".
* [Неизменяемость](../posts/correctness-immutability.md). Делаем код предсказуемым.
* [_Полное_/_исчерпывающее_ сопоставление с образцом](../posts/correctness-exhaustive-pattern-matching.md). _Мощная_/_эффективная_ техника обеспечения _правильности_/_корректности_.
* [Использование системы типов для обеспечения корректности кода](../posts/correctness-type-checking.md). Система типов F# -- ваш друг, а не враг.
* [Рабочий пример: Проектирование ради _корректности_/_правильности_](../posts/designing-for-correctness.md). _Как сделать неправильное непредставимым._ / _Как сделать недопустимое состояние непредставимым._
* [Параллелизм/Параллельная обработка](../posts/concurrency-intro.md). Следующая крупная революция в подходе к проектированию ПО?
* [Асинхронное программирование](../posts/concurrency-async-and-parallel.md). Инкапсулирование фоновой задачи c помощью класса Async.
* [Сообщения и агенты](../posts/concurrency-actor-model.md). Упрощаем _представление_/_размышление о_/_понимание_ параллельности.
* [Функциональное реактивное программирование](../posts/concurrency-reactive.md). Представляем события в виде потоков.
* [Полнота](../posts/completeness-intro.md). F# -- часть целой экосистемы .NET.
* [_Бесшовное_/_беспрепятственное_ взаимодействие с библиотеками .NET](../posts/completeness-seamless-dotnet-interop.md). Несколько удобных _возможностей_/_компонентов_ для работы с .NET-библиотеками.
* [Всё, что может C#...](../posts/completeness-anything-csharp-can-do.md). Ураганный тур по объектно-ориентированному F#-коду.
* [Зачем использоват F#: Заключение](../posts/why-use-fsharp-conclusion.md).

The following series of posts demonstrates each of these F# benefits, using standalone snippets of F# code (and often with C# code for comparison).

* [Introduction to the 'Why use F#' series](../posts/why-use-fsharp-intro.md). An overview of the benefits of F#
* [F# syntax in 60 seconds](../posts/fsharp-in-60-seconds.md). A very quick overview on how to read F# code
* [Comparing F# with C#: A simple sum](../posts/fvsc-sum-of-squares.md). In which we attempt to sum the squares from 1 to N without using a loop
* [Comparing F# with C#: Sorting](../posts/fvsc-quicksort.md). In which we see that F# is more declarative than C#, and we are introduced to pattern matching.
* [Comparing F# with C#: Downloading a web page](../posts/fvsc-download.md). In which we see that F# excels at callbacks, and we are introduced to the 'use' keyword
* [Four Key Concepts](../posts/key-concepts.md). The concepts that differentiate F# from a standard imperative language
* [Conciseness](../posts/conciseness-intro.md). Why is conciseness important?
* [Type inference](../posts/conciseness-type-inference.md). How to avoid getting distracted by complex type syntax
* [Low overhead type definitions](../posts/conciseness-type-definitions.md). No penalty for making new types
* [Using functions to extract boilerplate code](../posts/conciseness-extracting-boilerplate.md). The functional approach to the DRY principle
* [Using functions as building blocks](../posts/conciseness-functions-as-building-blocks.md). Function composition and mini-languages make code more readable
* [Pattern matching for conciseness](../posts/conciseness-pattern-matching.md). Pattern matching can match and bind in a single step
* [Convenience](../posts/convenience-intro.md). Features that reduce programming drudgery and boilerplate code
* [Out-of-the-box behavior for types](../posts/convenience-types.md). Immutability and built-in equality with no coding
* [Functions as interfaces](../posts/convenience-functions-as-interfaces.md). OO design patterns can be trivial when functions are used
* [Partial Application](../posts/convenience-partial-application.md). How to fix some of a function's parameters
* [Active patterns](../posts/convenience-active-patterns.md). Dynamic patterns for powerful matching
* [Correctness](../posts/correctness-intro.md). How to write 'compile time unit tests'
* [Immutability](../posts/correctness-immutability.md). Making your code predictable
* [Exhaustive pattern matching](../posts/correctness-exhaustive-pattern-matching.md). A powerful technique to ensure correctness
* [Using the type system to ensure correct code](../posts/correctness-type-checking.md). In F# the type system is your friend, not your enemy
* [Worked example: Designing for correctness](../posts/designing-for-correctness.md). How to make illegal states unrepresentable
* [Concurrency](../posts/concurrency-intro.md). The next major revolution in how we write software?
* [Asynchronous programming](../posts/concurrency-async-and-parallel.md). Encapsulating a background task with the Async class
* [Messages and Agents](../posts/concurrency-actor-model.md). Making it easier to think about concurrency
* [Functional Reactive Programming](../posts/concurrency-reactive.md). Turning events into streams
* [Completeness](../posts/completeness-intro.md). F# is part of the whole .NET ecosystem
* [Seamless interoperation with .NET libraries](../posts/completeness-seamless-dotnet-interop.md). Some convenient features for working with .NET libraries
* [Anything C# can do...](../posts/completeness-anything-csharp-can-do.md). A whirlwind tour of object-oriented code in F#
* [Why use F#: Conclusion](../posts/why-use-fsharp-conclusion.md).
