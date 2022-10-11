**1. Update the fresh_segments.interest_metrics table by modifying the month_year column to be a date data type with the start of the month**

```` sql

SELECT
  TO_CHAR(TO_DATE(month_year, 'MM-YYYY'), 'YYYY-MM') AS month_year
FROM fresh_segments.interest_metrics

````

<br/>

**2. What is count of records in the fresh_segments.interest_metrics for each month_year value sorted in chronological order (earliest to latest) with the null values appearing first?**

```` sql

SELECT
  TO_CHAR(TO_DATE(month_year, 'MM-YYYY'), 'YYYY-MM') AS month_year,
  COUNT(*) AS record_count
FROM fresh_segments.interest_metrics
GROUP BY TO_CHAR(TO_DATE(month_year, 'MM-YYYY'), 'YYYY-MM')
ORDER BY month_year ASC NULLS FIRST;

````

| month_year | record_count |
| ---------- | ------------ |
|            | 1194         |
| 2018-07    | 729          |
| 2018-08    | 767          |
| 2018-09    | 780          |
| 2018-10    | 857          |
| 2018-11    | 928          |
| 2018-12    | 995          |
| 2019-01    | 973          |
| 2019-02    | 1121         |
| 2019-03    | 1136         |
| 2019-04    | 1099         |
| 2019-05    | 857          |
| 2019-06    | 824          |
| 2019-07    | 864          |
| 2019-08    | 1149         |

<br/>

**3. What do you think we should do with these null values in the fresh_segments.interest_metrics**

The null values appear in _month, _year, month_year, and interest_id. The corresponding values in composition, index_value, ranking, and percentile_ranking fields are not meaningful without the specific information on interest_id and dates.

<br/>

**4. How many interest_id values exist in the fresh_segments.interest_metrics table but not in the fresh_segments.interest_map table? What about the other way around?**

```` sql

SELECT
  CAST(interest_id AS INTEGER) AS not_in_interest_metrics
FROM fresh_segments.interest_metrics
EXCEPT
SELECT 
  id  
FROM fresh_segments.interest_map;

````

| not_in_interest_metrics |
| ----------------------- |
|                         |

<br/>

```` sql

SELECT 
  id AS not_in_interest_map  
FROM fresh_segments.interest_map
EXCEPT
SELECT
  CAST(interest_id AS INTEGER) AS interest_id
FROM fresh_segments.interest_metrics;

````

| not_in_interest_map |
| ------------------- |
| 42400               |
| 47789               |
| 35964               |
| 40185               |
| 19598               |
| 40186               |
| 42010               |

There are 7 ids that did not appear in interest_metrics.
