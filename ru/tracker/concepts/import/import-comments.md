---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Импортировать комментарии

{% note warning %}

Запрос может быть выполнен только если у пользователя есть право на изменение задачи, в которую импортируется комментарий.

{% endnote %}

С помощью запроса вы можете импортировать в {{ tracker-name }} комментарии к задаче.

## Формат запроса {#section_i14_lyb_p1b}

Чтобы импортировать комментарий, используйте HTTP-запрос с методом `POST`. Параметры комментария передаются в теле запроса в формате JSON:

```json
POST /{{ ver }}/issues/<issue_id>/comments/_import
Host: {{ host }}
Authorization: OAuth <токен>
{{ org-id }}
Content-Type: application/json

{
  "text": "Test",
  "createdAt": "2017-08-29T12:34:41.740+0000",
  "createdBy": 1110000000011111,
  "updatedAt": "2017-09-07T11:24:31.140+0000",
  "updatedBy": 1110000000011111
}

```

{% include [headings](../../../_includes/tracker/api/headings.md) %}

#### Ресурс

- **<issue_id>**

    Ключ задачи, к которой будет прикреплен комментарий.


- **Content-Type**

    Формат тела запроса. Должен иметь значение `application/json`.

#### Тело запроса

Тело запроса содержит параметры комментария:

Параметр | Описание | Тип данных
-------- | -------- | ----------
text | Текст комментария, не более 512000 символов. | Строка
createdAt | Дата и время создания комментария в формате `YYYY-MM-DDThh:mm:ss.sss±hhmm`. Вы можете указать любое значение в интервале времени от создания до последнего обновления задачи. | Строка
createdBy | Логин или идентификатор автора комментария. | <ul><li>Строка для логина.</li><li>Число для идентификатора.</li></ul>
updatedAt | Дата и время последнего изменения комментария в формате `YYYY-MM-DDThh:mm:ss.sss±hhmm`. Вы можете указать любое значение в интервале времени от создания до последнего обновления задачи.<br/><br/>Параметр указывается только вместе с параметром `updatedBy`. | Строка
updatedBy | Логин или идентификатор пользователя, который редактировал комментарий последним.<br/><br/>Параметр указывается только вместе с параметром `updatedAt`. | <ul><li>Строка для логина.</li><li>Число для идентификатора.</li></ul>

## Формат ответа {#section_isd_myb_p1b}

{% list tabs %}

- Запрос выполнен успешно

    В случае успешного выполнения запроса API возвращает ответ с кодом 201. Тело запроса содержит информацию о импортированной задаче в формате JSON.

    ```json
    {
            "self": "{{ host }}/v2/issues/JUNE-2/comments/9849018",
            "id": 9849018,
            "longId" : "5fa15a24ac894475dd14ff07",
            "text": "Комментарий",
            "createdBy": {
                "self": "{{ host }}/v2/users/1120000000049224",
                "id": "<id сотрудника>",
                "display": "<отображаемое имя сотрудника>"
            },
            "updatedBy": {
                "self": "{{ host }}/v2/users/1120000000049224",
                "id": "<id сотрудника>",
                "display": "<отображаемое имя сотрудника>"
            },
            "createdAt": "2017-06-11T05:11:12.347+0000",
            "updatedAt": "2017-06-11T05:11:12.347+0000",
            "version": 1,
            "type" : "standard",
            "transport" : "internal"   
    }
    ```

    #### Параметры ответа

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    self | Ссылка на объект комментария | Строка
    id | Идентификатор комментария | Число
    longId | Идентификатор комментария в виде строки | Строка
    text | Текст комментария. | Строка
    [createdBy](#createdBy) | Объект с информацией о создателе комментария. | Объект
    [updatedBy](#updatedBy) | Объект с информацией о сотруднике, внесшем последнее изменение в комментарий. | Объект
    createdAt | Дата и время создания комментария в формате:<br/>``` YYYY-MM-DDThh:mm:ss.sss±hhmm ``` | Строка
    updatedAt | Дата и время обновления комментария.<br/>``` YYYY-MM-DDThh:mm:ss.sss±hhmm ``` | Строка
    version | Версия комментария. Каждое изменение комментария увеличивает номер версии. | Число
    type | Тип комментария:<ul><li>**standart** — отправлен через интерфейс {{ tracker-name }};</li><li>**incoming** — создан из входящего письма;</li><li>**outcoming** — создан из исходящего письма.</li></ul> | Строка
    transport | Способ добавления комментария:<ul><li>**internal** — через интерфейс {{ tracker-name }};</li><li>**email** — через письмо.</li></ul> | Строка

    **Поля объекта** `createdBy` {#createdBy}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    self | Ссылка на пользователя. | Строка
    id | Идентификатор пользователя. | Строка
    display | Отображаемое имя пользователя. | Строка

    **Поля объекта** `updatedBy` {#updatedBy}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    self | Ссылка на пользователя. | Строка
    id | Идентификатор пользователя. | Строка
    display | Отображаемое имя пользователя. | Строка

- Запрос выполнен с ошибкой

    Если запрос не был успешно обработан, ответное сообщение содержит информацию о возникших ошибках:

    HTTP-код ошибки | Описание ошибки
    --------------- | ---------------
    400 Bad Request | Один из параметров запроса имеет недопустимое значение или формат данных.
    403 Forbidden | У пользователя или приложения нет прав на доступ к ресурсу, запрос отклонен.
    404 Not Found | Запрашиваемый ресурс не найден.
    422 Unprocessable Entity | Ошибка валидации JSON, запрос отклонен.
    500 Internal Server Error | Внутренняя ошибка сервиса. Попробуйте повторно отправить запрос через некоторое время.
    503 Service Unavailable | Сервис API временно недоступен.

