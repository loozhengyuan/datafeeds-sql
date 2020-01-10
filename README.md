# datafeeds-sql
SQL code snippets for deriving dimensions and metrics from Adobe Analytics Data Feeds

### `time_spent_per_visit`

According to (Adobe Analytics documentation)[https://docs.adobe.com/content/help/en/analytics/export/analytics-data-feed/data-feed-contents/datafeeds-calculate.html] page:

> **Time spent**
> Hits must first be grouped by visit, then ordered according to the hit number within the visit.
> 1. Concatenate post_visid_high , post_visid_low , visit_num , and visit_start_time_gmt .
> 2. Sort by this concatenated value, then apply a secondary sort by visit_page_num .
> 3. If a hit is not the last one in a visit, subtract the post_cust_hit_time value from the subsequent hit's post_cust_hit_time value.
> 4. This number is the amount of time spent (in seconds) for the hit. Filters can be applied to focus on dimension values or events.

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
