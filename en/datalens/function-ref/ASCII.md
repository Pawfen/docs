---
editable: false

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---


# ASCII



#### Syntax {#syntax}


```
ASCII( string )
```

#### Description {#description}
Returns the numeric representation of the first character of the string.

**Argument types:**
- `string` — `String`


**Return type**: `Integer`

#### Examples {#examples}

```
ASCII("N") = 78
```

```
ASCII("¹") = 185
```

```
ASCII("¹") = 194
```

```
ASCII("…") = 8230
```


#### Data source support {#data-source-support}

`Materialized Dataset`, `ClickHouse 1.1`, `Microsoft SQL Server 2017 (14.0)`, `MySQL 5.6`, `Oracle Database 12c (12.1)`, `PostgreSQL 9.3`.
