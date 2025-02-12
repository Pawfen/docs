---
editable: false

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---


# Правила тарификации для {{ mms-name }}

## Статус кластера {#running-stopped}

В зависимости от статуса кластера тарифы применяются различным образом:

* Кластер  запущен (`Running`) — тарифицируются лицензии ПО, вычислительные ресурсы, объем хранилища и резервных копий.
* Кластер остановлен (`Stopped`) —  объем хранилища и резервных копий тарифицируется в полном объеме, а для лицензий ПО применяются правила, описанные в разделе [{#T}](#license). Вычислительные ресурсы не тарифицируются.

  {% note alert %}

  Если кластер использует локальное хранилище (`local-ssd`), вычислительные ресурсы кластера не освобождаются при его остановке и тарифицируются в полном объеме.

  {% endnote %}


## Из чего складывается стоимость использования {{ mms-short-name }} {#rules}

Расчет стоимости использования {{ mms-name }} учитывает:

* стоимость лицензий ПО Windows Server Standard и Microsoft SQL Server;

{% include [pricing-rules](../_includes/mdb/pricing-rules.md) %}


{% include [pricing-gb-size](../_includes/pricing-gb-size.md) %}

### Использование лицензий ПО {#license}

Использование лицензий тарифицируется помесячно на авансовой основе. Это означает, что плата взимается в начале периода тарификации еще до фактического потребления ресурсов сервиса. При этом:

* Если в течение периода тарификации вы остановили или удалили кластер, стоимость лицензий в этом периоде не возвращается.
* Если вы изменили конфигурацию кластера и снизили потребление ресурсов, стоимость освободившихся лицензий в этом периоде не возвращается. Новая стоимость начинает действовать с первого числа следующего месяца.
* Если в течение периода тарификации вы создали новый или запустили ранее остановленный кластер, стоимость лицензий будет рассчитана исходя из оставшихся в отчетном периоде дней. Например, если в месяце 30 дней и до конца месяца осталось 7 дней, вы заплатите 7/30 от месячной стоимости лицензий.
* Если вы изменили конфигурацию кластера и увеличили потребление ресурсов, вам понадобятся дополнительные лицензии. Их стоимость будет рассчитана исходя из оставшихся в отчетном периоде дней. Например, если в месяце 30 дней и до конца месяца осталось 7 дней, доплата составит 7/30 от месячной стоимости новых лицензий. 


### Использование хостов БД {#rules-hosts-uptime}

Стоимость использования хостов БД вычисляется в соответствии с их классом. Точные характеристики классов приведены в разделе [{#T}](concepts/instance-types.md). Плата за использование взимается в порядке, предусмотренном заключенным договором.

Минимальная единица тарификации — минута (например, стоимость 1,5 минут работы хоста равна стоимости 2 минут). Время, когда хост БД не может выполнять свои основные функции, не тарифицируется.

### Использование дискового пространства {#rules-storage}

Плата за использование дискового пространства взимается в порядке, предусмотренном заключенным договором. Оплачивается:

* Объем хранилища, выделенный для кластеров БД.

    * Хранилище на быстрых локальных дисках (`local-ssd`) можно заказывать только для кластеров с тремя хостами и более, с шагом 100 ГБ.
    * Хранилище на нереплицируемых сетевых дисках (`network-ssd-nonreplicated`) можно заказывать только для кластеров с тремя хостами и более, с шагом 93 ГБ.

* Объем, занимаемый резервными копиями баз данных сверх заданного хранилища для кластера.

    * Хранение резервных копий не тарифицируется пока сумма размера БД и всех резервных копий остается меньше выбранного объема хранилища.

    * При автоматическом резервном копировании {{ mms-short-name }} не создает новую копию, а сохраняет изменения БД по сравнению с предыдущей копией. Поэтому потребление хранилища автоматическими резервными копиями растет только пропорционально объему изменений.

    * Количество хостов кластера не влияет на объем хранилища и, соответственно, на бесплатный объем резервных копий.

Цена указывается за 1 месяц использования. Минимальная единица тарификации — 1 ГБ в минуту (например, стоимость хранения 1 ГБ в течение 1,5 минут равна стоимости хранения в течение 2 минут).

## Цены {#prices}

Все цены указаны с включением НДС.


{% include [rub-pricing.md](../_pricing/managed-sqlserver/rub-pricing.md) %}



### Исходящий трафик {#prices-traffic}


{% include notitle [rub-egress-traffic.md](../_pricing/rub-egress-traffic.md) %}



## Расчетные цены для классов хостов {#calculated-prices}

Цены за время работы хостов рассчитаны исходя из конфигураций [классов хостов](concepts/instance-types.md) и приведенных выше цен за использование лицензий ПО, vCPU и RAM для соответствующей платформы. Чтобы точно рассчитать стоимость нужного кластера, воспользуйтесь [калькулятором](https://cloud.yandex.ru/services/managed-sqlserver#calculator).

{% include [host-class-price-alert](../_includes/mdb/pricing-host-class-alert.md) %}

За месяц работы хоста из расчета 720 часов в месяц, округлено до целых рублей.


{% include [rub-licence.md](../_pricing/managed-sqlserver/rub-licence.md) %}


