---
editable: false

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---


# STARTSWITH



#### Синтаксис {#syntax}


```
STARTSWITH( string, substring )
```

#### Описание {#description}
Возвращает `TRUE`, если строка `string` начинается на подстроку `substring`. Для регистронезависимой проверки см. [ISTARTSWITH](ISTARTSWITH.md).

**Типы аргументов:**
- `string` — `Логический | Дата | Дата и время | Дробное число | Геоточка | Геополигон | Целое число | Строка | UUID`
- `substring` — `Строка`


**Возвращаемый тип**: `Логический`

#### Примеры {#examples}

```
STARTSWITH("Petrov Ivan", "Petrov") = TRUE
```

```
STARTSWITH("Lorem ipsum", "Lore") = TRUE
```

```
STARTSWITH("Lorem ipsum", "abc") = FALSE
```


#### Поддержка источников данных {#data-source-support}

`Материализованный датасет`, `ClickHouse 1.1`, `Yandex.Metrica`, `Microsoft SQL Server 2017 (14.0)`, `MySQL 5.6`, `Oracle Database 12c (12.1)`, `PostgreSQL 9.3`.
