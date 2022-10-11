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

<br/>

**5. Summarise the id values in the fresh_segments.interest_map by its total record count in this table**

```` sql

SELECT 
  COUNT(id) AS record_count  
FROM fresh_segments.interest_map;

````

| record_count |
| ------------ |
| 1209         |

<br/>

**6. What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where 'interest_id = 21246' in your joined output and include all columns from fresh_segments.interest_metrics and all columns from fresh_segments.interest_map except from the id column.**

```` sql

SELECT 
  interest_id,
  interest_name,
  interest_summary,
  created_at,
  last_modified,
  _month,	
  _year,	
  month_year,	
  composition,
  index_value,
  ranking,
  percentile_ranking
FROM fresh_segments.interest_map ma
JOIN fresh_segments.interest_metrics me
	ON ma.id = CAST(me.interest_id AS INT)
WHERE interest_id = '21246' AND month_year IS NOT NULL;

````

| interest_id | interest_name                    | interest_summary                                      | created_at               | last_modified            | _month | _year | month_year | composition | index_value | ranking | percentile_ranking |
| ----------- | -------------------------------- | ----------------------------------------------------- | ------------------------ | ------------------------ | ------ | ----- | ---------- | ----------- | ----------- | ------- | ------------------ |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 7      | 2018  | 07-2018    | 2.26        | 0.65        | 722     | 0.96               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 8      | 2018  | 08-2018    | 2.13        | 0.59        | 765     | 0.26               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 9      | 2018  | 09-2018    | 2.06        | 0.61        | 774     | 0.77               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 10     | 2018  | 10-2018    | 1.74        | 0.58        | 855     | 0.23               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 11     | 2018  | 11-2018    | 2.25        | 0.78        | 908     | 2.16               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 12     | 2018  | 12-2018    | 1.97        | 0.7         | 983     | 1.21               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 1      | 2019  | 01-2019    | 2.05        | 0.76        | 954     | 1.95               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 2      | 2019  | 02-2019    | 1.84        | 0.68        | 1109    | 1.07               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 3      | 2019  | 03-2019    | 1.75        | 0.67        | 1123    | 1.14               |
| 21246       | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 4      | 2019  | 04-2019    | 1.58        | 0.63        | 1092    | 0.64               |
