**1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**

````sql

SELECT 
  TO_CHAR(registration_date, 'W') AS registration_week,
  COUNT(runner_id) AS runner_sign_up
FROM runners
GROUP BY TO_CHAR(registration_date, 'W')
ORDER BY registration_week;

````

| registration_week | runner_sign_up |
| ----------------- | -------------- |
| 1                 | 2              |
| 2                 | 1              |
| 3                 | 1              |

<br/>

**2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**

````sql

SELECT
  runner_id,
  ROUND(AVG(DATE_PART('minute', CAST(pickup_time AS TIMESTAMP) - order_time))) AS average_pickup_time_in_minutes 
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
WHERE pickup_time <> 'null'
GROUP BY runner_id
ORDER BY runner_id;

````

| runner_id | average_pickup_time_in_minutes |
| --------- | ------------------------------ |
| 1         | 15                             |
| 2         | 23                             |
| 3         | 10                             |

<br/>

**3. Is there any relationship between the number of pizzas and how long the order takes to prepare?**

````sql

WITH preparation_time_cte AS
(

SELECT 
  order_id,
  SUM(DATE_PART('minute', CAST(pickup_time AS TIMESTAMP) - order_time)) AS preparation_time, 
  COUNT(pizza_id) AS number_of_pizzas 
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
WHERE pickup_time <> 'null'
GROUP BY order_id

)

SELECT
  order_id,
  preparation_time,
  number_of_pizzas,
  preparation_time / number_of_pizzas AS average_preparation_time_per_pizza
FROM preparation_time_cte
ORDER BY average_preparation_time_per_pizza DESC;

````

| order_id | preparation_time | number_of_pizzas | average_preparation_time_per_pizza |
| -------- | ---------------- | ---------------- | ---------------------------------- |
| 4        | 87               | 3                | 29                                 |
| 3        | 42               | 2                | 21                                 |
| 8        | 20               | 1                | 20                                 |
| 10       | 30               | 2                | 15                                 |
| 5        | 10               | 1                | 10                                 |
| 2        | 10               | 1                | 10                                 |
| 1        | 10               | 1                | 10                                 |
| 7        | 10               | 1                | 10                                 |
