# Введение

Этот учебник познакомит вас с базовыми концепциями  [FParsec](https://github.com/stephan-tolksdorf/fparsec). Наша цель &mdash; дать вам представление о том, как создавать приложения синтаксического разбора с помощью этой библиотеки. Мы охватим только основные идеи и бегло рассмотрим [API](https://ru.wikipedia.org/wiki/API). Но, надеемся, что этого материала будет достаточно для того, чтобы вы могли в дальнейшем использовать FParsec самостоятельно с помощью: [руководства пользователя](http://www.quanttec.com/fparsec/users-guide/), [справочника по API](http://www.quanttec.com/fparsec/reference/) и примеров синтаксических анализаторов в папке [Samples](https://github.com/stephan-tolksdorf/fparsec/tree/master/Samples).

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