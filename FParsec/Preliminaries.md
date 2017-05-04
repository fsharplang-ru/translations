# Вступление

`FParsec` собран как две DLL: `FParsec.dll` и `FParsecCS.dll`. Для использования `FParsec` в своем проекте, вы можете либо установить один из `NuGet` пакетов либо собрать `FParsec` из исходников.
Простейший способ собрать `FParsec` из исходников это использовать файл-решение `VS` в папке __=Build/VS__ из [архива исходного кода](https://github.com/stephan-tolksdorf/fparsec/archive/master.zip). Например, __Build/VS11__ для Visual Studio 2012.
Каждый проект, который использует `FParsec` должен ссылаться на обе DLL. Смотрите [download and installation](http://www.quanttec.com/fparsec/download-and-installation.html) для того, чтобы узнать больше подробностей.

Все типы и модули `FParsec` объявлены в пространстве имен `FParsec`. Это пространство имен содержит несколько базовых классов (таких как `CharStream` и `Reply`) и четыре модуля, а именно:

* [`Primitives`](http://www.quanttec.com/fparsec/reference/primitives.html) - содержит определение основных типов и парсер-комбинаторов.
* [`CharParsers`](http://www.quanttec.com/fparsec/reference/charparsers.html) - содержит парсеры для символов (`char`), строк (`string`), чисел и функций для применения парсеров к входному потоку.
* [`Error`](http://www.quanttec.com/fparsec/reference/primitives.html#members.Error) - содержит типы и вспомогательные функции для создания, обработки и форматирования сообщений об ошибках.
* [`StaticMapping`](http://www.quanttec.com/fparsec/reference/staticmapping.html) - содержит функции для компилирования статических преобразований ключ-значение в оптимизированные функции.


Во всех сниппетах кода из этого туториала предполагается, что вы открыли пространство имен `FParsec`:

``` fsharp 
open FParsec
```

Открытие пространства имен `FParsec` также автоматически откроет модули `Primitives`, `CharParsers` и `Error`.

**_Заметка_**

> Весь код используемый в туториале находится в папке [Tutorial](https://github.com/stephan-tolksdorf/fparsec/tree/master/Samples/Tutorial). Открытие проекта во время чтения туториала может быть довольно полезным. 
> Например, вы можете навести мышь над идентификатором, чтобы получить подсказку от Intellisense с выведенным типом. Если вам будет любопытно как реализована функция в библиотеке, вы сможете кликнуть по варианту `Перейти к определению` из контекстного меню для просмотра исходного кода.