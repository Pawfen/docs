---

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---
# Data aggregation

Find out the number of unique episodes within every season of every series.

{% include [yql-reference-prerequisites](../../_includes/yql_tutorial_prerequisites.md) %}

```sql
SELECT
    series_id,
    season_id,
    COUNT(*) AS cnt     -- Aggregation function COUNT returns the number of rows
                     -- output by the query.
                     -- Asterisk (*) specifies that COUNT
                     -- counts the total number of rows in the table.
                     -- COUNT(*) returns the number of rows in
                     -- the specified table, preserving the duplicate rows.
                     -- The function counts each row separately.
                     -- The result includes rows that contain null values.
FROM episodes

GROUP BY
    series_id,          -- The query result will follow the listed order of columns
    season_id -- Multiple columns are separated by a comma.
                     -- Other columns can be listed after SELECT only if
                     -- they are passed to an aggregate function.
ORDER BY
    series_id,
    season_id
;

COMMIT;
```

