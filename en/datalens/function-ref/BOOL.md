---
editable: false

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---


# BOOL



#### Syntax {#syntax}


```
BOOL( expression )
```

#### Description {#description}
Converts the `expression` expression to Boolean type according to the following rules:

| Type                                          | `FALSE`             | `TRUE`     |
|:----------------------------------------------|:--------------------|:-----------|
| <code>Fractional number &#124; Integer</code> | `0`, `0.0`          | All others |
| `String`                                      | Empty string (`""`) | All others |
| `Boolean`                                     | `FALSE`             | `TRUE`     |
| <code>Date &#124; Datetime</code>             | -                   | `TRUE`     |

**Argument types:**
- `expression` — `Boolean | Date | Datetime | Fractional number | Geopoint | Geopolygon | Integer | String`


**Return type**: `Boolean`

#### Examples {#examples}

```
BOOL(0) = FALSE
```

```
BOOL(#2019-04-04#) = TRUE
```

```
BOOL("Lorem ipsum") = TRUE
```


#### Data source support {#data-source-support}

`Materialized Dataset`, `ClickHouse 1.1`, `Microsoft SQL Server 2017 (14.0)`, `MySQL 5.6`, `Oracle Database 12c (12.1)`, `PostgreSQL 9.3`.
