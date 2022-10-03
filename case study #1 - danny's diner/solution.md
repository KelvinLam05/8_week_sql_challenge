**1. What is the total amount each customer spent at the restaurant?**

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

<br/>

 **2. How many days has each customer visited the restaurant?**

````sql

SELECT 
  customer_id, 
  COUNT(DISTINCT order_date) AS total_visits
FROM sales s 
JOIN menu m USING (product_id)
GROUP BY customer_id;

````

| customer_id | total_visits |
| ----------- | ------------ |
| A           | 4            |
| B           | 6            |
| C           | 2            |

<br/>

**3. What was the first item from the menu purchased by each customer?**

````sql

WITH first_purchased_item_cte AS
(

SELECT 
  customer_id, 
  order_date, 
  product_name, 
  DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date)
FROM sales s
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

<br/>

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

````sql

SELECT 
  product_name,
  COUNT(product_id) AS most_purchased
FROM sales s
JOIN menu m USING(product_id)
GROUP BY product_name
LIMIT 1;

````

| product_name | most_purchased |
| ------------ | -------------- |
| ramen        | 8              |

<br/>

**5. Which item was the most popular for each customer?**

````sql

WITH total_purchase_quantity_cte AS
(

SELECT 
  customer_id,
  product_name,
  COUNT(*) AS total_purchase_quantity
FROM dannys_diner.sales
JOIN dannys_diner.menu USING(product_id)
GROUP BY
  customer_id,
  product_id,
  product_name
  
),

ranking_cte AS
(

SELECT
  customer_id,
  product_name,
  total_purchase_quantity,
  DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY total_purchase_quantity DESC) AS ranking
FROM total_purchase_quantity_cte
  
)

SELECT
  customer_id,
  product_name,
  total_purchase_quantity
FROM ranking_cte
WHERE ranking = 1;    

````

**6. Which item was purchased first by the customer after they became a member?**

````sql

WITH first_item_purchased_by_members AS
(

SELECT 
  customer_id,
  join_date,
  order_date,
  product_id,
  DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date)
FROM sales s
JOIN members m USING(customer_id)
WHERE order_date >= join_date
  
)

SELECT customer_id, order_date, product_name
FROM first_item_purchased_by_members f
JOIN menu m USING(product_id)
WHERE dense_rank = 1
ORDER BY customer_id;

````

| customer_id | order_date               | product_name |
| ----------- | ------------------------ | ------------ |
| A           | 2021-01-07T00:00:00.000Z | curry        |
| B           | 2021-01-11T00:00:00.000Z | sushi        |

<br/>

**7. Which item was purchased just before the customer became a member?**

````sql

WITH prior_customers_purchased AS
(

SELECT 
  customer_id,
  join_date,
  order_date,
  product_id,
  DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date DESC)
FROM sales s
JOIN members m USING(customer_id)
WHERE order_date < join_date

)

SELECT customer_id, order_date, product_name
FROM prior_customers_purchased p
JOIN menu m USING(product_id)
WHERE dense_rank = 1
ORDER BY customer_id;

````

| customer_id | order_date               | product_name |
| ----------- | ------------------------ | ------------ |
| A           | 2021-01-01T00:00:00.000Z | sushi        |
| A           | 2021-01-01T00:00:00.000Z | curry        |
| B           | 2021-01-04T00:00:00.000Z | sushi        |

<br/>

**8. What is the total items and amount spent for each member before they became a member?**

````sql

SELECT 
  customer_id,
  COUNT(product_id) AS items_purchased,
  SUM(price) AS total_spend
FROM sales AS s
JOIN members AS mm USING(customer_id)
JOIN menu AS mn USING(product_id)
WHERE order_date < join_date
GROUP BY customer_id
ORDER BY customer_id;

````

| customer_id | items_purchased | total_spend |
| ----------- | --------------- | ----------- |
| A           | 2               | 25          |
| B           | 3               | 40          |

<br/>

**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**


