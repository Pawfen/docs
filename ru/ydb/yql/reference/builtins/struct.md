---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Функции для работы со структурами

## ExpandStruct {#expandstruct}

Добавление одного или нескольких новых полей в структуру.

В случае возникновения дублей в наборе полей будет возвращена ошибка.

Аргументы:

* В первый аргумент передается исходная структура для расширения.
* Все остальные аргументы должны быть именованными, каждый аргумент добавляет новое поле и имя аргумента используется в роли имени поля (по аналогии с [AsStruct](basic.md#asstruct)).

**Примеры**

```sql
$struct = AsStruct(1 AS a);
SELECT
  ExpandStruct(
    $struct,
    2 AS b,
    "3" AS c
  ) AS abc;
```

## AddMember {#addmember}

Добавление одного нового поля в структуру. Если необходимо добавление нескольких полей, предпочтительнее использовать [ExpandStruct](#expandstruct).

В случае возникновения дублей в наборе полей будет возвращена ошибка.

Аргументы:

1. Исходная структура;
2. Имя нового поля;
3. Значение нового поля.

**Примеры**

```sql
$struct = AsStruct(1 AS a);
SELECT
  AddMember(
    $struct,
    "b",
    2
  ) AS ab;
```

## RemoveMember {#removemember}

Удаление поля из структуры.

Если указанного поля не существовало, будет возвращена ошибка. 

Аргументы:

1. Исходная структура;
2. Имя  поля.

**Примеры**

```sql
$struct = AsStruct(1 AS a, 2 AS b);
SELECT
  RemoveMember(
    $struct,
    "b"
  ) AS a;
```

## ForceRemoveMember {#forceremovemember}

Удаление поля из структуры.

Если указанного поля не существовало, в отличии от [RemoveMember](#removemember) ошибка возвращена не будет.

Аргументы:

1. Исходная структура;
2. Имя  поля.

**Примеры**

```sql
$struct = AsStruct(1 AS a, 2 AS b);
SELECT
  ForceRemoveMember(
    $struct,
    "c"
  ) AS ab;
```

## CombineMembers {#combinemembers}

Объединение полей нескольких новых структур в новую структуру.

В случае возникновения дублей в результирующем наборе полей будет возвращена ошибка.

Аргументы:

1. Две и более структуры.

**Примеры**

```sql
$struct1 = AsStruct(1 AS a, 2 AS b);
$struct2 = AsStruct(3 AS c);
SELECT
  CombineMembers(
    $struct1,
    $struct2
  ) AS abc;
```

## FlattenMembers {#flattenmembers}

Объединение полей нескольких новых структур в новую структуру с поддержкой префиксов.

В случае возникновения дублей в результирующем наборе полей будет возвращена ошибка.

Аргументы:

1. Два и более кортежа из двух элементов: префикс и структура.

**Примеры**

```sql
$struct1 = AsStruct(1 AS a, 2 AS b);
$struct2 = AsStruct(3 AS c);
SELECT
  FlattenMembers(
    AsTuple("foo", $struct1), -- fooa, foob
    AsTuple("bar", $struct2)  -- barc
  ) AS abc;
```
