---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Обработка ошибок

Поток стандартного вывода ошибок `stderr` скрипта отправляется в [централизованную систему журналирования](logging.md).

Если скрипт завершен с кодом, отличным от 0, — значит произошла ошибка его исполнения. Тогда содержимое стандартного потока вывода будет перенаправлено в журнал, а результатом функции будет ошибка:

```json
{
    "errorType": "BashScriptExecutionError"
}
```

Если ответ функции превышает 1 MБ, вернется ошибка:

```json
{
    "errorType": "BashScriptReadOutputError"
}
``` 