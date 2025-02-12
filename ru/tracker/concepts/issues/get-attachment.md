---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Скачать файл

Запрос позволяет скачать прикрепленный к задаче файл.

## Формат запроса {#section_rnm_x4j_p1b}

Чтобы скачать файл, используйте HTTP-запрос с методом `GET`:

```
GET /{{ ver }}/issues/<issue-id>/attachments/<attachment-id>/<filename>
Host: {{ host }}
Authorization: OAuth <OAuth-токен>
{{ org-id }}
```
{% include [headings](../../../_includes/tracker/api/headings.md) %}

#### Ресурс {#resource}

- **\<issue-id\>**

    Идентификатор или ключ задачи.

- **\<attachment-id\>**

    Уникальный идентификатор файла.

- **\<filename\>**

    Имя файла.

> Запрос на скачивание файла, прикрепленного к задаче `JUNE-2`:
> 
> - Используется HTTP-метод GET.
> 
> 
> ```
> GET /v2/issues/JUNE-2/attachments/4159/attachment.txt HTTP/1.1
> Host: {{ host }}
> Authorization: OAuth <OAuth-токен>
> {{ org-id }}
> ```

## Формат ответа {#section_xc3_53j_p1b}

В случае успешного выполнения запроса API возвращает ответ с кодом 200.

## Возможные коды ответа {#section_otf_jrj_p1b}

200
:   Запрос выполнен успешно.

404
:   Запрошенный объект не был найден. Возможно, вы указали неверное значение идентификатора или ключа объекта.

