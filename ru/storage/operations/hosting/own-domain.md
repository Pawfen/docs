---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Собственный домен

Для публикации сайта вы можете использовать собственный домен, например, `www.example.com`.

По умолчанию сайт доступен только по протоколу HTTP. Чтобы поддержать для сайта протокол HTTPS, [загрузите собственный сертификат безопасности](certificate.md) в {{ objstorage-name }}.


Чтобы поддержать собственный домен:

- Назовите бакет так же, как домен.

    {% note info %}

    Используйте для хостинга в {{ objstorage-name }} домены не ниже третьего уровня. Это связано с особенностями обработки записей `CNAME` на DNS-хостингах. Читайте подробнее в п.2.4 [RFC 1912](https://www.ietf.org/rfc/rfc1912.txt).

    {% endnote %}

- Настройте алиас для бакета у своего провайдера DNS или на собственном DNS сервере.

    Например, для домена `www.example.com` необходимо добавить запись

    ```
    www.example.com CNAME www.example.com.{{ s3-web-host }}
    ```
