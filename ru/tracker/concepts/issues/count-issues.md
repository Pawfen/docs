---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Узнать количество задач

Запрос позволяет узнать количество задач, удовлетворяющих условиям вашего запроса.

## Формат запроса {#section_rnm_x4j_p1b}

Для получения числа задач, удовлетворяющих определенным критериям, используйте HTTP-запрос с методом `POST`. Критерии для поиска передаются в теле запроса в формате JSON:

```json
POST /v2/issues/_count
Host: {{ host }}
Authorization: OAuth <OAuth-токен>
{{ org-id }}

{
"filter": {
    "<имя поля>": "<значение в поле>"
  },
  "query": "фильтр на языке запросов"
}
```

{% include [headings](../../../_includes/tracker/api/headings.md) %}

#### Параметры, передаваемые в теле запроса {#req-body-params}

Параметр | Описание | Формат
----- | ----- | -----
filter | Параметры фильтрации задач. В параметре можно указать название любого поля и значение, по которому будет производиться фильтрация. | Объект
query | Фильтр на [языке запросов](../../user/query-filter.md). | Строка

> Запрос количества задач с указанием дополнительных параметров фильтрации:
> 
> - Используется HTTP-метод POST.
> - Ответ должен содержать только количество задач из очереди <q>JUNE</q>, в которых не указан исполнитель.
> 
> ```
> POST /v2/issues/_count HTTP/1.1
> Host: {{ host }}
> Authorization: OAuth <OAuth-токен>
> {{ org-id }}
> Cache-Control: no-cache
> 
> {
>   "filter": {
>     "queue": "JUNE",
>     "assignee": "empty()"
>   }
> }'
> ```

## Формат ответа {#section_xc3_53j_p1b}

В ответе содержится число задач, удовлетворяющих условиям вашего запроса.

```
5221186
```

## Возможные коды ответа {#section_otf_jrj_p1b}

200
:   Запрос выполнен успешно.

