---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Удалить Docker-образ из реестра

{% note alert %}

Удаление Docker-образа — это операция с отложенным действием: при удалении Docker-образа его слои физически удаляются **через 1 час**. Информация о суммарном размере реестра обновляется также через 1 час.

{% endnote %}

{% list tabs %}

- Консоль управления
  
  Чтобы удалить [Docker-образ](../../concepts/docker-image.md):
  1. Перейдите в репозиторий, из которого надо удалить образ:
      1. Откройте раздел **Container Registry** в каталоге.
      1. Откройте нужный реестр.
      1. Откройте нужный репозиторий.
  1. Нажмите значок ![image](../../../_assets/vertical-ellipsis.svg) в строке Docker-образа, который требуется удалить.
  1. В открывшемся меню нажмите кнопку **Удалить**.
  1. В открывшемся окне нажмите кнопку **Удалить**.
  
- CLI
  
  {% include [cli-install](../../../_includes/cli-install.md) %}
  
  Чтобы удалить [Docker-образ](../../concepts/docker-image.md), используйте его идентификатор. Узнать идентификатор можно,
  [запросив список Docker-образов в нужном реестре](docker-image-list.md#docker-image-list).
  
  1. Удалите Docker-образ:
  
      ```
      $ yc container image delete crp9vik7sgeco7emq743
      ```
  
  1. Проверьте, что Docker-образ действительно удален:
  
      ```
      $ yc container image list
      +----+---------+------+------+-----------------+
      | ID | CREATED | NAME | TAGS | COMPRESSED SIZE |
      +----+---------+------+------+-----------------+
      +----+---------+------+------+-----------------+
      ```
  
- API
  
  Чтобы удалить Docker-образ, воспользуйтесь методом [delete](../../api-ref/Image/delete.md) для ресурса [Image](../../api-ref/Image/).
  
{% endlist %}
