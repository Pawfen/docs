---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Массовое изменение статуса задач

Запрос позволяет выполнить переход в новый статус для нескольких задач одновременно. 

{% note info %}

Чтобы узнать, какие переходы доступны для задачи, выполните [запрос списка переходов](../issues/get-transitions.md). Для некоторых статусов (например, <q>Закрыт</q>) в параметре `value` тела запроса должна быть указана [резолюция]({{ link-admin-resolutions }}).

{% endnote %}

## Формат запроса {#query}

Перед выполнением запроса [получите доступ к API](../access.md).

Чтобы изменить статус задач, используйте HTTP-запрос с методом `POST`. Параметры запроса передаются в его теле в формате JSON.

```json
POST /{{ ver }}/bulkchange/_transition
Host: {{ host }}
Authorization: OAuth <OAuth-токен>
{{ org-id }}

{
  "transition": "start_progress",
  "issues": ["TEST-1","TEST-2","TEST-3"]
}
```

{% include [headings](../../../_includes/tracker/api/headings.md) %}

#### Параметры запроса {#req-params}

**Дополнительные параметры**

Параметр | Описание | Тип данных
-------- | -------- | ----------
notify | Признак уведомления об изменении задачи:<ul><li>`true` — пользователи, указанные в полях задачи, получат уведомления;</li><li>`false` — (по умолчанию) пользователи не получат уведомления.</li></ul> | Логический

#### Параметры тела запроса {#req-body-params}

**Обязательные параметры**

Параметр | Описание | Тип данных
-------- | -------- | ----------
transition | Идентификатор перехода. | Строка
issues | Идентификаторы задач, статус которых необходимо изменить. | Строка

**Дополнительные параметры**

Параметр | Описание | Тип данных
-------- | -------- | ----------
values | Параметры задач, которые будут изменены при смене статуса. Используйте параметры, доступные при [редактировании задачи](../issues/patch-issue.md#req-get-params). | Строка

> Пример: Изменить статус нескольких задач.
> 
> - Используется HTTP-метод POST.
>
> - Статус задач <q>TEST-1</q>, <q>TEST-2</q>, <q>TEST-3</q> меняется на <q>Закрыт</q> с резолюцией <q>Решен</q>.
> 
> ```
> POST /{{ ver }}/bulkchange/_transition
> Host: {{ host }}
> Authorization: OAuth <OAuth-токен>
> {{ org-id }}
> {
>   "transition": "close",
>   "issues": ["TEST-1", "TEST-2", "TEST-3"],
>   "values": {
>     "resolution": "fixed"
>   }
> }
> ```

## Формат ответа {#answer}

{% list tabs %}

- Запрос выполнен успешно

    В случае успешного выполнения запроса API возвращает ответ с кодом `201 Created`.

    Тело ответа содержит информацию об операции массового редактирования в формате JSON.

    ```json
    {
        "id": "1ab23cd4e56789012fg345h6",
        "self": "{{ host }}/v2/bulkchange/1ab23cd4e56789012fg345h6",
        "createdBy": {
            "self": "{{ host }}/v2/users/1234567890",
            "id": "1234567890",
            "display": "Имя Фамилия"
        },
        "createdAt": "2020-12-15T11:52:53.665+0000",
        "status": "CREATED",
        "statusText": "Операция массового редактирования задач создана.",
        "executionChunkPercent": 0,
        "executionIssuePercent": 0
    }
    ```

    #### Параметры ответа {#answer-params}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    id | Идентификатор операции массового редактирования. | Строка
    self | Адрес ресурса API, который содержит информацию о массовом редактировании. | Строка
    [createdBy](#createdBy) | Объект с информацией об инициаторе массового редактирования. | Объект
    createdAt | Дата и время создания операции массового редактирования. | Строка
    status | Статус операции массового редактирования. | Строка
    statusText | Описание статуса операции массового редактирования. | Строка
    executionChunkPercent | Служебный параметр. | Число
    executionIssuePercent | Служебный параметр. | Число

    **Поля объекта** `createdBy` {#createdBy}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    self | Адрес ресурса API, который содержит информацию о пользователе. | Строка
    id | Идентификатор пользователя. | Число
    display | Отображаемое имя пользователя. | Строка

- Запрос выполнен с ошибкой

    Если запрос не был успешно обработан, API возвращает ответ с кодом ошибки:

    400
    :   Один или несколько параметров запроса имеют недопустимое значение.

    401
    :   Пользователь не авторизован. Проверьте, были ли выполнены действия, описанные в разделе [{#T}](../access.md).

    403
    :   У вас не хватает прав на выполнение этого действия. Наличие прав можно перепроверить в интерфейсе {{ tracker-name }} — для выполнения действия при помощи API и через интерфейс требуются одинаковые права.

    422
    :   Ошибка валидации. Возможно, тело запроса содержит недопустимый или несуществующий параметр.

{% endlist %}

