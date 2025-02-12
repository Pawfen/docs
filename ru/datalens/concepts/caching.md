---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Кеширование

_Кеш_ хранит в себе часто запрашиваемые данные и обеспечивает к ним быстрый доступ.

{{ datalens-short-name }} кеширует результаты запроса пользователя, поэтому при изменении данных в источнике дашборды и чарты могут обновляться не сразу.

Время жизни кеша зависит от режима работы датасета с источником данных:
* Датасеты с прямым доступом — 5 минут.
* Материализованные датасеты — 60 минут с автопродлением. 
Пример автопродления: после первого обращения пользователя данные записались в кеш на 60 минут. Через 50 минут при новом запросе данные будут взяты из кеша, а время жизни кеша станет 60 минут.



## Кеширование в {{ datalens-public }} {#caching-in-public}

Время жизни кеша зависит от режима работы датасета с источником данных:
* Датасеты с прямым доступом — 5 минут.
* Материализованные датасеты — 6 часов с автопродлением. 
Пример автопродления: после первого обращения пользователя данные записались в кеш на 6 часов. Через 5 часов 50 минут при новом запросе данные будут взяты из кеша, а время жизни кеша станет 6 часов.

## Условия сброса кеша {#reset-cache}

Кеш будет сброшен в следующих случаях:   
* Повторная материализация.
* Замена подключения.
* Обновление полей в датасете.