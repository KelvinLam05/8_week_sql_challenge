### 1. What is the total amount each customer spent at the restaurant?

````sql

SELECT 
  customer_id, 
  SUM(price) AS total_spend
FROM sales s 
JOIN menu m USING (product_id)
GROUP BY customer_id
ORDER BY customer_id;

````

| customer_id | total_spend |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

### 2. How many days has each customer visited the restaurant?

````sql

SELECT 
  customer_id, 
  COUNT (DISTINCT order_date) AS total_visits
FROM sales s 
JOIN menu m USING (product_id)
GROUP BY customer_id;

````

| customer_id | total_visits |
| ----------- | ------------ |
| A           | 4            |
| B           | 6            |
| C           | 2            |

### 3. What was the first item from the menu purchased by each customer?

````sql

WITH first_purchased_item_cte AS
(

SELECT 
  customer_id, 
  order_date, 
  product_name, 
  DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date)
FROM sales AS s
JOIN menu m USING(product_id)

)

SELECT 
  customer_id,
  product_name
FROM first_purchased_item_cte
WHERE dense_rank = 1
GROUP BY 
  customer_id, 
  product_name;

````

| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**


