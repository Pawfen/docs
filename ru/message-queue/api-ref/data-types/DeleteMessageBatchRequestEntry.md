---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# DeleteMessageBatchRequestEntry

Параметр `ReceiptHandle` и его идентификатор.

Параметр | Тип | Обязательный параметр | Описание
----- | ----- | ----- | -----
`Id` | **string** | Да | Идентификатор для `ReceiptHandle`. Параметр должен быть уникален в пределах одного запроса.
`ReceiptHandle` | **string** | Да | Идентификатор получения сообщения.