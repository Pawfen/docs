---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Метод abortUpload

Прерывает загрузку и удаляет все уже сохраненные части объекта из {{ objstorage-name }}. Если запрос за на прерывание загрузки поступил в процессе загрузки какой-либо из частей, то результат не гарантирован.

Рекомендуем после прерывания загрузки [получить список частей](listparts.md), и если он не пуст, то следует еще раз отправить запрос на прерывание загрузки. Запросы на прерывание следует отправлять до тех пор, пока список частей не станет пустым.


## Запрос {#request}

```
DELETE /{bucket}/{key}?uploadId=UploadId HTTP/2
```

### Path параметры {#path-parameters}

Параметр | Описание
----- | -----
`bucket` | Имя бакета.
`key` | Ключ объекта.


### Query параметры {#request-parameters}

Параметр | Описание
----- | -----
`uploadId` | Идентификатор составной загрузки, который {{ objstorage-name }} вернул при [инициализации](startupload.md).


### Заголовки {#request-headers}

Используйте в запросе необходимые [общие заголовки](../common-request-headers.md).


## Ответ {#response}

### Заголовки {#response-headers}

Ответ может содержать только [общие заголовки](../common-response-headers.md).

### Коды ответов {#response-codes}

Перечень возможных ответов смотрите в разделе [{#T}](../response-codes.md).

Дополнительно, {{ objstorage-name }} может вернуть ошибки, описанные в таблице ниже.

Ошибка | Описание | HTTP-код
----- | ----- | -----
`NoSuchUpload` | Указанная загрузка не существует. Возможно указан неверный идентификатор загрузки или загрузка была завершена или удалена. | 404 Not Found


