# Тонкости value restriction в F#
Одной из отличительных особенностей языка F#, по сравнению с более распространёнными языками программирования, является мощный и всеобъемлющий автоматический вывод типов. Благодаря ему в программах на F# вы почти никогда не указываете типы явно, набираете меньше текста, и получаете в итоге более краткий, фантастически элегантный код.

Алгоритмы автоматического вывода типов - захватывающая тема, за ними стоит интересная и красивая теория. Сегодня мы рассмотрим один интересный аспект автоматического вывода типов в F#, который, возможно, даст вам представление о том, какие сложности возникают в хороших современных алгоритмах такого рода, и, надеюсь, объяснит один камень преткновения, с которым время от времени сталкиваются F# программисты.

Нашей темой сегодня будет *ограничение на значения (value restriction)*. На MSDN есть [хорошая статья]("http://msdn.microsoft.com/en-us/library/dd233183(v=VS.100).aspx") на тему ограничения на значения и автоматического обобщения типов в  F#, которая даёт очень разумные практические советы, что делать, если вы столкнулись с этим в вашем коде. Однако, эта статья просто как ни в чём не бывало констатирует: "*Компилятор выполняет автоматическое обобщение только на полных определениях функций с явно указанными аргументами и на простых неизменяемых значениях*", и не даёт этому практически никаких обоснований, что вполне справедливо, потому что MSDN - это просто справочный материал. Мой пост сфокусирован на рассуждениях, лежащих в основе ограничения на значения - я буду отвечать на вопрос "почему?", а не "что делать?".

*Автоматическое обобщение (automatic generalization)* - мощная возможность автоматического вывода типов F#. Определим простую функцию, например функцию тождества: 

    let id x = x

Здесь нет явных аннотаций типов (type annotations), но компилятор F# выводит для этой функции тип 'a -> 'a - функция принимает аргумент некоего типа и возвращает результат точно такого же типа. Это не особенно сложно, но заметьте, что компилятор F# вывел неявный тип-параметр (generic type parameter) 'a для функции id.

Мы можем скомбинировать функцию id с функцией List.map, которая сама по себе полиморфна (generic function):

    let listId l = List.map id l
(не очень полезная функция, но полезный код - это не сегодняшняя моя цель). Компилятор F#  даёт функции listId корректный тип 'a list -> 'a list; снова произошло автоматическое обобщение. Но поскольку List.map - каррированная функция, у нас может возникнуть искушение отбросить аргумент l слева и справа:

    let listId = List.map id
Но F# компилятор неожиданно возмущается:

    Program.fs(8,5): error FS0030: Value restriction. 
    The value 'listId' has been inferred to have generic type
         val listId : ('_a list -> '_a list)
    Either make the arguments to 'listId' explicit or,
    if you do not intend for it to be generic, add a type annotation.

Что происходит?

Статья на MSDN предлагает 4 способа исправить let-определение, которое отвергается компилятором из-за ограничения на значения:
* Ограничить тип так, чтобы он перестал быть обобщённым, добавив явную аннотацию типа к значению или параметру.
* Если проблема заключается в необобщаемой конструкции (nongeneralizable construct) в определении полиморфной функции (такой, как композиция функций или неполное применение аргументов к каррированной функции), попробуйте переписать определение функции на обыкновеное
* Если проблема заключается в выражении, слишком сложном для обобщения, превратите его в функцию, добавив неиспользуемый параметр.
* Добавьте явные полиморфные типы-параметры. Это способ используется редко.

Первый способ не для нас - мы хотим, чтобы функция listId была полиморфной.

Второй способ вернёт нас к явному указанию параметра list - и это канонический способ определения функций вроде listId в языке F#.

Способ 3 применим, когда нужно определить что-то, не являющееся функцией, в нашем случае это даёт

    let listId () = List.map id

что неубедительно.

В рабочем коде я бы поспользовался вторым способом и оставил параметр функции явным. Но ради обучения давайте попробуем "редко используемый" четвёртый способ:

    let listId<'T> : 'T list -> 'T list = List.map id

Это компилируется и работает так, как и ожидалось. На первый взгляд кажется, что это ошибка вывода типов - компилятор не может определить тип, поэтому мы добавили аннотацию, чтобы ему помочь. Но подождите, компилятор почти вывел этот тип - он же упоминается в сообщении об ошибке! (С таинственной переменной типа (type variable) '_a') Будто бы компилятор был ошарашен конкретно этим случаем - почему?

По очень разумной причине. Чтобы увидеть её, давайте рассмотрим другой случай ограничения на значения. Эта ссылочная ячейка (reference cell) на список не скомпилируется:

    let v = ref []
    Program.fs(16,5): error FS0030: Value restriction. 
    The value 'v' has been inferred to have generic type
        val v : '_a list ref    
    Either define 'v' as a simple data term, make it a function with explicit arguments or, 
    if you do not intend for it to be generic, add a type annotation.

Давайте обойдём это с помощью явных аннотаций типов:

    >let v<'T> : 'T list ref = ref []
    val v<'T> : 'T list ref

Компилятор доволен. Давайте попробуем присвоить v какое-нибудь значение:

    > v := [1];;
    val it : unit = ()
Правда же, v теперь ссылается на список с единственным элементом 1?

    > let x : int list = !v;;
    val x : int list = []
Упс! Содержимое v - пустой список! Куда делся наш [1]?

Вот что произошло. Наше присваивание на самом деле может быть переписано так:

    (v<int>):=[1]

Левая сторона этого присваивания - это применение v к аргументу типа (type argument) int. А v, в свою очередь, это не ссылочкая ячейка, а функция типа: получив на входе аргумент типа, она вернёт ссылочную ячейку. Наше выражение создаёт новую ссылочную ячейку и присваивает ей "[1]". Аналогично, если мы явно укажем аргумент типа в разыменовании v:

    let x = !(v<int>)

мы увидим, что v снова применяется к аргументу типа, и возвращает свежую ссылочную ячейку, содержащую пустой список.

Чтобы конкретизировать разговор о функциях типа, давайте изучим полученный IL. Если мы скомпилируем определение v, наш верный Reflector покажет нам, что v это:

    public static FSharpRef<FSharpList<T>> v<T>(){
           return Operators.Ref<FSharpList<T>>(FSharpList<T>.get_Empty());
    }

What we perceived as a single value in F# is actually a generic method without parameters in the underlying IL. Both assignment and dereference of v will call the IL method, and this will indeed produce a fresh reference cell every time.

However, nothing in

    let v = ref []

suggests that the above behavior. v looks an ordinary value, and not a method or function at all. If this definition was allowed, F# developers will be in for a dangerous surprise later. That is why compiler stops inferring generic parameters here – value restriction saves you from unexpected behavior.

So when it is safe to automatically generalize? It is hard to argue precisely, but one simple answer suggests itself: generalization is safe if the expression on right-hand side of “let” both:

is side-effect free (also known as pure)
produces an immutable object
Indeed, the weird behavior of v stems from mutability of a reference cell; it is because the reference cell was mutable we cared whether we get back same or different cell from different accesses to v. If the right-hand-side of “let” is side-effect-free we know that the object we are getting is the equivalent object; since it is an immutable object, we do not care if we get a single copy or multiple copies of it between different invocations.

The two conditions above are hard – impossible - for the compiler to establish precisely. So F# compiler follows a simple and crude, but a very natural and understandable approximation: it only generalizes if it can infer purity and immutability from a syntactic structure of an expression on right-hand-side of a let. That is why:

    let listId = fun l -> List.map id

(which is what our original definition “let listId l = List.map id l” a syntactic sugar for) generalizes – right-hand-side is a closure creation; closure creations are side-effect-free and closures are immutable.

Same for discriminated unions:

    let q = None
    let z = []

and immutable records:

    type 'a r = { x : 'a; y : int }
    let r1 = { x = []; y = 1 }

r1 gets a type 'a list r. However if you try to initialize some of the fields of immutable record with a function call:

    let gen =
        let u = ref 0
        fun () -> u := !u + 1; !u
    let f = { x = []; y = gen() }
f will not be generalized. In the above case gen is indeed a non-pure function; it might have been pure however, but compiler has no way of knowing, so it errs on a side of caution. For the same reason,

    let listId = List.map id
is not generalized – compiler does not know whether List.map is pure or not.

The expressions that are syntactically known to a compiler to be pure and produce immutable objects are called syntactic values. That is how value restriction got its name – automatic generalization on a right-hand-side of let definition is restricted to syntactic values. F# language specification gives a definition of a full set of syntactic values, but the above discussion gives you a good idea of what that set is – pure and producing immutable objects.

The problem we are solving here is not new – all compilers of languages belonging to ML family have been using value restriction in one form or another. One feature of F# that I think is unique however is that F# allows value restriction to be suppressed by explicit type annotation, and it is actually safe in F# semantics to do so.

When it can be useful? The canonical example is lazy and lazy list. Typical definition of lazy (let us pretend we do not have it in the language):

    type 'a LazyInner = Delayed of (unit -> 'a) | Value of 'a | Exception of exn
    type 'a Lazy = 'a LazyInner ref
    let create f = ref (Delayed f)
    let force (l : 'a Lazy) = ...
is, on the face of it, full of side-effects; compiler does not know of a contract that exists between create and force. If we build a lazy list upon this definition of lazy in a standard way

    type 'a cell = Nil | CCons of 'a * 'a lazylist
    and 'a lazylist = 'a cell Lazy

and try to define an empty lazy list:

    let empty = create (fun () -> Nil)

value restriction won’t let us; however generic uses of an empty lazy list is perfectly legitimate; we can declare this fact by making generic type parameter explicit:

    let empty<'T> : 'T lazylist = create (fun () -> Nil)
This is enough to get the definition of empty to compile; however if we try to use it

    let l = empty
compiler will complain again:

    File1.fs(12,5): error FS0030: Value restriction. 
    The value 'l' has been inferred to have generic type
        val l : '_a lazylist    
Either define 'l' as a simple data term, 
make it a function with explicit arguments or, 
if you do not intend for it to be generic, add a type annotation.
Indeed, compiler knows that empty is a type function that not automatically generalizable – therefore it is not in a set of syntactic values. F# provides an escape hatch here though – we can put a [<GeneralizableValue>] attribute on a definition of empty:

    [<GeneralizableValue>]
    let empty<'T> : 'T lazylist = create (fun () -> Nil)
    This instructs the compiler to treat empty as a syntactic value, and “let l = empty” will compile

As a matter of fact, you might occasionally found the likes of our generic v useful:

    let v<'T> : 'T list ref = ref []

If you end up writing a type-parametrized function returning mutable objects or having side effects, please do sprinkle a RequiresExplicitTypeArguments attribute over it:

    [<RequiresExplicitTypeArguments>]
    let v<'T> : 'T list ref = ref []
It does what it says on tin: now you cannot write “v := [1]”, only “v<int> := [1]”, and it is (somewhat) clearer what goes on.

If you got this far, I hope you now have a very solid understanding of F# value restriction, and you gained the power to control it if needed by means of explicit type annotations and GeneralizableValue attribute. With power comes responsibility however; MSDN article is quite right - those powers are rarely used in day-to-day F# programming. In my F# code, type functions appear only in cases similar to empty lazy lists from above – ground cases of data structures; in all other cases, I follow the MSDN article advice:

* Constrain a type to be nongeneric by adding an explicit type annotation to a value or parameter.
* If the problem is using a nongeneralizable construct to define a generic function, such as a function composition or incompletely applied curried function arguments, try to rewrite the function as an ordinary function definition.
* If the problem is an expression that is too complex to be generalized, make it into a function by adding an extra, unused parameter.
