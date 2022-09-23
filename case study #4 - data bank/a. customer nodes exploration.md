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
  ROUND(AVG("end_date" - "start_date")) AS average_days
FROM customer_nodes
WHERE end_date <> '9999-12-31';

````

| average_days |
| ------------ |
| 15           |
