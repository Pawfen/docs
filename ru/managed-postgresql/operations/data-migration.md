---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Миграция базы данных в {{ mpg-name }}

Чтобы перенести вашу базу данных в сервис {{ mpg-name }}, нужно непосредственно перенести данные, закрыть старую базу данных на запись и перенести нагрузку на кластер БД в {{ yandex-cloud }}.

Перенести данные в кластер {{ mpg-name }} можно двумя способами:

* Рекомендуемый способ — логическая репликация ([subscriptions](https://www.postgresql.org/docs/current/sql-createsubscription.html)). Механизм подписки, на котором построена логическая репликация, позволяет перенести данные на кластер {{ mpg-name }} с минимальным временем простоя.
* Восстановление из дампа базы данных, сделанного с помощью утилиты `pg_dump`.

Ниже сервер СУБД, с которого вы переносите данные, называется _сервер-источник_, а кластер {{ mpg-name }}, на который вы мигрируете — _сервер-приемник_.

Инструкции предполагают, что вы знакомы с основами администрирования ОС Linux.

## Логическая репликация {#logical_replication}

Логическая репликация поддерживается с версии {{ PG }} 10. Кроме миграции данных между одинаковыми версиями СУБД, логическая репликация позволяет мигрировать на более свежие версии {{ PG }}: просто пройдите шаги миграции, настроив репликацию c сервера-источника на сервер-приемник с более свежей версией {{ PG }}.

На кластерах {{ mpg-name }} подписки может использовать владелец базы данных (пользователь, созданный одновременно с кластером) и пользователи с ролью `mdb.admin` для этого кластера.

Этапы миграции:

1. [Настройте сервер-источник](#source-setup).
1. [Экспортируйте схему БД на источнике](#source-schema-export).
1. [Создайте кластер-приемник и восстановите схему БД](#restore-schema).
1. [Создайте публикацию и подписку {{ PG }}](#create-publication-subscription).
1. [Перенесите {{ PG }}-sequence после репликации](#transfer-sequences).
1. [Отключите репликацию и перенесите нагрузку](#transfer-load).

### Настройте сервер с источником данных {#source-setup}

1. Укажите нужные настройки SSL и WAL в файле `postgresql.conf`. В ОС Debian и Ubuntu путь к этому файлу по умолчанию — `/etc/postgresql/10/main/postgresql.conf`.
   1. Для миграции данных рекомендуется использовать SSL: это поможет не только шифровать данные, но и сжимать их. Подробнее в разделах документации {{ PG }}, [SSL Support](https://www.postgresql.org/docs/current/libpq-ssl.html) и [Database Connection Control Functions](https://www.postgresql.org/docs/current/libpq-connect.html).

      Чтобы включить использование SSL, задайте нужное значение в конфигурации:

      ```ini
      ssl = on                   # on, off
      ```

   1. Измените уровень логирования для [Write Ahead Log (WAL)](https://www.postgresql.org/docs/current/static/wal-intro.html), чтобы добавить в него информацию, необходимую для логической репликации. Для этого установите значение `logical` для настройки [wal_level](https://www.postgresql.org/docs/current/runtime-config-wal.html#RUNTIME-CONFIG-WAL-SETTINGS).

      Настройку можно изменить в файле `postgresql.conf`. Найдите строку с настройкой `wal_level`, раскомментируйте ее при необходимости и установите значение `logical`:

      ```ini
      wal_level = logical                    # minimal, replica, or logical
      ```

1. Настройте аутентификацию хостов на источнике. Для этого нужно внести хосты кластера в {{ yandex-cloud }} в файл `pg_hba.conf` (на дистрибутивах Debian и Ubuntu по умолчанию расположен по пути `/etc/postgresql/10/main/pg_hba.conf`).

   Для этого туда следует добавить строки, которые разрешат входящие соединения к базе данных с указанных хостов.

   * Если вы используете SSL:

     ```txt
     hostssl         all            all             <адрес хоста>      md5
     hostssl    replication         all             <адрес хоста>      md5
     ```

   * Если вы не используете SSL:

     ```txt
     host         all            all             <адрес хоста>      md5
     host    replication         all             <адрес хоста>      md5
     ```

1. Если на сервере-источнике работает файрвол, разрешите входящие соединения с хостов кластера {{ mpg-name }}. Например, для Ubuntu 18:

   ```bash
   sudo ufw allow from <адрес хоста> to any port 5432
   ```

1. Перезагрузите сервер БД, чтобы применить все сделанные настройки:

   ```bash
   sudo systemctl restart postgresql
   ```

1. Проверьте статус {{ PG }} после перезапуска:

   ```bash
   sudo systemctl status postgresql
   ```

### Экспортируйте схему БД на источнике {#source-schema-export}

С помощью утилиты `pg_dump` создайте файл со схемой БД, которую нужно будет применить в кластере {{ mpg-name }}.

```bash
pg_dump -h <адрес сервера СУБД> -U <имя пользователя> -p <порт> --schema-only --no-privileges --no-subscriptions -d <имя базы данных> -Fd -f /tmp/db_dump
```

В этой команде при экспорте исключаются все данные, связанные с привилегиями и ролями, чтобы не возникало конфликтов с настройками БД в {{ yandex-cloud }}. Если вашей БД нужны дополнительные пользователи, [создайте их](cluster-users.md#adduser).

### Создайте кластер {{ mpg-name }} и восстановите схему БД {#restore-schema}

Если у вас еще нет {{ PG }}-кластера в {{ yandex-cloud }}, [создайте кластер {{ mpg-name }}](cluster-create.md). При создании кластера укажите то же имя базы данных, что и на сервере-источнике.

Восстановите схему в созданном кластере:

```bash
pg_restore -Fd -v --single-transaction -s --no-privileges \
          -h <адрес приемника> \
          -U <имя пользователя> \
          -p 6432 \
          -d <имя базы данных> /tmp/db_dump
```

### Создайте публикацию и подписку {#create-publication-subscription}

Для работы логической репликации необходимо определить публикацию (группу логически реплицируемых таблиц) на сервере-источнике и подписку (описание соединения с другой базой) на сервере-приемнике.

1. На сервере-источнике создайте публикацию для всех таблиц базы данных. При переносе нескольких баз для каждой из них нужно создать отдельную публикацию.

   {% note info %}

   Для создания публикации на все таблицы потребуются права суперпользователя, а для переноса выбранных таблиц — нет. Более подробно о создании публикации см. в [документации {{ PG }}](https://www.postgresql.org/docs/current/sql-createpublication.html).

   {% endnote %}

   Запрос:

   ```sql
   CREATE PUBLICATION p_data_migration FOR ALL TABLES;
   ```

1. На хосте кластера {{ mpg-name }} создайте подписку со строкой подключения к публикации. Более подробно о создании
подписок см. в [документации {{ PG }}](https://www.postgresql.org/docs/10/sql-createsubscription.html).

   Запрос с включенным SSL:

   ```sql
   CREATE SUBSCRIPTION s_data_migration CONNECTION 'host=<адрес сервера-источника> port=<порт> user=<имя пользователя> sslmode=verify-full dbname=<имя базы данных>' PUBLICATION p_data_migration;
   ```

   Без SSL:

   ```sql
   CREATE SUBSCRIPTION s_data_migration CONNECTION 'host=<адрес сервера-источника> port=<порт> user=<имя пользователя> sslmode=disable dbname=<имя базы данных>' PUBLICATION p_data_migration;
   ```

1. Следить за статусом репликации можно через каталоги `pg_subscription_rel`. Общий статус репликации на приемнике можно получить через `pg_stat_subscription`, на источнике — через `pg_stat_replication`.

   ```sql
   select * from pg_subscription_rel;
   ```

   Прежде всего за статусом репликации стоит следить на приемнике, по полю `srsubstate`. Значение `r` в поле `srsubstate` означает, что синхронизация завершилась и базы готовы к репликации.

### Перенесите {{ PG }}-sequences после репликации {#transfer-sequences}

Чтобы полностью завершить синхронизацию источника и приемника, запретите запись новых данных на сервере-источнике и перенесите {{ PG }}-sequences в кластер {{ mpg-name }}:

1. Экспортируйте {{ PG }}-sequences на источнике:

   ```bash
   pg_dump -h <адрес сервера СУБД> -U <имя пользователя> -p <порт> -d <имя базы данных> \
           --data-only -t '*.*_seq' > /tmp/seq-data.sql
   ```

   Обратите внимание на используемый паттерн: если в переносимой базе данных есть sequences, не соотвествующие паттерну `*.*_seq`, то для их выгрузки необходимо указать другой паттерн. Более подробно о паттернах — в [документации {{ PG }}](https://www.postgresql.org/docs/current/app-psql.html#APP-PSQL-PATTERNS).

1. Восстановите sequences на хосте {{ mpg-name }}:

   ```bash
   psql -h <адрес сервера СУБД> -U <имя пользователя> -p 6432 -d <имя базы данных> \
        < /tmp/seq-data.sql
   ```

### Отключите репликацию и перенесите нагрузку {#transfer-load}

После того, как репликация завершена, и вы перенесли sequences, удалите подписку на сервере-приемнике (в кластере {{ mpg-name }}):

   ```sql
   DROP SUBSCRIPTION s_data_migration;
  ```

После этого можно переносить нагрузку на сервер-приемник. Так как процесс переноса sequences относительно быстрый и легко автоматизируется, то миграция на {{ mpg-name }} возможна с минимальным временем простоя.

## Восстановление из дампа базы данных {#backup}

Для переноса данных из существующей базы данных {{ PG }} в сервис {{ mpg-name }} используйте утилиты `pg_dump` и `pg_restore`: создайте дамп рабочей базы и восстановите его в {{ PG }}-кластере {{ yandex-cloud }}.

{% note info %}

Для использования утилиты `pg_restore` может понадобиться расширение базы данных `pg_repack`.

{% endnote %}

Перед тем, как пытаться импортировать данные, проверьте, совпадают ли версии СУБД у существующей базы данных и вашего кластера в {{ yandex-cloud }}. Если версии разные, восстановить сделанный дамп не получится. Чтобы мигрировать на {{ PG }} версии 11, 12 или 13, можно использовать [логическую репликацию](#logical_replication).

Этапы миграции:

1. [Создайте дамп переносимой базы](#dump).
1. [(опционально) Создайте виртуальную машину в {{ yandex-cloud }} и загрузите дамп БД на нее](#create-vm).
1. [Создайте кластер {{ mpg-name }}](#create-cluster).
1. [Восстановите данные из дампа в кластер](#restore).


### Создайте дамп базы данных {#dump}

Создать дамп базы данных следует с помощью утилиты [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html).

1. Перед созданием дампа рекомендуется переключить базу в режим <q>только чтение</q>, чтобы не потерять данные, которые бы появились во время создания дампа. Сам дамп базы данных создается следующей командой:

    ```bash
    pg_dump -h <адрес сервера СУБД> -U <имя пользователя> -Fd -d <имя базы данных> -f ~/db_dump
    ```

1. Для ускорения процесса вы можете запустить сброс дампа, используя несколько ядер процессора. Для этого задайте флаг `-j` с числом, соответствующим количеству ядер, которое доступно СУБД:

    ```bash
    pg_dump -h <адрес сервера СУБД> -U <имя пользователя> -j 4 -Fd -d <имя базы данных> -f ~/db_dump
    ```

1. Упакуйте дамп в архив:

    ```bash
    tar -cvzf db_dump.tar.gz ~/db_dump
    ```

Подробно утилита `pg_dump` описана в [документации {{ PG }}](https://www.postgresql.org/docs/current/app-pgdump.html).

### (опционально) Создайте виртуальную машину в {{ yandex-cloud }} и загрузите дамп на нее {#create-vm}

Переносить данные на промежуточную виртуальную машину в {{ compute-name }} нужно, если:

* К вашему кластеру {{ mpg-name }} нет доступа из интернета.
* Ваше оборудование или соединение с кластером в {{ yandex-cloud }} недостаточно надежны.

Нужное количество оперативной памяти и ядер процессора зависит от объема переносимых данных и требуемой скорости переноса.

Чтобы подготовить виртуальную машину для восстановления дампа:

1. В консоли управления [создайте новую виртуальную машину](../../compute/operations/vm-create/create-linux-vm.md) из образа Ubuntu 18.04. Параметры виртуальной машины должны зависеть от размера базы, которую вы хотите перенести. Минимальной конфигурации (1 ядро, 2 ГБ RAM, 10 ГБ дискового пространства) должно хватить для переноса базы до 1 ГБ. Чем больше переносимая база, тем больше должно быть оперативной памяти, и тем больше должно быть дискового пространства (как минимум в два раза больше, чем размер базы).

    Виртуальная машина должна находиться в той же сети и зоне доступности, что и кластер {{ PG }}. Кроме того, виртуальной машине должен быть присвоен внешний IP-адрес, чтобы вы могли загрузить дамп извне {{ yandex-cloud }}.

1. Настройте [apt-репозиторий {{ PG }}](https://www.postgresql.org/download/linux/ubuntu/).

1. Установите клиент {{ PG }} и дополнительные утилиты для работы с СУБД:

   ```bash
   $ sudo apt install postgresql-client-common

   # Для PostgreSQL 10
   $ sudo apt install postgresql-client-10

   # Для PostgreSQL 11
   $ sudo apt install postgresql-client-11

   # Для PostgreSQL 12
   $ sudo apt install postgresql-client-12

   # Для PostgreSQL 13
   $ sudo apt install postgresql-client-13
   ```

1. Перенесите дамп базы данных на виртуальную машину, например, используя утилиту `scp`:

    ```bash
    scp ~/db_dump.tar.gz <имя пользователя ВМ>@<публичный адрес ВМ>:/tmp/db_dump.tar.gz
    ```

1. Распакуйте дамп:

    ```bash
    tar -xzf /tmp/db_dump.tar.gz
    ```

### Создайте кластер {{ mpg-name }} {#create-cluster}

Убедитесь, что вычислительная мощность и размер хранилища кластера соответствуют среде,
 в которой развернуты уже имеющиеся базы данных, и [создайте кластер](cluster-create.md).


### Восстановите данные в новом окружении {#restore}

Восстанавливать дамп базы данных следует с помощью утилиты [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html).

Версия `pg_restore` должна совпадать с версией `pg_dump`, а мажорная версия должна быть не меньше, чем у базы на которую дамп разворачивается.

То есть для восстановления дампа {{ PG }} 10, {{ PG }} 11, {{ PG }} 12 и {{ PG }} 13 следует использовать `pg_restore 10`, `pg_restore 11`, `pg_restore 12` и `pg_restore 13` соответственно.

Если нужно восстановить только одну схему, добавьте флаг `-n <имя схемы>` (без этого флага команда сработает только от лица владельца базы данных). Восстановление лучше всего проводить с флагом `--single-transaction`, чтобы избежать неопределенного состояния базы в случае ошибки:

```bash
pg_restore -Fd \
           -v \
           -h <pgsql_host_address> \
           -U <username>
           -d <database_name> \
           -p 6432 \
           /tmp/db_dump \
           --single-transaction \
           --no-privileges
```

Подробно утилита `pg_restore` описана в [документации {{ PG }}](https://www.postgresql.org/docs/current/app-pgrestore.html).
