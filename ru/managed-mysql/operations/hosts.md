---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Управление хостами кластера

Вы можете добавлять и удалять хосты кластера.

## Получить список хостов в кластере {#list}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ mmy-name }}**.
  1. Нажмите на имя нужного кластера, затем выберите вкладку **Хосты**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы получить список баз данных в кластере, выполните команду:

  ```
  $ {{ yc-mdb-my }} host list
       --cluster-name=<имя кластера>

  +----------------------------+--------------+---------+--------+---------------+
  |            NAME            |  CLUSTER ID  |  ROLE   | HEALTH |    ZONE ID    |
  +----------------------------+--------------+---------+--------+---------------+
  | rc1b...mdb.yandexcloud.net | c9q5k4ve7... | MASTER  | ALIVE  | ru-central1-b |
  | rc1c...mdb.yandexcloud.net | c9q5k4ve7... | REPLICA | ALIVE  | ru-central1-c |
  +----------------------------+--------------+---------+--------+---------------+
  ```

  Имя кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- API

  Получить список хостов кластера можно с помощью метода [listHosts](../api-ref/Cluster/listHosts.md).

{% endlist %}


## Добавить хост {#add}

Количество хостов в кластерах {{ mmy-short-name }} ограничено квотами на количество CPU и объем памяти, которые доступны кластерам БД в вашем облаке. Чтобы проверить используемые ресурсы, откройте страницу [Квоты]({{ link-console-quotas }}) и найдите блок **Managed Databases**.

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ mmy-name }}**.
  1. Нажмите на имя нужного кластера и перейдите на вкладку **Хосты**.
  1. Нажмите кнопку **Добавить хост**.
  1. Укажите параметры хоста:

     - зону доступности;
     - подсеть (если нужной подсети в списке нет, [создайте ее](../../vpc/operations/subnet-create.md));
     - приоритет хоста как {{ MY }}-реплики;
     - источник репликации (если вы используете каскадную репликацию);
     - выберите опцию **Публичный доступ**, если хост должен быть доступен извне {{ yandex-cloud }}.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы добавить хост в кластере:

  1. Запросите список подсетей кластера, чтобы выбрать подсеть для нового хоста:

      ```
      $ yc vpc subnet list

      +-----------+-----------+------------+---------------+------------------+
      |     ID    |   NAME    | NETWORK ID |     ZONE      |      RANGE       |
      +-----------+-----------+------------+---------------+------------------+
      | b0cl69... | default-c | enp6rq7... | ru-central1-c | [172.16.0.0/20]  |
      | e2lkj9... | default-b | enp6rq7... | ru-central1-b | [10.10.0.0/16]   |
      | e9b0ph... | a-2       | enp6rq7... | ru-central1-a | [172.16.32.0/20] |
      | e9b9v2... | default-a | enp6rq7... | ru-central1-a | [172.16.16.0/20] |
      +-----------+-----------+------------+---------------+------------------+
      ```

      Если нужной подсети в списке нет, [создайте ее](../../vpc/operations/subnet-create.md).

  1. Посмотрите описание команды CLI для добавления хостов:

     ```
     $ {{ yc-mdb-my }} host add --help
     ```

  1. Выполните команду добавления хоста:

     ```
     $ {{ yc-mdb-my }} host add
          --cluster-name=<имя кластера>
          --host zone-id=<зона доступности>,subnet-id=<ID подсети>
     ```

     {{ mmy-short-name }} запустит операцию добавления хоста.

     Идентификатор подсети необходимо указать, если в зоне доступности больше одной подсети, в противном случае {{ mmy-short-name }} автоматически выберет единственную подсеть. Имя кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- {{ TF }}

  1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

      О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

  1. Добавьте к описанию кластера {{ mmy-name }} блок `host`:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<имя кластера>" {
        ...
        host {
          zone             = "<зона доступности>"
          subnet_id        = <идентификатор подсети>
          assign_public_ip = <публичный доступ к хосту: true или false>
          ...
        }
      }
      ```

      Запросить публичный доступ после создания хоста невозможно, но вы можете [удалить](#remove) какой-либо из имеющихся хостов и создать новый с публичным адресом.

  1. Проверьте корректность настроек.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Подтвердите изменение ресурсов.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

  Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-mmy }}).

- API

  Добавить хост в кластер можно с помощью метода [addHosts](../api-ref/Cluster/addHosts.md).

{% endlist %}

{% note warning %}

Если после добавления хоста к нему невозможно [подключиться](connect.md), убедитесь, что [группа безопасности](../concepts/network.md#security-groups) кластера настроена корректно для подсети, в которую помещен хост.

{% endnote %}

## Удалить хост {#remove}

Вы можете удалить хост из {{ MY }}-кластера, если он не является единственным хостом. Чтобы заменить единственный хост, сначала создайте новый хост, а затем удалите старый.

Если хост является мастером в момент удаления, {{ mmy-short-name }} автоматически назначит мастером следующую по приоритету реплику.

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ mmy-name }}**.
  1. Нажмите на имя нужного кластера и выберите вкладку **Хосты**.
  1. Нажмите значок ![image](../../_assets/cross.svg) в строке нужного хоста.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы удалить хост из кластера, выполните команду:

  ```
  $ {{ yc-mdb-my }} host delete <имя хоста>
       --cluster-name=<имя кластера>
  ```

  Имя хоста можно запросить со [списком хостов в кластере](#list-hosts), имя кластера — со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- Terraform

  1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

      О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

  1. Удалите из описания кластера {{ mmy-name }} блок `host`.

  1. Проверьте корректность настроек.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Подтвердите удаление ресурсов.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

  Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-mmy }}).

- API

  Удалить хост можно с помощью метода [deleteHosts](../api-ref/Cluster/deleteHosts.md).
  
{% endlist %}
