# Исправление ошибок компиляции в F#
### Почему мой код не компилируется?

Как говорится, "если код компилируется - значит он правильный", но может порой бывает очень сложно получить компилирующийся код. Поэтому, данная статья посвящена тому, как помочь устранить ошибки компиляции в вашем F# коде.

Сначала я расскажу о некоторых общих советах по устранению наиболее распространённых ошибок, совершаемых начинающими F# разработчиками. После этого, я опишу каждое, из наиболее часто встречаемых сообщениях об ошибке в подробностях, приведу примеры, в которых данные ошибки могут произойти, и как их исправить.

[(Перейти к номерам ошибок)](#NumericErrors)

## Общие рекомендации по устранению ошибок ##

Безусловно, самое важное, что вы можете сделать - это потратить время и силы, чтобы точно понять, как работает F#, особенно базовые понятия, связанные с функциями и системой типов. Поэтому, пожалуйста, прочитайте и перечитайте серию ["мыслить функционально"](http://fsharpforfunandprofit.com/series/thinking-functionally.html) и ["понимание типов F#"](http://fsharpforfunandprofit.com/series/understanding-fsharp-types.html), поэкспериментируйте с примерами, проникнитесь идеями данного языка перед тем, как попробовать писать настоящий код. Если вы не понимаете, как работают функции и типы, то и ошибки компилятора для вас не будут иметь никакого смысла.

Если вы пришли из императивного языка, такого как C# то возможно, имеете некоторые вредные привычки, например, полагатетесь на отладчик, чтобы найти и исправить неверный код. В F# у вас не получится забраться так далеко, поскольку компилятор довольно строг во многих случаях. И конечно же нет инструмента для пошаговой “отладки” компилятора (And of course, there is no tool to “debug” the compiler and step through its processing). Лучший инструмент для отладки ошибок компилятора - это ваш мозг, и F# заставляет вас использовать его!

Тем не менее, есть ряд распространённых ошибок, которые совершают новички, и я кратко пройдусь по ним.

### Не используйте круглые скобки, при вызове функции ###

В F#, пробел является стандартным разделителем для параметров функций. Вы редко придётся использовать круглые скобки, в частности, не используйте скобки, когда вызываете функцию.

```fsharp
let add x y = x + y
let result = add (1 2)  // неправильно
    // error FS0003: This value is not a function and cannot be applied
let result = add 1 2    // правильно
```
_(Ошибка FS0003: Это значение не является функцией, и применить его невозможно)_

### Не используйте кортежи, для передачи отдельных параметров ###

Если в параметре есть запятая - значит это кортеж. Кортеж - это один объект, не два. Таким образом, вы получите ошибку, о неверном типе параметра, или "слишком мало параметров".

```fsharp
addTwoParams (1,2)  // попытка передать в качестве аргумента один кортеж, вместо двух аргументов
   // error FS0001: This expression was expected to have type
   // int but here has type 'a * 'b 
```
_(Ошибка FS0001: Несоответствие  типов.  Требуется  int, но получен 'a * 'b)_

Компилятор рассматривает `(1,2)` как один кортеж, который передаётся в "`addTwoParams`". Затем он жалуется, что первый параметр в `addTwoParams` - это int, а мы пытаемся передать кортеж.

Если вы попытаетесь передать *два* аргумента в функцию, принимающую *один* кортеж, вы получите другую невнятную ошибку.

```fsharp
addTuple 1 2   // попытка передать два аргумента, вместо одного кортежа
  // error FS0003: This value is not a function and cannot be applied
``` 
_(Ошибка FS0003: This value is not a function and cannot be applied)_
  
### Остерегайтесь ошибок "слишком мало аргументов" и "слишком много аргументов" (Watch out for too few or too many arguments TODO) ###

Компилятор F# не будет жаловаться, если вы передадите слишком мало аргументов функции (на самом деле, "частичное применение" - это очень важная фукнция), но если вы не понимаете, что происходит, то вы часто будете получать странные ошибки "несоответствие типа" позже.

Similarly the error for having too many arguments is typically "This value is not a function" rather than a more straightforward error. (TODO)

Функции, подобные "printf" в этом отношении достаточно строгие. Количество аргументов должно быть точным.

Это очень важная тема -- важно понимать, как работает частичное применение. Смотрите серию ["думать функционально"](/series/thinking-functionally.html), чтобы подробнее ознакомиться с данной возможностью.

### Используйте точки с запятой, для разделителей списков ###

В тех немногих местах, где F# нуждается в явном символе разделителя, например в списках и записях, используется точка с запятой. Запятые никогда не используются. (Как сломанная запись (broken record TODO), напомню, что запятые используются в кортежах).

```fsharp
let list1 = [1,2,3]    // неправильно! В данном случае, список содержит ОДИН элемент 
                       // кортеж, с тремя параметрами
let list1 = [1;2;3]    // правильно

type Customer = {Name:string, Address: string}  // неправильно
type Customer = {Name:string; Address: string}  // правильно
```

### Не используйте ! для отрицания, или != для "не равено" ###

Символ восклицательного знака не является оператором "НЕТ". Это оператор разыменовывания для изменяемых ссылок. Если вы по ошибке попробуете использовать его для отрицания, вы получите следующие ошибки:

```fsharp
let y = true
let z = !y
// => error FS0001: This expression was expected to have 
//    type 'a ref but here has type bool    
```
_(Ошибка FS0001: В данном выражении требовалось наличие типа 'a ref, но получен тип bool)_


В данном случае, правильным является использовать ключевое слово "not". В данном случае, думайте будто пишете на SQL или VB, а не на C.

```fsharp
let y = true
let z = not y       //правильно
```

И для "не равно", используйте "<>", так же, как в SQL или VB.

```fsharp
let z = 1 <> 2      //правильно
```

### Не используйте "=" для присваивания значения изменяемой переменной ###

Если вы используете изменяемые значения, то операция присваивания записывается так "`<-`". При использовании символа равенства, вы можете вместо ошибки получить неожиданный результат.
```fsharp
let mutable x = 1
x = x + 1          // возвращает false. x не равен x+1 
x <- x + 1         // присваивает значение x+1 переменной x 
```

### Следите за скрытыми символами табуляции ###

Правила отступов очень просты, и их не трудно понять. Но вы не можете использовать табы, допускается только использование пробелов.

```fsharp
let add x y =
{tab}x + y
// => error FS1161: TABs are not allowed in F# code 
```
_(Ошибка FS1161: В коде F# табуляция может использоваться только при использовании параметра #indent "off")_

Обязательно настройте редактор для замены табов на пробелы. И следите при вставке кода из другого места. Если вы столкнулись с множеством проблем в небольшом куске кода, попробуйте удалить отступы, и снова добавить их.

### Don't mistake simple values for function values ###

Если вы пытаетесь создать указатель на функцию, или делегат (function pointer or delegate TODO), следите за тем, чтобы случайно не создать обычное значение, которое уже вычислено

If you want a parameterless function that you can reuse, you will need to explicitly pass a unit parameter, or define it as a lambda.
Если вы хотите создать функцию без параметров, которую можно переиспользовать, то вам необходимо явно передать параметр unit, или определить её как лямбда-выражение.

```fsharp
let reader = new System.IO.StringReader("hello")
let nextLineFn   =  reader.ReadLine()  //неправильно
let nextLineFn() =  reader.ReadLine()  //правильно
let nextLineFn   =  fun() -> reader.ReadLine()  //правильно

let r = new System.Random()
let randomFn   =  r.Next()  //неправильно
let randomFn() =  r.Next()  //правильно
let randomFn   =  fun () -> r.Next()  //правильно
```

Чтобы узнать более подробно о функциях без параметров, смотрите серию ["думать функционально"](/series/thinking-functionally.html)

### Советы по устранению ошибок "недостаточно информации" ("not enough information" errors) ###

Компилятор F#, в настоящее время, является однопроходным компилятором, слева направо, поэтому информация о типах, объявленных в коде ниже не доступна компилятору, если она ещё не была проанализирована.

Это может вызвать ряд ошибок, таких как ["FS0072: Lookup on object of indeterminate type"](#FS0072) и ["FS0041: A unique overload for could not be determined"](#FS0041).

_(FS0072: Поиск объекта неопределенного типа)_

_(FS0041: Невозможно определить уникальную перегрузку)_

Предлагаемые исправления, для каждого из этих случаев описаны ниже, но есть некоторые общие принципы, которые могут помочь, если компилятор жалуется на недостающие типы, или недостаточное количество информации. Далее приведены следующие указания (TODO последнее предложение, кажется, немного бредовое):

* Определяйте различные типы, функции, значения перед тем, как их использовать (это так же позволит убедиться, что файлы компилируются в правильном порядке);
* Помещайте выражения, которые имеют "известные типы" перед теми, которые имеют "неизвестные типы" (In particular, you might be able reorder pipes and similar chained functions so that the typed objects come first. TODO);
* Аннотируйте по мере необходимости. Один важный "трюк" заключается в том, что сначала добавляются аннотации, пока всё не заработает, а затем постепенно убираются, пока не останется необходимый минимум.

Постарайтесь избегать аннотаций, если это возможно. Это не только не эстетично, но и делает код более "хрупким". Гораздо проще изменять типы, если они не имеют явных зависимостей.

<a id="NumericErrors"></a>
<div class="page-header">
	<h1>Ошибки компилятора F#</h1>
	<p class="subtitle">Список наиболее часто встречаемых ошибок, упорядоченный по номеру ошибки</p>
</div>

Ниже представлен список основных ошибок, которые мне кажутся необходимыми для документирования. Я специально не документировал ошибки, которые являются очевидными, только те, которые кажутся неясными для новичков.

Я буду продолжать добавлять ошибки в дальнейшем, и я приветствую любые предложения о дополнениях.

* [FS0001: The type 'X' does not match the type 'Y'](#FS0001)
* [FS0003: This value is not a function and cannot be applied](#FS0003)
* [FS0008: This runtime coercion or type test involves an indeterminate type](#FS0008)
* [FS0010: Unexpected identifier in binding](#FS0010a)
* [FS0010: Incomplete structured construct](#FS0010b)
* [FS0013: The static coercion from type X to Y involves an indeterminate type](#FS0013)
* [FS0020: This expression should have type 'unit'](#FS0020)
* [FS0030: Value restriction](#FS0030)
* [FS0035: This construct is deprecated](#FS0035)
* [FS0039: The field, constructor or member X is not defined](#FS0039)
* [FS0041: A unique overload for could not be determined](#FS0041)
* [FS0049: Uppercase variable identifiers should not generally be used in patterns](#FS0049)
* [FS0072: Lookup on object of indeterminate type](#FS0072)
* [FS0588: Block following this 'let' is unfinished](#FS0588)
	
<a id="FS0001"></a>
## FS0001: The type 'X' does not match the type 'Y' ##
### _(FS0001: В данном выражении требовалось наличие типа 'X', но получен тип 'Y')_ ###

Вероятно, это самая распространённая ошибка, с которой вы столкнётесь. Данная ошибка может появляться в самых разных контекстах, поэтому я сгруппировал наиболее распространённые случаи с примерами и исправлениями. Обращайте внимание на сообщение об ошибке, так как обычно оно даёт понять, в чём заключается проблема.

<table class="table table-striped table-bordered table-condensed">
<thead>
  <tr>
	<th>Сообщение об ошибке</th>
	<th>Возможные причины</th>
  </tr>
</thead>
<tbody>
  <tr>
	<td>
        The type 'float' does not match the type 'int'
        <br/>
        <em>(Тип 'float' не совпадает с типом 'int')</em>
    </td>
	<td><a href="#FS0001A">A. Нельзя смешивать float и int</a></td>
  </tr>
  <tr>
	<td>
    The type 'int' does not support any operators named 'DivideByInt'
    <br/>
    <em>(Тип 'int' не поддерживает оператор 'DivideByInt')</em>
    </td>
	<td><a href="#FS0001A">A. Нельзя смешивать float и int.</a></td>
  </tr>
  <tr>
	<td>
        The type 'X' is not compatible with any of the types
        <br/>
        <em>(Тип 'X' несовместим с любыми типами)</em>
    </td>
	<td><a href="#FS0001B">B. Использование неправильного числового типа.</a></td>
  </tr>
  <tr>
	<td>
        This type (function type) does not match the type (simple type).
        <br/>
        <em>(Тип (тип функции) не совпадает с типом (обычный тип))</em>
        <br/>
        Примечание: типы функций имеют стрелку в них, например <code>'a -> 'b</code>.
    </td>
	<td><a href="#FS0001C">C. Передаётся слишком много аргументов в функцию.</a></td>
  </tr>
  <tr>
	<td>
        This expression was expected to have (function type) but here has (simple type)
        <br/>
        <em>(Несоответствие типов. Требуется (тип функции), но получен (обычный тип))</em>
    </td>
	<td><a href="#FS0001C">C. Передаётся слишком много аргументов в функцию.</a></td>
  </tr>
  <tr>
	<td>
        This expression was expected to have (N part function) but here has (N-1 part function)
        <br/>
        <em>(В данном выражении требовалось наличие типа (N part function TODO), но получен тип (N-1 part function TODO))</em>
    </td>
	<td><a href="#FS0001C">C. Передаётся слишком много аргументов в функцию.</a></td>
  </tr>
  <tr>
	<td>
        This expression was expected to have (simple type) but here has (function type)
        <br/>
        <em>(Несоответствие типов. Требуется (обычный тип), но получен (тип функции))</em>
    </td>
	<td><a href="#FS0001D">D. Передаётся недостаточно аргументов в функцию.</a></td>
  </tr>
  <tr>
	<td>
        This expression was expected to have (type) but here has (other type)
        <br/>
        <em>(Несоответствие типов. Требуется (тип 'X'), но получен (другой тип))</em>
    </td>
	<td><a href="#FS0001E">E. Прямое несоответствие типа.</a><br>
	<a href="#FS0001F">F. Inconsistent returns in branches or matches. (TODO)</a><br>
	<a href="#FS0001G">G. Watch out for type inference effects buried in a function. (TODO)</a><br>
	</td>
  </tr>
  <tr>
	<td>
        Type mismatch. Expecting a (simple type) but given a (tuple type).
        <br/>
        <em>(Несоответствие типов. Требуется (обычный тип), но получен (кортежный тип))</em>
        Примечание: типы кортежей имеют звёздочку в названии, например <code>'a * 'b</code>.
    </td>
	<td><a href="#FS0001H">H. Вы использовали запятую, вместо пробела или точки с запятой?</a></td>
  </tr>
  <tr>
	<td>
        Type mismatch. Expecting a (tuple type) but given a (different tuple type).
        <br/>
        <em>(Несоответствие типов. Требуется (кортежный тип), но получен (другой кортежный тип))</em>
    </td>
	<td><a href="#FS0001I">I. Кортежи должны быть одного и того же типа.</a></td>
  </tr>
  <tr>
	<td>
        This expression was expected to have type 'a ref but here has type X
        <br/>
        <em>(В данном выражении требовалось наличие типа 'a ref, но получен тип X)</em>
    </td>
	<td><a href="#FS0001J">J. Не используйте оператор "!", как оператор "не".</a></td>
  </tr>
  <tr>
	<td>
        The type (type) does not match the type (other type)
        <br/>
        <em>(Тип (тип) не совпадает с типом (другой тип))</em>
    </td>
	<td><a href="#FS0001K">K. Приоритет операторов (особенно функции и конвейеры).</a></td>
  </tr>
  <tr>
	<td>
        This expression was expected to have type (monadic type) but here has type 'b * 'c
        <br/>
        <em>(В данном выражении требовалось наличие типа (монадический тип), но получен тип 'b * 'c)</em>
    </td>
	<td><a href="#FS0001L">L. let! ошибка в вычислимых выражениях (computation expressions).</a></td>
  </tr>
</tbody>
</table>

<a id="FS0001A"></a>
### A. Нельзя смешивать float и int ###

В отличие от C# и многих других императивных языков, в F# нельзя смешивать типы int и float в выражениях. Вы получите ошибку, если попытаетесь это сделать:

```fsharp
1 + 2.0  //неправильно
   // => error FS0001: The type 'float' does not match the type 'int'
```
_(Ошибка FS0001: Тип 'float' не совпадает с типом 'int')_

Исправление состоит в том, чтобы сначала преобразовать тип int в `float`:

```fsharp
float 1 + 2.0  //правильно
```

Эта проблема также может проявляться в библиотечных функциях и в других местах. Например, вы не можете применить функцию "`average`" к списку int.

```fsharp
[1..10] |> List.average   // неправильно
   // => error FS0001: The type 'int' does not support any 
   //    operators named 'DivideByInt'
```
_(Ошибка FS0001: Тип 'int' не поддерживает оператор 'DivideByInt')_
   
Вам необходимо сперва преобразовать int в float, как показано ниже:

```fsharp
[1..10] |> List.map float |> List.average  //правильно
[1..10] |> List.averageBy float  //правильно (использует averageBy)
```

<a id="FS0001B"></a>
### B. Использование неправильного числового типа ###

Вы получите ошибка "не совместимо", если числовое приведение не выполнено

```fsharp
printfn "hello %i" 1.0  // должно иметь тип int, а не float
  // error FS0001: The type 'float' is not compatible 
  //               with any of the types byte,int16,int32...
```
_(Ошибка FS0001: Тип 'float' не совместим с любыми типами byte,int16,int32..)_

Одним из возможных решений заключается в приведении типа float к типу int:

```fsharp
printfn "hello %i" (int 1.0)
```

<a id="FS0001C"></a>
### C. Передаётся слишком много аргументов в функцию ###

```fsharp
let add x y = x + y
let result = add 1 2 3
// ==> error FS0001: The type ''a -> 'b' does not match the type 'int'
```
_(Ошибка FS0001: Тип ''a -> 'b' не совпадает с типом 'int')_

Подсказка находится в ошибке. 

Исправление состоит в удалении одиного из аргументов!

Подобные ошибки вызваны передачей слишком большого количества аргументов в функцию `printf`.

```fsharp
printfn "hello" 42
// ==> error FS0001: This expression was expected to have type 'a -> 'b    
//                   but here has type unit    

printfn "hello %i" 42 43
// ==> Error FS0001: Type mismatch. Expecting a 'a -> 'b -> 'c    
//                   but given a 'a -> unit    

printfn "hello %i %i" 42 43 44
// ==> Error FS0001: Type mismatch. Expecting a  'a -> 'b -> 'c -> 'd    
//                   but given a 'a -> 'b -> unit   
```
_(Ошибка FS0001: В данном выражении требовалось наличие типа' a -> 'b, но получен тип unit )_
_(Ошибка FS0001: Несоответствие типов. Требуется 'a -> 'b -> 'c, но получен 'a -> unit)_
_(Ошибка FS0001: Несоответствие типов. Требуется 'a -> 'b -> 'c -> 'd, но получен 'a -> 'b -> unit)_

<a id="FS0001D"></a>
### D. Передаётся недостаточно аргументов в функцию ###

Если вы передадите не достаточно аргументов функции, вы получите частичное применение. Когда вы позже попытаетесь вызвать функцию, вы получите сообщение об ошибке, потому что это не обычный тип.

```fsharp
let reader = new System.IO.StringReader("hello");

let line = reader.ReadLine        //неправильно, но компилятор не жалуется
printfn "The line is %s" line     //ошибка компиляции здесь!
// ==> error FS0001: This expression was expected to have type string    
//                   but here has type unit -> string
```
_(Ошибка FS0001: В данном выражении требовалось наличие типа string, но получен тип unit -> string)_

Это особенно характерно для некоторых функций .NET библиотек, которые ожидают параметр типа unit, например как `ReadLine` выше.

Чтобы исправить данную ошибку, необходимо передать правильное количество параметров. Проверьте тип возвращаемого значения, чтобы убедиться, что это действительно простой тип. В случае с `ReadLine`, достаточно передать аргумент `()`.

```fsharp
let line = reader.ReadLine()      //правильно
printfn "The line is %s" line     //нет ошибки компиляции
```

<a id="FS0001E"></a>
### E. Прямое несоответствие типа ###

Самый простой случай возникновения данной ошибки заключается в том, что вы указали неправильный тип в строке print.

```fsharp
printfn "hello %s" 1.0
// => error FS0001: This expression was expected to have type string    
//                  but here has type float    
```
_(Ошибка FS0001: В данном выражении требовалось наличие типа string, но получен тип float)_

<a id="FS0001F"></a>
### F. Inconsistent return types in branches or matches (TODO) ###

Данная ошибка очень часто возникает в выражениях `if` или `match`. В подобных выражениях, каждая "ветвь" ДОЛЖНА возвращать значения одного, и того же типа, если это не так, то вы получите ошибку типа.

```fsharp
let f x = 
  if x > 1 then "hello"
  else 42
// => error FS0001: This expression was expected to have type string    
//                  but here has type int
```
_(Ошибка FS0001: В этом выражении ожидалось использование типа string, но используется тип int)_

```fsharp
let g x = 
  match x with
  | 1 -> "hello"
  | _ -> 42
// error FS0001: This expression was expected to have type
//               string but here has type int
```
_(Ошибка FS0001: В этом выражении ожидалось использование типа string, но используется тип int)_

Очевидно, что для исправления данной ошибки необходимо, чтобы каждая ветвь выражения возвращала один и тот же тип.

```fsharp
let f x = 
  if x > 1 then "hello"
  else "42"
  
let g x = 
  match x with
  | 1 -> "hello"
  | _ -> "42"
```
Запомните, что в случае когда ветвь "else" отсутствует, возвращаемым типом должен быть тип unit.

```fsharp
let f x = 
  if x > 1 then "hello"
// error FS0001: This expression was expected to have type
//               unit but here has type string    
```
_(Ошибка FS0001: В этом выражении ожидалось использование типа unit, но используется тип string)_

Если в вашем случае обе ветви не могут возвращать один и тот же тип, то вам необходимо создать union тип, который будет содержать оба значения.

```fsharp
type StringOrInt = | S of string | I of int  // новый union тип
let f x = 
  if x > 1 then S "hello"
  else I 42
```

<a id="FS0001G"></a>  
### G. Watch out for type inference effects buried in a function (TODO) ###

A function may cause an unexpected type inference that ripples around your code. For example, in the following, the innocent print format string accidentally causes `doSomething` to expect a string. (TODO)

```fsharp
let doSomething x = 
   // сделать что-то
   printfn "x is %s" x
   // сделать что-то ещё

doSomething 1
// => error FS0001: This expression was expected to have type string    
//    but here has type int    
```
_(Ошибка FS0001: В этом выражении ожидалось использование типа string, но используется тип int)_

The fix is to check the function signatures and drill down until you find the guilty party.  Also, use the most generic types possible, and avoid type annotations if possible. (TODO)

<a id="FS0001H"></a>  
### H. Вы использовали запятую, вместо пробела или точки с запятой? ###

Если вы являетесь новичком в F#, вы можете по привычке использовать запятую, вместо пробелов, для разделения аргументов функции:

```fsharp
// определим функцию с двумя параметрами
let add x y = x + 1

add(x,y)   // FS0001: This expression was expected to have 
           // type int but here has type  'a * 'b   
```
_(Ошибка FS0001: В этом выражении ожидалось использование типа int, но используется тип 'a * 'b)_

Исправление: не использовать запятую!

```fsharp
add x y    // Правильно
```

Обычно запятые *используются* в вызовах функций из стандартной библиотеки .NET.
Все эти функции принимают кортежи в качестве аргументов, поэтому использование запятой в данном случае является правильным. Фактически, эти вызовы выглядят так же, как и в C#:

```fsharp
// Правильно
System.String.Compare("a","b")

// Неправильно
System.String.Compare "a" "b"
```

  
<a id="FS0001I"></a>  
### I. Кортежи должны быть одного и того же типа ###

Кортежи с разными типами нельзя сравнивать. Попытка сравнения кортежа типа `int * int`, с кортежем типа `int * string` приведёт к ошибке:

```fsharp
let  t1 = (0, 1)
let  t2 = (0, "привет")
t1 = t2
// => error FS0001: Type mismatch. Expecting a int * int    
//    but given a int * string    
//    The type 'int' does not match the type 'string'
```
_(Ошибка FS0001: Несоответствие типов. Требуется int * int, но получен int * string. Тип 'int' не совпадает с типом 'string')_

И количество элементов в сравниваемых кортежах должно быть одинаковым:

```fsharp
let  t1 = (0, 1)
let  t2 = (0, 1, "привет")
t1 = t2
// => error FS0001: Type mismatch. Expecting a int * int    
//    but given a int * int * string    
//    The tuples have differing lengths of 2 and 3
```
_(Ошибка FS0001: Несоответствие типов. Требуется int * int, но получен int * int * string. Кортежи имеют различающиеся длины 2 и 3)_

Вы так же можете получить похожую ошибку сопоставления с образцом при декомпозиции кортежей:

```fsharp
let x,y = 1,2,3
// => error FS0001: Type mismatch. Expecting a 'a * 'b    
//                  but given a 'a * 'b * 'c    
//                  The tuples have differing lengths of 2 and 3

let f (x,y) = x + y
let z = (1,"hello")
let result = f z
// => error FS0001: Type mismatch. Expecting a int * int    
//                  but given a int * string    
//                  The type 'int' does not match the type 'string'
```
_(Ошибка FS0001: Несоответствие типов. Требуется 'a * 'b , но получен 'a * 'b * 'c. Кортежи имеют различающиеся длины 2 и 3)_
_(Ошибка FS0001: Несоответствие типов. Требуется int * int, но получен int * string. Тип 'int' не совпадает с типом 'string')_


<a id="FS0001J"></a>  
### J. Не используйте оператор "!", как оператор "не" ###

Если вы попробуете использовать `!` как оператор "not", вы получите ошибку типа с упоминанием слова "ref".

```fsharp
let y = true
let z = !y     //неправильно
// => error FS0001: This expression was expected to have 
//    type 'a ref but here has type bool    
```
_(Ошибка FS0001: В этом выражении ожидалось использование типа 'a ref, но используется тип bool)_

Чтобы исправить данную ошибку, используйте ключевое слово "not".

```fsharp
let y = true
let z = not y   //правильно
```


<a id="FS0001K"></a>  
### K. Приоритет операторов (особенно функции и конвейеры) ###

Если вы смешаете приоритет операторов, то получите ошибки типа. Как правило, применение функции имеет более высокий приоритет, чем другие операторы, поэтому вы получите ошибку в приведённом ниже примере:

```fsharp
String.length "hello" + "world"
   // => error FS0001:  The type 'string' does not match the type 'int'

// что действительно происходит
(String.length "hello") + "world"  
```
_(Ошибка FS0001: Тип 'string' не совпадает с типом 'int')_

Для исправления, используйте круглые скобки.

```fsharp
String.length ("hello" + "world")  // исправлено
```

Conversely, the pipe operator is low precedence compared to other operators.

```fsharp
let result = 42 + [1..10] |> List.sum
 // => => error FS0001:  The type ''a list' does not match the type 'int'

// что действительно происходит
let result = (42 + [1..10]) |> List.sum  
```
_(Ошибка FS0001: )_

Again, the fix is to use parentheses.

```fsharp
let result = 42 + ([1..10] |> List.sum)
```


<a id="FS0001L"></a>  
### L. let! ошибка в вычислимых выражениях (computation expressions) ###

Here is a simple computation expression:

```fsharp
type Wrapper<'a> = Wrapped of 'a

type wrapBuilder() = 
    member this.Bind (wrapper:Wrapper<'a>) (func:'a->Wrapper<'b>) = 
        match wrapper with
        | Wrapped(innerThing) -> func innerThing

    member this.Return innerThing = 
        Wrapped(innerThing) 
        
let wrap = new wrapBuilder()
```

However, if you try to use it, you get an error.

```fsharp
wrap {
    let! x1 = Wrapped(1)   // <== error here
    let! y1 = Wrapped(2)
    let z1 = x + y
    return z
    }
// error FS0001: This expression was expected to have type Wrapper<'a>
//               but here has type 'b * 'c    
```
_(Ошибка FS0001: )_

The reason is that "`Bind`" expects a tuple `(wrapper,func)`, not two parameters.  (Check the signature for bind in the F# documentation).

The fix is to change the bind function to accept a tuple as its (single) parameter.

```fsharp
type wrapBuilder() = 
    member this.Bind (wrapper:Wrapper<'a>, func:'a->Wrapper<'b>) = 
        match wrapper with
        | Wrapped(innerThing) -> func innerThing
```

<a id="FS0003"></a>
## FS0003: This value is not a function and cannot be applied ##

This error typically occurs when passing too many arguments to a function.

```fsharp
let add1 x = x + 1
let x = add1 2 3
// ==>   error FS0003: This value is not a function and cannot be applied
```
_(Ошибка FS0003: )_

It can also occur when you do operator overloading, but the operators cannot be used as prefix or infix.

```fsharp
let (!!) x y = x + y
(!!) 1 2              // ok
1 !! 2                // failed !! cannot be used as an infix operator
// error FS0003: This value is not a function and cannot be applied
```

<a id="FS0008"></a>
## FS0008: This runtime coercion or type test involves an indeterminate type ##

You will often see this when attempting to use "`:?`" operator to match on a type.

```fsharp
let detectType v =
    match v with
        | :? int -> printfn "this is an int"
        | _ -> printfn "something else"
// error FS0008: This runtime coercion or type test from type 'a to int    
// involves an indeterminate type based on information prior to this program point. 
// Runtime type tests are not allowed on some types. Further type annotations are needed.
```
_(Ошибка FS0008: )_

The message tells you the problem: "runtime type tests are not allowed on some types".  

The answer is to "box" the value which forces it into a reference type, and then you can type check it:

```fsharp
let detectTypeBoxed v =
    match box v with      // used "box v" 
        | :? int -> printfn "this is an int"
        | _ -> printfn "something else"

//test
detectTypeBoxed 1
detectTypeBoxed 3.14
```

<a id="FS0010a"></a>
## FS0010: Unexpected identifier in binding ##

Typically caused by breaking the "offside" rule for aligning expressions in a block.

```fsharp
//3456789
let f = 
  let x=1     // offside line is at column 3 
   x+1        // oops! don't start at column 4
              // error FS0010: Unexpected identifier in binding
```
_(Ошибка FS0010: )_
         
The fix is to align the code correctly!

See also [FS0588: Block following this 'let' is unfinished](#FS0588) for another issue caused by alignment.
 
<a id="FS0010b"></a>
## FS0010: Incomplete structured construct ##

Often occurs if you are missing parentheses from a class constructor:

```fsharp
type Something() =
   let field = ()

let x1 = new Something     // Error FS0010 
let x2 = new Something()   // OK!
```

Can also occur if you forgot to put parentheses around an operator:

```fsharp
// define new operator
let (|+) a = -a

|+ 1    // error FS0010: 
        // Unexpected infix operator

(|+) 1  // with parentheses -- OK!
```
_(Ошибка FS0010: )_

Can also occur if you are missing one side of an infix operator:

```fsharp
|| true  // error FS0010: Unexpected symbol '||'
false || true  // OK
```
_(Ошибка FS0010: )_

Can also occur if you attempt to send a namespace definition to F# interactive. The interactive console does not allow namespaces.

```fsharp
namespace Customer  // FS0010: Incomplete structured construct 

// declare a type
type Person= {First:string; Last:string}
```
_(Ошибка FS0010: )_

<a id="FS0013"></a>
## FS0013: The static coercion from type X to Y involves an indeterminate type ##

This is generally caused by implic

<a id="FS0020"></a>
## FS0020: This expression should have type 'unit' ##

This error is commonly found in two situations:

* Expressions that are not the last expression in the block
* Using wrong assignment operator

### FS0020 with expressions that are not the last expression in the block ###

Only the last expression in a block can return a value. All others must return unit. So this typically occurs when you have a function in a place that is not the last function. 

```fsharp
let something = 
  2+2               // => FS0020: This expression should have type 'unit'
  "hello"
```
_(Ошибка FS0020: )_

The easy fix is use `ignore`.  But ask yourself why you are using a function and then throwing away the answer -- it might be a bug.

```fsharp
let something = 
  2+2 |> ignore     // ok
  "hello"
```

This also occurs if you think you writing C# and you accidentally use semicolons to separate expressions:

```fsharp
// wrong
let result = 2+2; "hello";

// fixed
let result = 2+2 |> ignore; "hello";
```

### FS0020 with assignment ###

Another variant of this error occurs when assigning to a property.

    This expression should have type 'unit', but has type 'Y'. 

With this error, chances are you have confused the assignment operator "`<-`" for mutable values, with the equality comparison operator "`=`".

```fsharp
// '=' versus '<-'
let add() =
    let mutable x = 1
    x = x + 1          // warning FS0020
    printfn "%d" x    
```

The fix is to use the proper assignment operator.

```fsharp
// fixed
let add() =
    let mutable x = 1
    x <- x + 1
    printfn "%d" x
```

<a id="FS0030"></a>	
## FS0030: Value restriction ##

This is related to F#'s automatic generalization to generic types whenever possible. 

For example, given :

```fsharp
let id x = x
let compose f g x = g (f x)
let opt = None
```

F#'s type inference will cleverly figure out the generic types.

```fsharp
val id : 'a -> 'a
val compose : ('a -> 'b) -> ('b -> 'c) -> 'a -> 'c
val opt : 'a option
```

However in some cases, the F# compiler feels that the code is ambiguous, and, even though it looks like it is guessing the type correctly, it needs you to be more specific:

```fsharp
let idMap = List.map id             // error FS0030
let blankConcat = String.concat ""  // error FS0030
```

Almost always this will be caused by trying to define a partially applied function, and almost always, the easiest fix is to explicitly add the missing parameter: 

```fsharp
let idMap list = List.map id list             // OK
let blankConcat list = String.concat "" list  // OK
```

For more details see the MSDN article on ["automatic generalization"](http://msdn.microsoft.com/en-us/library/dd233183%28v=VS.100%29.aspx).

<a id="FS0035"></a>	
## FS0035: This construct is deprecated ##

F# syntax has been cleaned up over the last few years, so if you are using examples from an older F# book or webpage, you may run into this.  See the MSDN documentation for the correct syntax.

```fsharp
let x = 10
let rnd1 = System.Random x         // Good
let rnd2 = new System.Random(x)    // Good
let rnd3 = new System.Random x     // error FS0035
```

<a id="FS0039"></a>	
## FS0039: The field, constructor or member X is not defined ##

This error is commonly found in four situations:

* The obvious case where something really isn't defined! And make sure that you don't have a typo or case mismatch either.
* Interfaces
* Recursion
* Extension methods

### FS0039 with interfaces ###

In F# all interfaces are "explicit" implementations rather than "implicit". (Read the C# documentation on ["explicit interface implementation"](http://msdn.microsoft.com/en-us/library/aa288461%28v=vs.71%29.aspx) for an explanation of the difference). 

The key point is that when a interface member is explicitly implemented, it cannot be accessed through a normal class instance, but only through an instance of the interface, so you have to cast to the interface type by using the `:>` operator.

Here's an example of a class that implements an interface:

```fsharp
type MyResource() = 
   interface System.IDisposable with
       member this.Dispose() = printfn "disposed"
```

This doesn't work:

```fsharp
let x = new MyResource()
x.Dispose()  // error FS0039: The field, constructor 
             // or member 'Dispose' is not defined
```

The fix is to cast the object to the interface, as below:

```fsharp
// fixed by casting to System.IDisposable 
(x :> System.IDisposable).Dispose()   // OK

let y =  new MyResource() :> System.IDisposable 
y.Dispose()   // OK
```


### FS0039 with recursion ###

Here's a standard Fibonacci implementation: 

```fsharp
let fib i = 
   match i with
   | 1 -> 1
   | 2 -> 1
   | n -> fib(n-1) + fib(n-2)
```

Unfortunately, this will not compile: 

    Error FS0039: The value or constructor 'fib' is not defined

The reason is that when the compiler sees 'fib' in the body, it doesn't know about the function because it hasn't finished compiling it yet!

The fix is to use the "`rec`" keyword.

```fsharp
let rec fib i = 
   match i with
   | 1 -> 1
   | 2 -> 1
   | n -> fib(n-1) + fib(n-2)
```

Note that this only applies to "`let`" functions. Member functions do not need this, because the scope rules are slightly different.

```fsharp
type FibHelper() =
    member this.fib i = 
       match i with
       | 1 -> 1
       | 2 -> 1
       | n -> fib(n-1) + fib(n-2)
```

### FS0039 with extension methods ###

If you have defined an extension method, you won't be able to use it unless the module is in scope.

Here's a simple extension to demonstrate:

```fsharp
module IntExtensions = 
    type System.Int32 with
        member this.IsEven = this % 2 = 0
```

If you try to use it the extension, you get the FS0039 error:

```fsharp
let i = 2
let result = i.IsEven  
    // FS0039: The field, constructor or 
    // member 'IsEven' is not defined
```
    
The fix is just to open the `IntExtensions` module.
    
```fsharp
open IntExtensions // bring module into scope
let i = 2
let result = i.IsEven  // fixed!
```

<a id="FS0041"></a>	
## FS0041: A unique overload for could not be determined ##

This can be caused when calling a .NET library function that has multiple overloads:

```fsharp
let streamReader filename = new System.IO.StreamReader(filename) // FS0041
```

There a number of ways to fix this. One way is to use an explicit type annotation:

```fsharp
let streamReader filename = new System.IO.StreamReader(filename:string) // OK
```

You can sometimes use a named parameter to avoid the type annotation:

```fsharp
let streamReader filename = new System.IO.StreamReader(path=filename) // OK
```

Or you can try to create intermediate objects that help the type inference, again without needing type annotations:

```fsharp
let streamReader filename = 
    let fileInfo = System.IO.FileInfo(filename)
    new System.IO.StreamReader(fileInfo.FullName) // OK
```
	
<a id="FS0049"></a>	
## FS0049: Uppercase variable identifiers should not generally be used in patterns ##

When pattern matching, be aware of a subtle difference between the pure F# union types which consist of a tag only, and a .NET Enum type.

Pure F# union type:

```fsharp
type ColorUnion = Red | Yellow 
let redUnion = Red  

match redUnion with
| Red -> printfn "red"     // no problem
| _ -> printfn "something else" 
```

But with .NET enums you must fully qualify them:

```fsharp
type ColorEnum = Green=0 | Blue=1      // enum 
let blueEnum = ColorEnum.Blue  

match blueEnum with
| Blue -> printfn "blue"     // warning FS0049
| _ -> printfn "something else" 
```

The fixed version:

```fsharp
match blueEnum with
| ColorEnum.Blue -> printfn "blue" 
| _ -> printfn "something else"
```

<a id="FS0072"></a>	
## FS0072: Lookup on object of indeterminate type ##

This occurs when "dotting into" an object whose type is unknown.

Consider the following example:

```fsharp
let stringLength x = x.Length // Error FS0072
```

The compiler does not know what type "x" is, and therefore does not know if "`Length`" is a valid method. 

There a number of ways to fix this. The crudest way is to provide an explicit type annotation:

```fsharp
let stringLength (x:string) = x.Length  // OK
```

In some cases though, judicious rearrangement of the code can help. For example, the example below looks like it should work. It's obvious to a human that the `List.map` function is being applied to a list of strings, so why does `x.Length` cause an error?

```fsharp
List.map (fun x -> x.Length) ["hello"; "world"] // Error FS0072      
```

The reason is that the F# compiler is currently a one-pass compiler, and so type information present later in the program cannot be used if it hasn't been parsed yet. 

Yes, you can always explicitly annotate:

```fsharp
List.map (fun x:string -> x.Length) ["hello"; "world"] // OK
```

But another, more elegant way that will often fix the problem is to rearrange things so the known types come first, and the compiler can digest them before it moves to the next clause.

```fsharp
["hello"; "world"] |> List.map (fun x -> x.Length)   // OK
```

It's good practice to avoid explicit type annotations, so this approach is best, if it is feasible.

<a id="FS0588"></a>	
## FS0588: Block following this 'let' is unfinished ##

Caused by outdenting an expression in a block, and thus breaking the "offside rule".

```fsharp
//3456789
let f = 
  let x=1    // offside line is at column 3 
 x+1         // offside! You are ahead of the ball!
             // error FS0588: Block following this 
             // 'let' is unfinished
```

The fix is to align the code correctly.

See also [FS0010: Unexpected identifier in binding](#FS0010a) for another issue caused by alignment.