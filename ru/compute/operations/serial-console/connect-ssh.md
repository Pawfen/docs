---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Подключение к серийной консоли виртуальной машины по SSH

После [включения доступа](./index.md), вы можете подключиться к серийной консоли для взаимодействия с виртуальной машиной. Перед подключением к серийной консоли внимательно ознакомьтесь с разделом [{#T}](#security).

## Безопасность {#security}

{% include [sc-warning](../../../_includes/compute/serial-console-warning.md) %}

Важным моментом при удаленном доступе является защита от [MITM-атак](https://en.wikipedia.org/wiki/Man-in-the-middle_attack). Защититься от MITM-атак можно, используя шифрование между клиентом и сервером.

Установить безопасное подключение можно следующими способами:

- Вы можете скачивать текущий [SHA256 Fingerprint](https://storage.yandexcloud.net/cloud-certs/serialssh-fingerprint.txt) (отпечаток) ключа перед каждым подключением к виртуальной машине.

    При первом подключении к виртуальной машине клиент сообщает отпечаток ключа и ожидает решения по установке соединения:

    - YES — установить соединение.
    - NO — отказаться.

    Убедитесь, что отпечаток по ссылке совпадает с отпечатком, полученным от клиента.

- Вы можете скачивать публичный [ключ](https://storage.yandexcloud.net/cloud-certs/serialssh-knownhosts) хоста перед каждым подключением к серийной консоли.

    Используйте полученный публичный ключ при подключении к серийной консоли.

    Рекомендуемые параметры запуска:

    ```bash
    $ ssh -o ControlPath=none -o IdentitiesOnly=yes -o CheckHostIP=no -o StrictHostKeyChecking=yes -o UserKnownHostsFile=./serialssh-knownhosts -p 9600 -i ~/.ssh/<имя закрытого ключа> <ID виртуальной машины>.<имя пользователя>@serialssh.cloud.yandex.net
    ```

    Публичный ключ хоста в будущем может быть изменен.

Чаще сверяйтесь с указанными файлами. Скачивайте данные файлы только по протоколу HTTPS, предварительно убедившись в валидности сертификата сайта `https://storage.yandexcloud.net`. Если из-за проблем с сертификатом сайт не может обеспечить безопасное шифрование ваших данных, браузер предупреждает об этом.

## Подключиться к серийной консоли {#connect-to-serial-console}

{% note info %}

Работа серийной консоли зависит от настроек используемой операционной системы. Сервис {{ compute-full-name }} обеспечивает канал между пользователем и COM-портом виртуальной машины и не гарантирует стабильность работы консоли со стороны операционной системы.

{% endnote %}

Для подключения к виртуальной машине необходимо знать ее идентификатор. Как получить идентификатор виртуальной машины читайте в разделе [{#T}](../vm-info/get-info.md).

Пример команды подключения:

```bash
$ ssh -t -p 9600 -o IdentitiesOnly=yes -i ~/.ssh/<имя закрытого ключа> <ID виртуальной машины>.<имя пользователя>@serialssh.cloud.yandex.net
```

Пример для пользователя `yc-user` и виртуальной машины с идентификатором `fhm0b28lgfp4tkoa3jl6`:

```bash
$ ssh -t -p 9600 -o IdentitiesOnly=yes -i ~/.ssh/id_rsa fhm0b28lgfp4tkoa3jl6.yc-user@serialssh.cloud.yandex.net
```

Пользователь `yc-user` создается автоматически при создании виртуальной машины. Подробнее читайте в разделе [{#T}](../vm-create/create-linux-vm.md).

#### Решение проблем {#troubleshooting}

- Если после подключения к серийной консоли на экране ничего не отображается:
    - Нажмите на клавиатуре клавишу `Enter`.
    - Перезапустите виртуальную машину (для виртуальных машин, созданных до 22 февраля).
- Если система запрашивает данные пользователя для доступа на виртуальную машину, укажите имя пользователя (логин) и пароль.
- Если при подключении возникает ошибка `Warning: remote host identification has changed!`, выполните команду `ssh-keygen -R <IP виртуальной машины>`.

## Отключиться от серийной консоли {#turn-off-serial-console}

Чтобы отключиться от серийной консоли:

1. Нажмите на клавиатуре клавишу `Enter`.
1. Введите последовательно символы `~.`.
