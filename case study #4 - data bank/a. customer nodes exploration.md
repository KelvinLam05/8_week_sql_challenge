**1. How many unique nodes are there on the Data Bank system?**

```` sql

SELECT 
  COUNT(DISTINCT node_id)
FROM customer_nodes;

````

| count |
| ----- |
| 5     |

<br/>

**2. What is the number of nodes per region?**

```` sql

SELECT 
  region_id, 
  region_name, 
  COUNT(node_id) AS number_of_nodes
FROM customer_nodes cn
JOIN regions r USING(region_id)
GROUP BY 
  region_id, 
  region_name
ORDER BY region_id;

````

| region_id | region_name | number_of_nodes |
| --------- | ----------- | --------------- |
| 1         | Australia   | 770             |
| 2         | America     | 735             |
| 3         | Africa      | 714             |
| 4         | Asia        | 665             |
| 5         | Europe      | 616             |

<br/>

**3. How many customers are allocated to each region?**

```` sql

SELECT 
  region_id, 
  region_name, 
  COUNT(DISTINCT customer_id) AS number_of_customers
FROM customer_nodes cn
JOIN regions r USING(region_id)
GROUP BY 
  region_id, 
  region_name;
 
````

| region_id | region_name | number_of_customers |
| --------- | ----------- | ------------------- |
| 1         | Australia   | 110                 |
| 2         | America     | 105                 |
| 3         | Africa      | 102                 |
| 4         | Asia        | 95                  |
| 5         | Europe      | 88                  |

<br/>

**4. How many days on average are customers reallocated to a different node?**

```` sql

SELECT 
  ROUND(AVG(end_date - start_date)) AS average_days
FROM customer_nodes
WHERE end_date <> '9999-12-31';

````

| average_days |
| ------------ |
| 15           |

<br/>

**5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?**

```` sql

SELECT
  region_id,
  PERCENTILE_DISC(0.50) WITHIN GROUP (ORDER BY end_date - start_date) AS "50th percentile",
  PERCENTILE_DISC(0.80) WITHIN GROUP (ORDER BY end_date - start_date) AS "80th percentile",
  PERCENTILE_DISC(0.95) WITHIN GROUP (ORDER BY end_date - start_date) AS "95th percentile"
FROM data_bank.customer_nodes
WHERE end_date <> '9999-12-31'
GROUP BY region_id;

````

| region_id | 50th percentile | 80th percentile | 95th percentile |
| --------- | --------------- | --------------- | --------------- |
| 1         | 15              | 23              | 28              |
| 2         | 15              | 23              | 28              |
| 3         | 15              | 24              | 28              |
| 4         | 15              | 23              | 28              |
| 5         | 15              | 24              | 28              |
