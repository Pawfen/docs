---

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---
# Data insert by INSERT

Add data to the table using [INSERT INTO](../reference/syntax/insert_into.md):

{% include [yql-reference-prerequisites](../../_includes/yql_tutorial_prerequisites.md) %}

```sql
INSERT INTO episodes
(
    series_id,
    season_id,
    episode_id,
    title,
    air_date
)
VALUES
(
    2,
    5,
    21,
    "Test 21",
    CAST(Date("2018-08-27") AS Uint64)
),                                        -- Rows are separated by commas.
(
    2,
    5,
    22,
    "Test 22",
    CAST(Date("2018-08-27") AS Uint64)
)
;

COMMIT;

-- See result:
SELECT * FROM episodes WHERE series_id = 2 AND season_id = 5;

COMMIT;
```

