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

