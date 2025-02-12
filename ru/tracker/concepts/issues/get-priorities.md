---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Получить приоритеты

Запрос позволяет получить список приоритетов для задачи.

## Формат запроса {#section_rnm_x4j_p1b}

Для получения списка приоритетов используйте HTTP-запрос с методом `GET`:

```json
GET /v2/priorities?
localized=<true/false>
Host: {{ host }}
Authorization: OAuth <OAuth-токен>
{{ org-id }}
```

{% include [headings](../../../_includes/tracker/api/headings.md) %}

#### Параметры запроса {#req-get-params}

- **localized**

    Признак наличия переводов в ответе. Допустимые значения:

    - `true` — В ответе содержатся описания приоритетов только на языке пользователя. Значение по умолчанию.
    - `false` — В ответе содержатся описания приоритетов на всех языках.

> Запрос приоритетов:
> 
> - Используется HTTP-метод GET.
> 
> ```
> GET /v2/priorities HTTP/1.1
> Host: {{ host }}
> Authorization: OAuth <OAuth-токен>
> {{ org-id }}
> Cache-Control: no-cache
> ```

## Формат ответа {#section_xc3_53j_p1b}

```json
[
    {
        "self": "{{ host }}/v2/priorities/5",
        "id": 5,
        "key": "blocker",
        "version": 1341632717561,
        "name": "Блокер",
        "order": 5
    },
    ...
]
```

#### Параметры ответа {#answer-params}

Параметр | Описание | Тип данных
----- | ----- | -----
self | Ссылка на объект. | Строка
id | Идентификатор приоритета. | Число
key | Ключ приоритета. | Строка
version | Версия приоритета. | Число
name | Отображаемое название приоритета. При переданном в запросе `localized=false` данный параметр будет содержать дубликаты названия на других языках. | Строка
order | Вес приоритета. Параметр влияет на порядок отображения приоритета в интерфейсе. | Число

## Возможные коды ответа {#section_otf_jrj_p1b}

200
:   Запрос выполнен успешно.

