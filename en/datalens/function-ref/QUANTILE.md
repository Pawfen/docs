---
editable: false

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---


# QUANTILE



#### Syntax {#syntax}


```
QUANTILE( value, quant )
```

#### Description {#description}
Returns the precise `quant`-level quantile (`quant` should be in range from 0 to 1).

**Argument types:**
- `value` — `Date | Datetime | Fractional number | Integer`
- `quant` — `Fractional number | Integer`


**Return type**: Same type as (`value`)

#### Data source support {#data-source-support}

`Materialized Dataset`, `ClickHouse 1.1`, `Microsoft SQL Server 2017 (14.0)`, `Oracle Database 12c (12.1)`, `PostgreSQL 9.4`.
