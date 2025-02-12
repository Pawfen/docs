- **Начало резервного копирования (UTC)**{#setting-backup-start}

  Время по UTC (в 24-часовом формате), когда начинается [резервное копирование](../../managed-mysql/operations/cluster-backups.md) кластера. Если время не задано, резервное копирование начинается в 22:00 UTC.

- **Окно обслуживания**{#setting-maintenance-window}

  Настройки окна технического обслуживания:
  - Чтобы указать предпочтительное время начала обслуживания хостов кластера, выберите пункт по **по расписанию** и укажите из выпадающих списков нужные день недели и час дня в UTC (Coordinated Universal Time). Вы можете выбрать время, когда кластер наименее загружен.
  - Чтобы разрешить проведение технического обслуживания в любое время, выберите пункт **произвольное**.

  К техническому обслуживанию относится обновление версии СУБД, применение патчей и так далее.

- **Доступ из {{ datalens-name }}**{#setting-datalens-access}
  
  Разрешает анализировать данные из кластера в сервисе [{{ datalens-full-name }}](../../datalens/concepts/index.md).
  
  Подробнее о настройке подключения см. в разделе [Подключение к {{ datalens-name }}](../../managed-mysql/operations/datalens-connect.md).

- **Доступ из консоли управления**{#setting-websql-access}

  Разрешает [выполнять SQL-запросы](../../managed-mysql/operations/web-sql-query.md) к базам кластера из консоли управления {{ yandex-cloud }}.

- **Сбор статистики** — включите эту опцию, чтобы воспользоваться инструментом [{#T}](../../managed-mysql/operations/performance-diagnostics.md) в кластере.
