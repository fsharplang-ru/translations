+++
weight = 1

# Publication type.
publication_types = ["7"]

# Publication name and optional abbreviated version.
date = "2017-01-28"
title = "Глава 1. Вступление"
publication = "Учебник библиотеки FParsec"

# Does this page contain LaTeX math? (true/false)
math = false

# Does this page require source code highlighting? (true/false)
highlight = true

# Is this a selected publication? (true/false)
selected = false

# Links (optional)
url_source = "http://www.quanttec.com/fparsec/tutorial.html#preliminaries"
+++

FParsec состоит из двух библиотек: *FParsec.dll* и *FParsecCS.dll*. Для использования FParsec в вашем проекте, вы можете или установить из [nuget](http://nuget.org) (см. подробнее варианты установки [nuget-пакетов](http://www.quanttec.com/fparsec/download-and-installation.html#nuget-packages)<sup>en</sup>), или собрать два DLL файла FParsec из исходного кода. Самый простой способ собрать FParsec из исходного кода это использовать файлы решений Visual Studio в каталогах __=Build/VS__ [исходного кода пакета](https://github.com/stephan-tolksdorf/fparsec/archive/master.zip), например, в каталоге __Build/VS11__ для Visual Studio 2012. Любой проект, который использует библиотеку FParsec должен ссылаться на оба файла DLL. Смотри подробнее в руководстве пользователя главу [Загрузка и установка](http://www.quanttec.com/fparsec/download-and-installation.html)<sup>en</sup>.

Все типы и модули библиотеки FParsec объявлены в пространстве имен `FParsec`. Это пространство имен содержит несколько базовых классов (например, `CharStream` и `Reply`) и четыре F# модуля, а именно:
- [`Primitives`](http://www.quanttec.com/fparsec/reference/primitives.html), содержащий основные определения типов и комбинаторов синтаксического анализа,
- [`CharParsers`](http://www.quanttec.com/fparsec/reference/charparsers.html), содержащий синтаксические анализаторы для символов, строк, чисел и функций для применения синтаксических анализаторов для входных потоков,
- [`Error`](http://www.quanttec.com/fparsec/reference/primitives.html#members.Error), содержащий типы и вспомогательные функции для создания, обработки, форматирования сообщений об ошибках синтаксического анализа,
- [`StaticMapping`](http://www.quanttec.com/fparsec/reference/staticmapping.html), содержащий функции для создания оптимизированных функций отображения между ключами и значениями.

Во всех примерах в этом учебнике предполагается, что вы открыли пространство имен `FParsec`:

``` fsharp
open FParsec
```

При открытии пространства имен `FParsec` также автоматически открываются модули `Primitives`, `CharParsers` и `Error`.

{{% alert note %}}
Все примеры кода в этом учебнике содержатся в проекте [Samples/Tutorial](https://bitbucket.org/fparsec/main/src/c234349e7b738e09a1b9eb53f5f1ef77d584f09b/Samples/Tutorial/tutorial.fs?at=default). Читая учебник может быть весьма полезным держать этот проект открытым в окне редактора Visual Studio. Например, вы можете навести курсор мыши на идентификатор, чтобы получить всплывающее окно Intellisense с выведенным типом. А если вам интересно, как функция библиотеки реализована, вы можете открыть контекстное меню и выбрать *Перейти к определению (F12)*.
{{% /alert %}}