# Домашняя страница: Write-up

Видим домашнюю страницу Пала Эрдёша. На ней ничего особо интересного — данные соответствуют Википедии. В условии таска сказано, что у этой страницы много языковых версий, но никаких ссылок для переключения на сайте нет.

На самом деле, сервер может определить, на каком языке говорит клиент, по заголовкам запроса — а именно, по заголовку `Accept-Language`, в котором браузер передаёт предпочтительные языки для показа страницы. Значение этого заголовка имеет вид `ru-RU,en-US;q=0.9,en;q=0.8,*;q=0.7`. Данное правило означает, что максимальное предпочтение отдаётся русскому языку, чуть менее предпочтителен (с приоритетом 0,9) — американский английский, затем любой английский, а в случае, если никакой из этих языков недоступен, можно вернуть любой. Это значение формируется браузером на основе установленной у вас локали в операционной системе, языка браузера и других параметров. Сервер не обязан следовать этим правилам, но многие веб-сервера используют этот заголовок для выдачи нужной языковой версии.

Проверим то, что этот заголовок влияет на ответ, сделав запросы консольной утилитой `curl` с русским языком и с английским:

```
$ curl -H "Accept-Language: ru-RU" https://homepage.q.2020.ugractf.ru/8f461a9980b89428/
$ curl -H "Accept-Language: en-US" https://homepage.q.2020.ugractf.ru/8f461a9980b89428/
```

Действительно, первый ответ отдаётся на русском языке, а второй — на английском. Но ни в одном из них флага нет. Где же он? В условии сказано, что страница — личная, а флаг находится лишь в одной языковой версии. Логично предположить, что нам нужна венгерская версия сайта, поскольку наш герой именно оттуда. Код Венгрии — `hu`. В соответствующей языковой версии видим специальный раздел с флагом.

Флаг: **ugra_what_is_your_erdos_number_58ba5159b34c**
