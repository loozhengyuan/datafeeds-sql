# datafeeds-sql
SQL code snippets for deriving dimensions and metrics from Adobe Analytics Data Feeds

### `time_spent_per_visit`

```sql
WITH
  cte AS (
  SELECT
    CONCAT( CAST(post_visid_low AS STRING), CAST(post_visid_high AS STRING), CAST(visit_num AS STRING), CAST(visit_start_time_gmt AS STRING) ) AS visit,
    LEAD(post_cust_hit_time_gmt) OVER (PARTITION BY CONCAT(CAST(post_visid_low AS STRING), CAST(post_visid_high AS STRING), CAST(visit_num AS STRING), CAST(visit_start_time_gmt AS STRING) )
    ORDER BY
      post_cust_hit_time_gmt) - post_cust_hit_time_gmt AS time_spent_per_visit,
  FROM
    `project.dataset.table` )
SELECT
  visit,
  SUM(time_spent_per_visit) AS time_spent_per_visit
FROM
  cte
GROUP BY
  visit
```
