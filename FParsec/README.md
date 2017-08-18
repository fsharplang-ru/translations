+++
# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Стефан Тольксдорф","Дмитрий Власов"]

# Publication type.
publication_types = ["7", "3"]

# Publication name and optional abbreviated version.
date = "2017-01-28"
title = "Учебник библиотеки FParsec"
publication = "Перевод учебника библиотеки FParsec"
abstract = "Этот учебник знакомит вас с основными понятиями библиотеки FParsec. Цель этого учебника это дать вам возможность попробовать создать приложения синтаксического разбора с помощью библиотеки FParsec. Что будет достаточной основой для того, чтобы вы могли в дальнейшем использовать FParsec самостоятельно с помощью руководства пользователя, справочника по API и примеров синтаксических анализаторов."

# Does this page contain LaTeX math? (true/false)
math = false

# Does this page require source code highlighting? (true/false)
highlight = true

# Is this a selected publication? (true/false)
selected = true

# Links (optional)
url_code = "https://github.com/stephan-tolksdorf/fparsec"
url_dataset = "https://bitbucket.org/fparsec/main/src/c234349e7b738e09a1b9eb53f5f1ef77d584f09b/Samples/?at=default"
url_source = "http://www.quanttec.com/fparsec/tutorial.html"
+++

# Введение

Этот учебник знакомит вас с основными понятиями библиотеки FParsec. Наша цель &mdash; дать вам возможность попробовать создать приложения синтаксического разбора с помощью библиотеки FParsec. Мы охватим только основные идеи и дадим беглый обзор библиотеки по [API](https://ru.wikipedia.org/wiki/API)<sup>en</sup>. Но, надеемся, это будет достаточной основой для того, чтобы вы могли в дальнейшем использовать FParsec самостоятельно с помощью: [руководства пользователя](http://www.quanttec.com/fparsec/users-guide/)<sup>en</sup>, [справочника по API](http://www.quanttec.com/fparsec/reference/)<sup>en</sup> и примеров синтаксических анализаторов в папке [Samples](https://bitbucket.org/fparsec/main/src/c234349e7b738e09a1b9eb53f5f1ef77d584f09b/Samples/?at=default)<sup>en</sup>.

# Оглавление
1. [Вступление](01-preliminaries) 
1. [Синтаксический анализатор числа с плавающей точкой](02-parsing-a-single-float)
1. [Синтаксический анализатор числа с плавающей точкой в скобках](03-parsing-a-float-between-brackets)
1. [Абстрактные синтаксические анализаторы](04-abstracting-parsers)
1. [Синтаксический анализатор списка чисел с плавающей точкой](05-parsing-a-list-of-floats)
1. [Обработка пробелов](06-handling-whitespace)
1. [Синтаксический анализатор строковых данных](07-parsing-string-data)
1. [Использование последовательности синтаксических анализаторов](08-sequentially-applying-parsers)
1. [Использование альтернативных синтаксических анализов](09-parsing-alternatives)
1. [Ограничение значений F#](10-fsharps-value-restriction)
1. [Синтаксический анализ JSON](11-parsing-json) (В работе)
1. [Куда дальше?](12-what-now) (В работе)

# Учебник на других языках
- [Стефан Тольксдорф](https://github.com/stephan-tolksdorf), авторский текст на [английском языке](http://www.quanttec.com/fparsec/tutorial.html).
* [Gab_km](https://twitter.com/gab_km), перевод на [японский язык](http://blog.livedoor.jp/gab_km/archives/1437534.html).
  