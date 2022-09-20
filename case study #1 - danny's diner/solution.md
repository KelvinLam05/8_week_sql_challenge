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
