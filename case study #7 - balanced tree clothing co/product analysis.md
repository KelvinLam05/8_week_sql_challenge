**1. What are the top 3 products by total revenue before discount?**

````sql

WITH revenue_cte AS
(

SELECT
  prod_id,
  SUM(qty * price) AS revenue
FROM balanced_tree.sales
GROUP BY prod_id

)

SELECT 
  product_name,
  revenue
FROM revenue_cte AS r
JOIN balanced_tree.product_details AS d
  ON r.prod_id = d.product_id
ORDER BY revenue DESC
LIMIT 3;

````

| product_name                 | revenue |
| ---------------------------- | ------- |
| Blue Polo Shirt - Mens       | 217683  |
| Grey Fashion Jacket - Womens | 209304  |
| White Tee Shirt - Mens       | 152000  |

<br/>

**2. What is the total quantity, revenue and discount for each segment?**

````sql

SELECT
  segment_name,
  SUM(qty) AS total_quantity,
  SUM(qty * s.price) AS total_revenue,
  SUM(qty * s.price * discount * 0.01) AS total_discount_amount
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
  ON s.prod_id = d.product_id
GROUP BY 
  segment_id,
  segment_name
ORDER BY total_revenue DESC;

````

| segment_name | total_quantity | total_revenue | total_discount_amount |
| ------------ | -------------- | ------------- | --------------------- |
| Shirt        | 11265          | 406143        | 49594.27              |
| Jacket       | 11385          | 366983        | 44277.46              |
| Socks        | 11217          | 307977        | 37013.44              |
| Jeans        | 11349          | 208350        | 25343.97              |

<br/>

**3. What is the top selling product for each segment?**

````sql

````
