---
layout: post
title: "Сравнение F# и C#: Сортировка"
description: "Где показано, что F# более декларативен, чем C#, и где происходит знакомство с механизмом сопоставления с образцом."
nav: why-use-fsharp
seriesId: "Зачем использовать F#?"
seriesOrder: 4
categories: [F# vs C#]
---

В этом примере мы реализуем алгоритм сортировки, подобный быстрой сортировке (Quick-Sort), для списков и сравним F#-реализацию с реализацией на С#.

Логика этого упрощённого алгоритма быстрой сортировки следующая:

<pre>
Если список пуст, то делать ничего не нужно.
Иначе же:
  1. Взять первый элемент списка.
  2. Найти все элементы в оставшейся части списка, которые меньше первого элемента, и отсортировать их.
  3. Найти все элементы в оставшейся части списка, которые больше первого элемента или равны ему, и отсортировать их.
  4. Скомбинировать все три части вместе для получения итогового результата:
      (отсортированные меньшие элементы + первый элемент +
       отсортированные большие элементы).
</pre>

Стоит отметить, что это упрощённый алгоритм и он не оптимизирован (и он не сортирует "на месте", как в настоящей быстрой сортировке); сейчас важнее сделать акцент на ясности/простоте понимания.

Вот код на F#:

> In this next example, we will implement a quicksort-like algorithm for sorting lists and compare an F# implementation to a C# implementation.

> Here is the logic for a simplified quicksort-like algorithm:

<pre>
If the list is empty, there is nothing to do.
Otherwise:
  1. Take the first element of the list
  2. Find all elements in the rest of the list that
      are less than the first element, and sort them.
  3. Find all elements in the rest of the list that
      are >= than the first element, and sort them
  4. Combine the three parts together to get the final result:
      (sorted smaller elements + firstElement +
       sorted larger elements)
</pre>

> Note that this is a simplified algorithm and is not optimized (and it does not sort in place, like a true quicksort); we want to focus on clarity for now.

> Here is the code in F#:

```fsharp
let rec quicksort list =
   match list with
   | [] ->                            // Если список пуст
        []                            // вернуть пустой список
   | firstElem::otherElements ->      // Если список не пустой
        let smallerElements =         // извлечь меньшие элементы
            otherElements
            |> List.filter (fun e -> e < firstElem)
            |> quicksort              // и отсортировать их
        let largerElements =          // извлечь бо́льшие элементы
            otherElements
            |> List.filter (fun e -> e >= firstElem)
            |> quicksort              // и отсортировать их
        // Вернуть список, скомбинированных из этих трёх частей
        List.concat [smallerElements; [firstElem]; largerElements]

//тест
printfn "%A" (quicksort [1;5;23;18;9;1;3])
```

Следует ещё раз отметить, что это не оптимизированная реализация алгоритма, но спроектированная для точного отражения сути алгоритма.

Давайте рассмотрим этот код:

* Здесь нигде нет объявлений типов. Эта функция будет работать на любом списке, содержащем сравнимые элементы <!--- (прим.: значения типа, поддерживающего операцию сравнения) --> (то есть почти любой из F#-типов, т.к. все они автоматически имеют/поддерживают стандартную функцию сравнения).
* Вся функция рекурсивна -- об этом компилятору сигнализирует ключевое слово `rec` в "`let rec quicksort list = `".
* Конструкция `match..with` в некотором роде похожа на `switch/case`-инструкцию. Каждая ветвь отделяется с помощью вертикальной черты, например:

> Again note that this is not an optimized implementation, but is designed to mirror the algorithm closely.

> Let's go through this code:

> * There are no type declarations anywhere. This function will work on any list that has comparable items (which is almost all F# types, because they automatically have a default comparison function).
> * The whole function is recursive -- this is signaled to the compiler using the `rec` keyword in "`let rec quicksort list =`".
> * The `match..with` is sort of like a switch/case statement. Each branch to test is signaled with a vertical bar, like so:

```fsharp
match x with
| caseA -> something
| caseB -> somethingElse
```
* Ветвь "`match`" с `[]` сопоставляет c пустым списком и возвращает пустой список.
* Ветвь "`match`" с `firstElem::otherElements` делает две вещи.
    * Во-первых, она сопоставляет лишь с непустым списком.
    * Во-вторых, в ней автоматически создаётся два новых значения. Первое -- для первого элемента и именованное как "`firstElem`", а второе -- для списка с остальными элементами и именованное как "`otherElements`". С точки зрения C#, это как иметь "`switch`"-инструкцию которая поддерживает не только ветвление, но также позволяет объявлять переменные и означивать их *одновременно*. <!--- Примечание насчёт сопоставления с образцом, появившемся в C# 7 -->
* Конструкция с `->` -- это своего рода лямбда-функция (`=>`) в C#. <!--- Наверное, стоит указать `fun ... -> ...` вместо просто `->`, а то `->` ещё и в сопоставлении с образцом используется --> Эквивалентная лямбда функция будет выглядеть как-то так: `(firstElem, otherElements) => do something`.
* "`smallerElements`" принимает оставшуюся часть списка <!--- (прим.: также список) -->, фильтрует его по первому элементу (*прим.: `firstElem`*), используя (?)встраиваемое(?) лямбда-выражение с оператором "`<`" и затем рекурсивно передаёт результат в функцию быстрой сортировки (`quicksort`).
* "`largerElements`" делает всё то же самое, за ислючением использования оператора "`>=`" <!--- Возможно, стоит добавить: "вместо оператора "`<`" в лямбда-выражении" ? -->.
* Наконец, конструируется результирующий список, используя функция конкатенации списков "`List.concat`". Для этого первый элемент (*прим.: `firstElem`*) должен быть "упакован" в список, для чего и используются квадратные скобки.
* Опять же стоит отметить, что ключевое слово "return" не используется -- возвращаемым является последнее значение. В ветви с "`[]`" возвращаемое значение -- пустой список, а в главной ветви -- новый сконструированный/новообразованный список.

Вот, для сравнения, реализация на C# в старом стиле (без использования LINQ).

> * The "`match`" with `[]` matches an empty list, and returns an empty list.
> * The "`match`" with `firstElem::otherElements` does two things.
>   * First, it only matches a non-empty list.
>   * Second, it creates two new values automatically. One for the first element called "`firstElem`", and one for the rest of the list, called "`otherElements`".
    In C# terms, this is like having a "switch" statement that not only branches, but does variable declaration and assignment *at the same time*.
> * The `->` is sort of like a lambda (`=>`) in C#. The equivalent C# lambda would look something like `(firstElem, otherElements) => do something`.
> * The "`smallerElements`" section takes the rest of the list, filters it against the first element using an inline lambda expression with the "`<`" operator and then pipes the result into the quicksort function recursively.
> * The "`largerElements`" line does the same thing, except using the "`>=`" operator
> * Finally the resulting list is constructed using the list concatenation function "`List.concat`". For this to work, the first element needs to be put into a list, which is what the square brackets are for.
> * Again note there is no "return" keyword; the last value will be returned. In the "`[]`" branch the return value is the empty list, and in the main branch, it is the newly constructed list.

> For comparison here is an old-style C# implementation (without using LINQ).

```csharp
public class QuickSortHelper
{
   public static List<T> QuickSort<T>(List<T> values)
      where T : IComparable
   {
      if (values.Count == 0)
      {
         return new List<T>();
      }

      //извлечение первого элемента
      T firstElement = values[0];

      // извлечение бо́льшего и меньшего элементов
      var smallerElements = new List<T>();
      var largerElements = new List<T>();
      for (int i = 1; i < values.Count; i++)  // исходным значением для i является 1,
      {                                       // а не 0!
         var elem = values[i];
         if (elem.CompareTo(firstElement) < 0)
         {
            smallerElements.Add(elem);
         }
         else
         {
            largerElements.Add(elem);
         }
      }

      //возвращаем результат
      var result = new List<T>();
      result.AddRange(QuickSort(smallerElements.ToList()));
      result.Add(firstElement);
      result.AddRange(QuickSort(largerElements.ToList()));
      return result;
   }
}
```

Сравнивая эти варианты кода, мы опять же можем видеть, что F#-код гораздо более компактный, менее "зашумлён" и не требует указания типов.

Более того, F#-код читается потчи точно также, как и непосредственно алгоритм, в отличии от C#-версии. Это ещё одно ключевое преимущество F# -- код обычно более декларативный ("что сделать") и менее императивный ("как это сделать"), чем код на C#, и поэтому он гораздо более "самодокументирующий".

> Comparing the two sets of code, again we can see that the F# code is much more compact, with less noise and no need for type declarations.

> Furthermore, the F# code reads almost exactly like the actual algorithm, unlike the C# code.  This is another key advantage of F# -- the code is generally more declarative ("what to do") and less imperative ("how to do it") than C#, and is therefore much more self-documenting.


## Реализация в функциональном стиле на C# (A functional implementation in C#) ##

Вот более современная реализация, в "функциональном стиле", использующая LINQ и методы расширений:

> Here's a more modern "functional-style" implementation using LINQ and an extension method:

```csharp
public static class QuickSortExtension
{
    /// <summary>
    /// Реализация в качестве метода расширения для IEnumerable
    /// </summary>
    public static IEnumerable<T> QuickSort<T>(
        this IEnumerable<T> values) where T : IComparable
    {
        if (values == null || !values.Any())
        {
            return new List<T>();
        }

        // разбиение списка на первый элемент и оставшиеся
        var firstElement = values.First();
        var rest = values.Skip(1);

        // получение меньшего и бо́льшего элементов
        var smallerElements = rest
                .Where(i => i.CompareTo(firstElement) < 0)
                .QuickSort();

        var largerElements = rest
                .Where(i => i.CompareTo(firstElement) >= 0)
                .QuickSort();

        // возврат результата
        return smallerElements
            .Concat(new List<T>{firstElement})
            .Concat(largerElements);
    }
}
```

Так гораздо аккуратнее/яснее, и читается практически так же, как и F#-версия. Но, к сожалению, никак не избежать излишней "зашумлённости" сигнатуры функции.

> This is much cleaner, and reads almost the same as the F# version.  But unfortunately there is no way of avoiding the extra noise in the function signature.

## Корректность (Correctness)

Наконец, полезным побочным эффектом от этой компактности заключается в том, что F#-код обычно работает с первого запуска, тогда как C#-код может потребовать дополнительной отладки.

Действительно, в процессе разработки этих примеров C#-версия "в старом стиле" получилась изначально неправильной, из-за чего потребовалось немного отладить её, прежде чем получить корректный вариант. Особенно каверзными частями были цикл `for` (начинающийся с 1, а не с 0) и сравнение `CompareTo` (которое я понял неправильно), вдобавок к этому было очень просто ненамеренно изменить/модифицировать входной список. Версия в функциональном стиле же, во втором C#-примере, помимо того, что выглядит понятнее, её оказалось проще правильно спроектировать.

Но даже функциональная C#-версия имеет недостатки, по сравнению с F#-версией. Например, ввиду того, что в F# используется сопоставление с образцом (pattern matching), пустой список не может попасть в ветвь "с непустым списком". С другой стороны, если мы в C#-версии забудем сделать такую проверку:

> Finally, a beneficial side-effect of this compactness is that F# code often works the first time, while the C# code may require more debugging.

> Indeed, when coding these samples, the old-style C# code was incorrect initially, and required some debugging to get it right. Particularly tricky areas were the `for` loop (starting at 1 not zero) and the `CompareTo` comparison (which I got the wrong way round), and it would also be very easy to accidentally modify the inbound list. The functional style in the second C# example is not only cleaner but was easier to code correctly.

> But even the functional C# version has drawbacks compared to the F# version. For example, because F# uses pattern matching, it is not possible to branch to the "non-empty list" case with an empty list. On the other hand, in the C# code, if we forgot the test:

```csharp
if (values == null || !values.Any()) ...
```

то тогда при извлечении первого элемента:

> then the extraction of the first element:

```csharp
var firstElement = values.First();
```

возникнет исключение ("упадёт с выдачей исключения"). Компилятор не может сделать этого. Как часто в своём коде вы использовали `FirstOrDefault` вместо `First` для обеспечения "защищённости" кода? Вот пример шаблона кода, который очень часто встречается в C#, но редко в F#:

> would fail with an exception. The compiler cannot enforce this for you.  In your own code, how often have you used `FirstOrDefault` rather than `First` because you are writing "defensive" code. Here is an example of a code pattern that is very common in C# but is rare in F#:

```csharp
var item = values.FirstOrDefault();  // вместо .First()
if (item != null)
{
   // сделать что-то, если значение item валидно
}
```

Сопоставление с образцом и ответвление как один этап в F# позволяет вам избежать подобного во многих случаях.

The one-step "pattern match and branch" in F# allows you to avoid this in many cases.

## Послесловие (Postscript)

Вышеприведённый пример реализации на F# на самом деле довольно громоздкий по меркам F#!

Ради интереса, вот, как выглядит более привычная краткая версия:

> The example implementation in F# above is actually pretty verbose by F# standards!

> For fun, here is what a more typically concise version would look like:

```fsharp
let rec quicksort2 = function
   | [] -> []
   | first::rest ->
        let smaller,larger = List.partition ((>=) first) rest
        List.concat [quicksort2 smaller; [first]; quicksort2 larger]

// код для проверки
printfn "%A" (quicksort2 [1;5;23;18;9;1;3])
```

Неплохо для четырёх строк кода, а когда вы привыкаете к синтаксису, ещё и довольно читаемо.

> Not bad for 4 lines of code, and when you get used to the syntax, still quite readable.
