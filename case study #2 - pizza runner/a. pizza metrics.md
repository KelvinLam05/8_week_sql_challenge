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

````sql

SELECT 
  pizza_name, 
  COUNT(pizza_id) AS orders
FROM runner_orders r
JOIN customer_orders c USING(order_id)
JOIN pizza_names p USING(pizza_id)
WHERE distance <> ''
GROUP BY pizza_name;

````

| pizza_name | orders |
| ---------- | ------ |
| Meatlovers | 9      |
| Vegetarian | 3      |

<br/>

**5. How many Vegetarian and Meatlovers were ordered by each customer?**

````sql

SELECT 
  customer_id, 
  pizza_name,
  COUNT(pizza_name) AS orders
FROM customer_orders c 
JOIN pizza_names p USING(pizza_id)
GROUP BY 
  customer_id, 
  pizza_name
ORDER BY customer_id;

````

| customer_id | pizza_name | orders |
| ----------- | ---------- | ------ |
| 101         | Meatlovers | 2      |
| 101         | Vegetarian | 1      |
| 102         | Meatlovers | 2      |
| 102         | Vegetarian | 1      |
| 103         | Meatlovers | 3      |
| 103         | Vegetarian | 1      |
| 104         | Meatlovers | 3      |
| 105         | Vegetarian | 1      |

<br/>

**6. What was the maximum number of pizzas delivered in a single order?**

````sql

WITH largest_single_order_cte AS
(
  
SELECT 
  order_id, 
  COUNT(pizza_id) AS order_size
FROM customer_orders AS c
JOIN runner_orders AS r USING(order_id)
GROUP BY order_id

)

SELECT MAX(order_size) AS largest_single_order
FROM largest_single_order_cte;

````

| largest_single_order |
| -------------------- |
| 3                    |

<br/>

**7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**

````sql

SELECT 
  customer_id,
  SUM(CASE WHEN exclusions <> '' OR extras <> '' THEN 1
      ELSE 0
      END) AS at_least_1_change,
  SUM(CASE WHEN exclusions = '' AND extras = '' THEN 1
      ELSE 0
      END) AS no_change
FROM customer_orders c
JOIN runner_orders r USING(order_id)
WHERE distance <> ''
GROUP BY customer_id
ORDER BY customer_id;

````

| customer_id | at_least_1_change | no_change |
| ----------- | ----------------- | --------- |
| 101         | 0                 | 2         |
| 102         | 0                 | 3         |
| 103         | 3                 | 0         |
| 104         | 2                 | 1         |
| 105         | 1                 | 0         |

<br/>

**8. How many pizzas were delivered that had both exclusions and extras?**

````sql

SELECT 
  SUM(CASE WHEN exclusions <> '' AND extras <> '' THEN 1
      ELSE 0
      END) AS both_exclusions_and_extras
FROM customer_orders c
JOIN runner_orders r USING(order_id)
WHERE distance <> '' AND exclusions <> '' AND extras <> '';

````

| both_exclusions_and_extras |
| -------------------------- |
| 1                          |

<br/>

**9. What was the total volume of pizzas ordered for each hour of the day?**

````sql

SELECT 
  DATE_PART('hour', order_time) AS hour_of_the_day,
  COUNT(order_id) AS total_volume_of_pizzas_ordered
FROM customer_orders 
GROUP BY DATE_PART('hour', order_time)
ORDER BY hour_of_the_day;

````

| hour_of_the_day | total_volume_of_pizzas_ordered |
| --------------- | ------------------------------ |
| 11              | 1                              |
| 13              | 3                              |
| 18              | 3                              |
| 19              | 1                              |
| 21              | 3                              |
| 23              | 3                              |

<br/>

**10. What was the volume of orders for each day of the week?**

````sql

SELECT 
  TO_CHAR(order_time, 'Day') AS day_of_the_week,
  COUNT(order_id) AS total_volume_of_pizzas_ordered
FROM customer_orders 
GROUP BY TO_CHAR(order_time, 'Day')
ORDER BY day_of_the_week;

````

| day_of_the_week | total_volume_of_pizzas_ordered |
| --------------- | ------------------------------ |
| Friday          | 1                              |
| Saturday        | 5                              |
| Thursday        | 3                              |
| Wednesday       | 5                              |

