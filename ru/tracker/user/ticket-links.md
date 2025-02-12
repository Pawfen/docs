---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Изменить связи задачи

Задачу можно связать с другими задачами, чтобы сгруппировать задачи с общей темой или обозначить зависимость задач друг от друга.

Число связей задачи не ограничено. Список связанных задач отображается в блоке **Связанные** под описанием задачи.

Также к задаче можно привязать [коммит в репозиторий](#section_commit). 


## Добавить связь {#section_wz1_rpn_jz}

Чтобы создать связь с другой задачей:

1. Выберите **Действия** → **Добавить связь**.

1. Выберите подходящий [тип связи](links.md).

1. Укажите ключ задачи, с которой нужно создать связь. Найти ключ можно на странице задачи сразу под заголовком (например, `TEST-1234`).

1. Нажмите **Добавить связь**.

{% note info %}

Связь с задачей создается автоматически, если указать ее ключ в комментарии или описании.

{% endnote %}


## Сделать задачу подзадачей {#section_nfz_qpn_jz}

Задачу можно сделать частью более крупной (родительской) задачи:

1. Выберите **Действия** → **Сделать подзадачей**.

1. Укажите ключ родительской задачи. Найти ключ можно на странице задачи сразу под заголовком (например, `TEST-1234`).

1. Нажмите **Сохранить**.


## Изменить родительскую задачу {#section_zhl_xv1_wz}

1. Откройте страницу подзадачи.

1. Нажмите на значок ![](../../_assets/tracker/edit-parent.png) возле названия родительской задачи в верхней части страницы.

1. Введите ключ задачи, которую вы хотите сделать родительской.

1. Нажмите кнопку **Сохранить**.


## Удалить связь с родительской задачей {#section_uh5_sw1_wz}

Чтобы удалить связь с родительской задачей:

1. Откройте страницу дочерней задачи.

1. Нажмите на значок ![](../../_assets/tracker/edit-parent.png) возле названия родительской задачи в верхней части страницы.

1. Нажмите кнопку **Удалить связь**.


## Связать коммит с задачей {#section_commit}

Вы можете привязать к задаче коммит в репозиторий, который [подключен к {{ tracker-name }}](../manager/add-repository.md). Для этого укажите ключ задачи в комментарии к коммиту. Привязанные коммиты можно просмотреть:

- на странице задачи на вкладке **Коммиты**;
- на странице очереди на вкладке **Коммиты**.

Если вы не видите вкладки **Коммиты**, убедитесь, что она включена в [настройках очереди](../manager/edit-queue-general.md).






