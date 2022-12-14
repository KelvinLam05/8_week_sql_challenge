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

SELECT 
  order_id,
  AVG(DATE_PART('minute', CAST(pickup_time AS TIMESTAMP) - order_time)) AS preparation_time_in_minutes,
  COUNT(order_id) AS number_of_pizzas, 
  ROUND(AVG(DATE_PART('minute', CAST(pickup_time AS TIMESTAMP) - order_time))::NUMERIC / COUNT(order_id)) AS average_time_per_pizza_in_minutes
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
WHERE pickup_time <> 'null'
GROUP BY order_id
ORDER BY preparation_time_in_minutes DESC;

````

| order_id | preparation_time_in_minutes | number_of_pizzas | average_time_per_pizza_in_minutes |
| -------- | --------------------------- | ---------------- | --------------------------------- |
| 4        | 29                          | 3                | 10                                |
| 3        | 21                          | 2                | 11                                |
| 8        | 20                          | 1                | 20                                |
| 10       | 15                          | 2                | 8                                 |
| 5        | 10                          | 1                | 10                                |
| 2        | 10                          | 1                | 10                                |
| 1        | 10                          | 1                | 10                                |
| 7        | 10                          | 1                | 10                                |

<br/>

**4. What was the average distance travelled for each customer?**

````sql

WITH distance_cte AS
(

SELECT
  customer_id,
  CASE WHEN distance LIKE '%km' THEN TRIM('km' FROM distance) ELSE distance END AS distance
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
WHERE distance <> 'null'

)

SELECT
  customer_id,
  ROUND(AVG(CAST(distance AS DECIMAL(4, 1))), 1) AS average_travel_distance_in_km 
FROM distance_cte
GROUP BY customer_id
ORDER BY customer_id;

````

| customer_id | average_travel_distance_in_km |
| ----------- | ----------------------------- |
| 101         | 20.0                          |
| 102         | 16.7                          |
| 103         | 23.4                          |
| 104         | 10.0                          |
| 105         | 25.0                          |

<br/>

**5. What was the difference between the longest and shortest delivery times for all orders?**

````sql

WITH duration_cte AS
(

SELECT
  order_id,
  TO_NUMBER(duration, '99') AS duration
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
WHERE duration <> 'null'
  
),

duration_per_order_cte AS
(

SELECT 
  order_id,
  ROUND(AVG(duration)) AS duration
FROM duration_cte
GROUP BY order_id

)

SELECT
  MAX(duration) AS longest_delivery_time_in_minutes,
  MIN(duration) AS shortest_delivery_time_in_minutes,
  MAX(duration) - MIN(duration) AS delivery_time_difference_in_minutes
FROM duration_per_order_cte;

````

| longest_delivery_time_in_minutes | shortest_delivery_time_in_minutes | delivery_time_difference_in_minutes |
| -------------------------------- | --------------------------------- | ----------------------------------- |
| 40                               | 10                                | 30                                  |

<br/>

**6. What was the average speed for each runner for each delivery and do you notice any trend for these values?**

````sql

WITH distance_and_duration_cte AS
(

SELECT
  order_id,
  runner_id,
  TO_NUMBER(distance, '99D9') AS distance,
  TO_NUMBER(duration, '99') AS duration
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
WHERE duration <> 'null'
  
),

hours_cte AS
(

SELECT 
  order_id,
  runner_id,
  ROUND(AVG(distance), 1) AS distance,
  ROUND(AVG(duration)) / 60 AS hours
FROM distance_and_duration_cte
GROUP BY 
  order_id,
  runner_id

)

SELECT
  order_id,
  runner_id,
  ROUND(distance / hours) AS runner_average_speed
FROM hours_cte
ORDER BY order_id;

````

| order_id | runner_id | runner_average_speed |
| -------- | --------- | -------------------- |
| 1        | 1         | 38                   |
| 2        | 1         | 44                   |
| 3        | 1         | 40                   |
| 4        | 2         | 35                   |
| 5        | 3         | 40                   |
| 7        | 2         | 60                   |
| 8        | 2         | 94                   |
| 10       | 1         | 60                   |

<br/>

**7. What is the successful delivery percentage for each runner?**

````sql

WITH total_delivery_cte AS
(

SELECT 
  runner_id,
  COUNT(*) AS total_delivery
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
GROUP BY runner_id

),

successful_delivery_cte AS
(
  
SELECT 
  runner_id,
  COUNT(*) AS successful_delivery
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r USING(order_id)
WHERE duration <> 'null'
GROUP BY runner_id

)

SELECT
  runner_id,
  successful_delivery,
  total_delivery,
  ROUND((100.0 * successful_delivery / total_delivery), 1) AS successful_delivery_percentage
FROM successful_delivery_cte 
JOIN total_delivery_cte USING(runner_id)
ORDER BY runner_id;

````

| runner_id | successful_delivery | total_delivery | successful_delivery_percentage |
| --------- | ------------------- | -------------- | ------------------------------ |
| 1         | 6                   | 6              | 100.0                          |
| 2         | 5                   | 6              | 83.3                           |
| 3         | 1                   | 2              | 50.0                           |
