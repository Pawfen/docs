---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Плагин для **IntelliJ** IDE

Если вы используете IDE от компании JetBrains, свяжите ее с {{ tracker-name }} с помощью [плагина](https://plugins.jetbrains.com/plugin/10549-yandex-tracker-integration). Плагин позволяет работать с задачами из интерфейса IDE. Подробнее о возможностях плагина читайте в помощи вашей IDE: [IntelliJ IDEA](https://www.jetbrains.com/help/idea/managing-tasks-and-context.html), [PhpStorm](https://www.jetbrains.com/help/phpstorm/2017.2/managing-tasks-and-contexts.html), [WebStorm](https://www.jetbrains.com/help/webstorm/managing-tasks-and-context.html), [PyCharm](https://www.jetbrains.com/help/pycharm/managing-tasks-and-context.html), [RubyMine](https://www.jetbrains.com/help/ruby/managing-tasks-and-context.html), [AppCode](https://www.jetbrains.com/help/objc/managing-tasks-and-context.html), [CLion](https://www.jetbrains.com/help/clion/managing-tasks-and-context.html), [GoLand](https://www.jetbrains.com/help/clion/2017.2/managing-tasks-and-contexts.html), [DataGrip](https://www.jetbrains.com/help/idea/managing-tasks-and-context.html), [Rider](https://www.jetbrains.com/help/rider/managing_tasks_and_context.html), [MPS](https://www.jetbrains.com/help/idea/managing-tasks-and-context.html), [Android Studio](https://www.jetbrains.com/help/idea/managing-tasks-and-context.html).

{% note warning %}

Плагин позволяет работать только с теми задачами, для которых вы являетесь исполнителем.

{% endnote %}

## Установить плагин {#section_jm1_r4d_kdb}


#### Из репозитория

1. Откройте окно настроек вашей IntelliJ IDE: **File** → **Settings**.

1. На вкладке **Plugins** нажмите кнопку **Browse Repositories**.

1. Введите в строку поиска запрос `Yandex.Tracker integration`.

1. Нажмите кнопку **Install** в правой части окна.

1. Перезапустите вашу IDE.

#### Из файла

1. Скачайте плагин из официального [каталога плагинов](https://plugins.jetbrains.com/plugin/10549-yandex-tracker-integration).

1. Откройте окно настроек вашей IntelliJ IDE: **File** → **Settings**.

1. На вкладке **Plugins** нажмите кнопку **Install plugin from disk**.

1. Укажите расположение архива с плагином и нажмите кнопку **OK**.

1. Перезапустите вашу IDE.

## Подключить плагин к {{ tracker-name }} {#section_vgn_cqd_kdb}


Чтобы плагин заработал, привяжите его к своему аккаунту в {{ tracker-name }}:


1. Авторизуйтесь в {{ tracker-name }}.

1. Запустите вашу IntelliJ IDE и выберите **Tools** → **Tasks & Contexts** → **Configure Servers**.

1. Нажмите кнопку **+** и выберите **Yandex Tracker**.

1. Нажмите кнопку **Authorize** и дождитесь появления окна браузера.

1. Разрешите приложению <q>Yandex.Tracker IntelliJ Plugin</q> доступ к вашим данным на Яндексе.

1. Скопируйте код подтверждения.

1. Вставьте код подтверждения в окно **Authentication Code** в IDE.

1. Нажмите кнопку **ОК**.

1. Выберите очередь, в которой будете работать.

1. Нажмите кнопку **ОК**.

