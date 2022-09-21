**1. How many pizzas were ordered?**

````sql

SELECT 
  COUNT(order_id) AS total_orders
FROM customer_orders;

````

| total_orders |
| ------------ |
| 14           |

<br/>

**2. How many unique customer orders were made?**

````sql

SELECT 
  COUNT(DISTINCT order_id) AS total_unique_orders 
FROM customer_orders;

````

| total_unique_orders |
| ------------------- |
| 10                  |

<br/>

**3. How many successful orders were delivered by each runner?**

````sql

SELECT 
  runner_id, 
  COUNT(order_id) AS successful_orders
FROM runner_orders
WHERE distance <> ''  
GROUP BY runner_id
ORDER BY runner_id;

````

| runner_id | successful_orders |
| --------- | ----------------- |
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |

<br/>

**4. How many of each type of pizza was delivered?**
