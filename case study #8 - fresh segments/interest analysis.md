**1. Which interests have been present in all month_year dates in our dataset?**

```` sql

WITH months_cte AS
(

SELECT
  interest_id,
  COUNT(*) AS months
FROM fresh_segments.interest_metrics
WHERE interest_id IS NOT NULL
GROUP BY interest_id
  
),

interest_count_cte AS
(

SELECT
  months,
  COUNT(*) AS interest_count
FROM months_cte
GROUP BY months

)

SELECT
  months,
  interest_count
FROM interest_count_cte
WHERE months = 14;

````

| months | interest_count |
| ------ | -------------- |
| 14     | 480            |

<br/>

**2. Using this same total_months measure - calculate the cumulative percentage of all records starting at 14 months - which total_months value passes the 90% cumulative percentage value?**

```` sql

WITH months_cte AS
(

SELECT
  interest_id,
  COUNT(*) AS months
FROM fresh_segments.interest_metrics
WHERE interest_id IS NOT NULL
GROUP BY interest_id
  
),

interest_count_cte AS
(

SELECT
  months,
  COUNT(*) AS interest_count
FROM months_cte
GROUP BY months
  
),

cumulative_percentage_cte AS
(

SELECT
  months,
  interest_count,
  ROUND(100.0 * SUM(interest_count) OVER (ORDER BY months DESC) / SUM(interest_count) OVER (), 1) AS cumulative_percentage
FROM interest_count_cte
ORDER BY months DESC

)

SELECT
  months,
  interest_count,
  cumulative_percentage
FROM cumulative_percentage_cte
WHERE cumulative_percentage <= 91.00
ORDER BY months DESC;

````

| months | interest_count | cumulative_percentage |
| ------ | -------------- | --------------------- |
| 14     | 480            | 39.9                  |
| 13     | 82             | 46.8                  |
| 12     | 65             | 52.2                  |
| 11     | 95             | 60.1                  |
| 10     | 85             | 67.1                  |
| 9      | 95             | 75.0                  |
| 8      | 67             | 80.6                  |
| 7      | 90             | 88.1                  |
| 6      | 33             | 90.8                  |
