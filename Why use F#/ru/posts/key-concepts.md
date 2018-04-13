---
layout: post
title: "Четыре ключевых понятия"
description: "Понятия, отличающие F# от стандартного императивного языка"
nav: why-use-fsharp
seriesId: "Зачем использовать F#?"
seriesOrder: 6
categories:
image: "/assets/img/four-concepts2.png"
---

В следующих нескольких статьях мы перейдём к рассмотрению таких тем, как: краткость (conciseness), удобство (convenience), корректность (correctness), параллелизм/конкуррентность (concurrency) и полнота (completeness).

Но прежде давайте рассмотрим некоторые из ключевых понятий, с которыми нам придётся встречаться постоянно. F# во многих аспектах отличается от стандартного императивного языка, такого как C#, но существует несколько значительных различий, которые особенно важно понять:

* **Функционально-ориентированный**, нежели объектно-ориентированный;
* **Выражения**, нежели утверждения/инструкции;
* **Алгебраические типы данных** для описания моделей предметной области;
* **Сопоставление с образцом** для потока управления.

В более поздних статьях все эти понятия будут рассмотрены гораздо глубже. Сейчас же была дана лишь вводная информация, необходимая для понимания остальной части серии статей.

> In the next few posts we'll move on to demonstrating the themes of this series: conciseness, convenience, correctness, concurrency and completeness.

> But before that, let's look at some of the key concepts in F# that we will meet over and over again.  F# is different in many ways from a standard imperative language like C#, but there are a few major differences that are particularly important to understand:

> * **Function-oriented** rather than object-oriented
> * **Expressions** rather than statements
> * **Algebraic types** for creating domain models
> * **Pattern matching** for flow of control

> In later posts, these will be dealt with in much greater depth -- this is just a taster to help you understand the rest of this series.

![four key concepts](../assets/img/four-concepts2.png)

### Функционально-ориентированный, нежели объектно-ориентированный

Как вы могли ожидать от термина "функциональное программирование", в F# функции повсюду.

И конечно же, функции являются сущностями первого класса и могут передаваться как любые другие значения:

> As you might expect from the term "functional programming", functions are everywhere in F#.

> Of course, functions are first class entities, and can be passed around like any other value:

```fsharp
let square x = x * x

// функции как значения
let squareclone = square
let result = [1..10] |> List.map squareclone

// функции, принимающие другие функции в качестве параметров
let execFunction aFunc aParam = aFunc aParam
let result2 = execFunction square 12
```

Однако в C# также есть первоклассные функции, так что же тогда особенного в функциональном программировании?

Короткий ответ следующий: функционально-ориентированная природа F# проникает во все части языка и системы типов, чего не происходит в случае с C#; так что вещи, выглядящие неуклюжими или нелепыми в C#, в F# получаются очень элегантными.

Очень сложно объяснить всё это в пределах нескольких абзацев, но вот несколько достоинств, которые будут продемонстрированы в рамках этой серии статей:

> But C# has first-class functions too, so what's so special about functional programming?

> The short answer is that the function-oriented nature of F# infiltrates every part of the language and type system in a way that it does not in C#, so that things that are awkward or clumsy in C# are very elegant in F#.

> It's hard to explain this in a few paragraphs, but here are some of the benefits that we will see demonstrated over this series of posts:

* **Проектирование с помощью композиции**. Композиция является "клеем", который позволяет строить более крупные системы из малых. Это не какая-то вспомогательная методика, это самое сердце функционального стиля. Почти каждая строка кода представляет собой выражение, пригодное для последующей его композиции с другими выражениями (ниже будет продемонстрировано). Композиция используется для построения базовых функций, затем функций, использующие эти базовые функции, и так далее. И принцип композиции применим не только к функциям, но и к типам (типы-произведения и типы-суммы, которые описаны ниже).
* **Факторинг (factoring) и рефакторинг (refactoring)**. Возможность разбиения задачи на части зависит от того, как просто эти части могут быть снова собраны вместе. Методы и классы, которые могут показаться неделимыми в императивном языке могут быть разбиты на удивительно маленькие части при проектировании в функциональном стиле. Эти мелкие компоненты, как правило, состоят из (а) нескольких очень общих функций, которые принимают другие функции в качестве параметров и (б) прочих вспомогательных функций, специализирующие общий случай под конкретную структура данных или приложение. Как только будет реализовано такое разбиение/декомпозиция, обобщённые функции позволят очень легко запрограммировать большое количество дополнительных операций без необходимости писать новый код. Вы можете ознакомиться хороший пример такой общей функции (функции свёртки - fold) в [статье про извлечение повторяющегося кода из циклов](../posts/conciseness-extracting-boilerplate.md).
* **Правильный подход к проектированию**. Многие принципы хорошего/правильного проектирования, такие как "разделение ответственности" ("separation of concerns"), "принцип единственной ответственности" ("single responsibility principle"), ["программирование относительно интерфейса, а не реализации (program to an interface, not an implementation)"](../posts/convenience-functions-as-interfaces.md), возникают естественным образом в результате следования функциональному подходу. Да и в целом, функциональный код склонен быть высокоуровневым и декларативным.

В последующих статьях этой серии будут приведены примеры того, как функции могут сделать код более кратким и удобным. Ну и также, для более глубокого понимания есть целая серия статей о том, [как мыслить функционально](../series/thinking-functionally.md).

* **Building with composition**. Composition is the 'glue' that allows us build larger systems from smaller ones. This is not an optional technique, but is at the very heart of the functional style. Almost every line of code is a composable expression (see below). Composition is used to build basic functions, and then functions that use those functions, and so on. And the composition principle doesn't just apply to functions, but also to types (the product and sum types discussed below).
* **Factoring and refactoring**. The ability to factor a problem into parts depends how easily the parts can be glued back together. Methods and classes that might seem to be indivisible in an imperative language can often be broken down into surprisingly small pieces in a functional design. These fine-grained components typically consist of (a) a few very general functions that take other functions as parameters, and (b) other helper functions that specialize the general case for a particular data structure or application.
  Once factored out, the generalized functions allow many additional operations to be programmed very easily without having to write new code. You can see a good example of a general function like this (the fold function) in the [post on extracting duplicate code from loops](../posts/conciseness-extracting-boilerplate.md).
* **Good design**. Many of the principles of good design, such as "separation of concerns", "single responsibility principle", ["program to an interface, not an implementation"](../posts/convenience-functions-as-interfaces.md), arise naturally as a result of a functional approach. And functional code tends to be high level and declarative in general.

The following posts in this series will have examples of how functions can make code more
concise and convenient, and then for a deeper understanding, there is a whole series on [thinking functionally](../series/thinking-functionally.md).

### Выражения, нежели утверждения/инструкции (Expressions rather than statements)

В функциональных языках нет утверждений/инструкций, есть только выражения. Это значит, что любой блок кода всегда возвращает значение, и бо́льшие блоки формируются путём комбинирования меньших с использованием композиции, а не просто упорядочиванием инструкций.

Если вы когда либо использовали LINQ или SQL, вы уже должны быть знакомы с языками, базирующимися на выражениях. Например, в чистом SQL вы не может осуществлять присваивания. Вместо этого вы должны создавать вложенные запросы внутри бо́льших запросов.

> In functional languages, there are no statements, only expressions. That is, every chunk of code always returns a value,
and larger chunks are created by combining smaller chunks using composition rather than a serialized list of statements.

> If you have used LINQ or SQL you will already be familiar with expression-based languages. For example, in pure SQL,
you cannot have assignments. Instead, you must have subqueries within larger queries.

```sql
SELECT EmployeeName
FROM Employees
WHERE EmployeeID IN
	(SELECT DISTINCT ManagerID FROM Employees)  -- вложенный запрос
```

В F# делается точно так же -- каждое определение функции является целым выражением, а не набором инструкций.

И это может быть неочевидным, однако код, построенный из выражений одновременно и более безопасен, и более компактен, чем код из инструкций.
Для того, чтобы в этом убедиться, давайте сравним фрагмент C#-кода, основанного на инструкциях, с эквивалентным, но состоящем из выражений.

Для начала код на инструкциях. Инструкции/утверждения не имеют возвращаемого значения, поэтому вам придется использовать временные переменные, которым будут присваиваться какие-то значения внутри тел инструкций.

> F# works in the same way -- every function definition is a single expression, not a set of statements.

> And it might not be obvious, but code built from expressions is both safer and more compact than using statements.
> To see this, let's compare some statement-based code in C# with the equivalent expression-based code.

> First, the statement-based code. Statements don't return values, so you have to use temporary variables that are assigned to from within statement bodies.

```csharp
// C#-код, основанный на инструкциях
int result;
if (aBool)
{
  result = 42;
}
Console.WriteLine("result={0}", result);
```

Так как `if-then`-блок является инструкцией, переменная `result` должна быть объявлена *вне* этой инструкции, но значение должно быть присвоено ей *внутри* инструкции, что приводит к проблемам, наподобие следующих:

* Какое исходное значение должно быть присвоено переменной `result`?
* Что, если я забуду присвоить значение переменной `result`?
* Какое значение имеет переменная `result` в случае выполнения блока "else"?

Вот, для сравнения, такой же код, переписанный в стиле выражений:

> Because the `if-then` block is a statement, the `result` variable must be defined *outside* the statement but assigned to from *inside* the statement, which leads to issues such as:

> * What initial value should `result` be set to?
> * What if I forget to assign to the `result` variable?
> * What is the value of the `result` variable in the "else" case?

> For comparison, here is the same code, rewritten in an expression-oriented style:

```csharp
// C#-код, базирующийся на выражениях
int result = (aBool) ? 42 : 0;
Console.WriteLine("result={0}", result);
```

В версии с выражениями не встречается ни одна из вышеописанных проблем:

* Переменная `result` объявляется одновременно с присваиванием ей значения. Не нужно задавать никаких переменных вне выражения и незачем беспокоиться о том, какое исходное значение нужно назначить переменной.
* Ветвь "else" обрабатывается явно. Нет возможности упустить присваивание переменной значения ни в одной из ветвей.
* Невозможно забыть означить переменную `result`, так как в этом случае переменной попросту не будет существовать!

Стиль, ориентированный на выражения, не является в F# предметом выбора. Это одна из тех вещей, для которых требуется изменить подход к программированию при начале работы с этим языком, имея опыт лишь императивного программирования.

> In the expression-oriented version, none of these issues apply:

> * The `result` variable is declared at the same time that it is assigned. No variables have to be set up "outside" the expression and there is no worry about what initial value they should be set to.
> * The "else" is explicitly handled. There is no chance of forgetting to do an assignment in one of the branches.
> * It is not possible to forget to assign `result`, because then the variable would not even exist!

> Expression-oriented style is not a choice in F#, and it is one of the things that requires a change of approach when coming from an imperative background.

### Алгебраические типы данных (Algebraic Types)

Система типов в F# основана на понятии **алгебраических типов данных**. В этом случае новые составные типы строятся путём комбинирования существующих типов двумя различными способами:

* Комбинированием значений, где каждое значение берётся из множества типов. Такие типы называются "типами-произведениями".
* Как дизъюнктивное объединение, представляющее выбор из множества типов. Такие типы называются "типами-суммами".

например, используя существующие типы `int` и `bool`, мы можем создать новый тип-произведение, который должен содержать в себе их оба:

> The type system in F# is based on the concept of **algebraic types**. That is, new compound types are built by combining existing types in two different ways:

> * First, a combination of values, each picked from a set of types. These are called "product" types.
> * Of, alternately, as a disjoint union representing a choice between a set of types. These are called "sum" types.

> For example, given existing types `int` and `bool`, we can create a new product type that must have one of each:

```fsharp
// объявление
type IntAndBool = {intPart: int; boolPart: bool}

// использование
let x = {intPart=1; boolPart=false}
```

Или же мы можем создать новый тип-сумму/тип-объединение, предоставляющий выбор между типами:

> Alternatively, we can create a new union/sum type that has a choice between each type:

```fsharp
// объявление
type IntOrBool =
	| IntChoice of int
	| BoolChoice of bool

// использование
let y = IntChoice 42
let z = BoolChoice true
```

Эти "типы выбора" недоступны в C#, однако невероятно полезны для моделирования многих реальных задач, такие как состояния в конечном автомате (которые, на удивление, часто встречаются во многих предметных областях).

А путём подобного комбинирования типов-произведений и типов-сумм, очень просто спроектировать обширное множество типов, которые в точности описывают любую бизнес-модель. В качестве примерах, демонстрирующих это, посмотрите статьи на темы: ["Определения типов с малыми накладными расходами"](../posts/conciseness-type-definitions.md) и ["Использование системы типов для обеспечения корректности кода"](../posts/correctness-type-checking).

> These "choice" types are not available in C#, but are incredibly useful for modeling many real-world cases, such as states in a state machine (which is a surprisingly common theme in many domains).

> And by combining "product" and "sum" types in this way, it is easy to create a rich set of types that accurately models any business domain.
> For examples of this in action, see the posts on [low overhead type definitions](../posts/conciseness-type-definitions.md) and [using the type system to ensure correct code](../posts/correctness-type-checking).


### Сопоставление с образцом для потока управления (Pattern matching for flow of control)

Многие императивные языки предоставляют множество инструкций потока управления для ветвления и циклов:

* `if-then-else` (и тернарная версия `bool ? if-true : if-false`);
* `case` или `switch` инструкции;
* `for` и `foreach` циклы, с `break` и `continue`;
* `while` и `until` циклы;
* и даже страшную `goto`.

F# также поддерживает некоторые из них, однако F# также поддерживает и наиболее общую форму условного выражения, которой является **сопоставление с образцом (pattern matching)**.

Типичное выражения сопоставления с образцом, заменяющее `if-then-else`, выглядит следующим образом:

> Most imperative languages offer a variety of control flow statements for branching and looping:

> * `if-then-else` (and the ternary version `bool ? if-true : if-false`)
> * `case` or `switch` statements
> * `for` and `foreach` loops, with `break` and `continue`
> * `while` and `until` loops
> * and even the dreaded `goto`

> F# does support some of these, but F# also supports the most general form of conditional expression, which is **pattern-matching**.

> A typical matching expression that replaces `if-then-else` looks like this:

```fsharp
match booleanExpression with
| true -> // ветвь для true
| false -> // ветвь для false
```

А замена для `switch` может выглядеть так:

> And the replacement of `switch` might look like this:

```fsharp
match aDigit with
| 1 -> // случай, когда digit=1
| 2 -> // случай, когда digit=2
| _ -> // во всех остальных случаях
```

Наконец, циклы как правило реализуются с использованием рекурсией и обычно выглядит следующим образом:

> Finally, loops are generally done using recursion, and typically look something like this:

```fsharp
match aList with
| [] ->
     // случай пустого списка
| first::rest ->
    // Случай, когда в списке есть хотя бы один элемент.
    // Обрабатываем первый элемент, а затем
    // рекурсивно обрабатываем оставшуюся часть списка
```

Хотя выражение сопоставления сначала может показаться излишне сложным, позже вы увидите на практике, что оно одновременно и элегантное, и мощное.

Чтобы узнать преимущества механизма сопоставления с образцом, посмотрите статью ["(?)Исчерпывающее(?) сопоставление с образцом"](../posts/correctness-exhaustive-pattern-matching), а чтобы посмотреть рабочий пример, обильно использующий сопоставление с образцом, посмотрите статью ["Пример про римские цифры"](../posts/roman-numerals.md).

> Although the match expression seems unnecessarily complicated at first, you'll see that in practice it is both elegant and powerful.

> For the benefits of pattern matching, see the post on [exhaustive pattern matching](../posts/correctness-exhaustive-pattern-matching), and for a worked example that uses pattern matching heavily, see the [roman numerals example](../posts/roman-numerals.md).

### Сопоставление с образцом на типах-объединениях (Pattern matching with union types) ###

Ранее упоминалось о том, что F# поддерживает тип-объединение или "тип-выбор". Он используется вместо механизма наследования для работы с различными вариантами подчинённых типов. Сопоставление с образцом легко/бесшовно работает с такими типами в целях создания потока управления для каждого из вариантов.

В следующем примере мы создадим типа `Shape`, представляющий собой различные фигуры, а затем определим функцию `draw` с различным поведением для каждого вида фигуры. Это похоже на полиморфизм в объектно-ориентированном языке, однако основано на функциях.

> We mentioned above that F# supports a "union" or "choice" type. This is used instead of inheritance to work with different variants of an underlying type. Pattern matching works seamlessly with these types to create a flow of control for each choice.

> In the following example, we create a `Shape` type representing four different shapes and then define a `draw` function with different behavior for each kind of shape.
> This is similar to polymorphism in an object oriented language, but based on functions.

```fsharp
type Shape =        // задание объединения альтернатив
| Circle of int
| Rectangle of int * int
| Polygon of (int * int) list
| Point of (int * int)

let draw shape =    // задание функции "draw" с фигурой shape в качестве параметра
  match shape with
  | Circle radius ->
      printfn "The circle has a radius of %d" radius
  | Rectangle (height,width) ->
      printfn "The rectangle is %d high by %d wide" height width
  | Polygon points ->
      printfn "The polygon is made of these points %A" points
  | _ -> printfn "I don't recognize this shape"

let circle = Circle(10)
let rect = Rectangle(4,5)
let polygon = Polygon( [(1,1); (2,2); (3,3)])
let point = Point(2,3)

[circle; rect; polygon; point] |> List.iter draw
```

Отметим несколько нюансов:

* Как обычно, мы не обязаны указывать типы явно. Компилятор правильно определяет, что параметр `"shape"` для функции "`draw`" имеет тип `Shape`.
* Вы можете видеть, что `match..with` не только сопоставляет внутреннюю структуру shape, но также устанавливает значения, в зависимости от того, чему соответствует shape.
* Символ нижнего подчёркивания (`_`) подобен ветви "`default`" в инструкции "`switch`", за исключением того что в F# он необходим -- любой возможный случай должен всегда обрабатываться.Посмотрите, что произойдёт при компиляции, если вы закомментируете строку:
  ```fsharp
    | _ -> printfn "I don't recognize this shape"
  ```

Подобные "типы с выбором" можно некоторым образом сымитировать в C#, используя вложенные классы или интерфейсы, однако в системе типов C# нету встроенной поддержки для такого рода (?)исчерпывающего(?) сопоставления с проверкой на предмет ошибок.

> A few things to note:

> * As usual, we didn't have to specify any types. The compiler correctly determined that the shape parameter for the "draw" function was of type `Shape`.
> * You can see that the `match..with` logic not only matches against the internal structure of the shape, but also assigns values based on what is appropriate for the shape.
> * The underscore is similar to the "default" branch in a switch statement, except that in F# it is required -- every possible case must always be handled. If you comment out the line
  ```fsharp
    | _ -> printfn "I don't recognize this shape"
  ```

> see what happens when you compile!

> These kinds of choice types can be simulated somewhat in C# by using subclasses or interfaces, but there is no built in support in the C# type system for this kind of exhaustive matching with error checking.

