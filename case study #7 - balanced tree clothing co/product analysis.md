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

WITH products_for_each_category AS
(

SELECT 
  category_name,
  product_name,
  SUM(qty) AS quantity
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
  ON s.prod_id = d.product_id
GROUP BY 
  category_name,
  product_name
  
),

ranking_cte AS
(

SELECT 
  category_name,
  product_name,
  quantity,
  DENSE_RANK() OVER(PARTITION BY category_name ORDER BY quantity DESC) AS ranking
FROM products_for_each_category

)

SELECT
  category_name,
  product_name,
  quantity
FROM ranking_cte
WHERE ranking = 1
ORDER BY quantity DESC;

````

| category_name | product_name                 | quantity |
| ------------- | ---------------------------- | -------- |
| Womens        | Grey Fashion Jacket - Womens | 3876     |
| Mens          | Blue Polo Shirt - Mens       | 3819     |

<br/>

**4. What is the total quantity, revenue and discount for each category?**

````sql

SELECT 
  category_name,
  SUM(qty) AS total_quantity, 
  SUM(qty * s.price) AS total_revenue,
  ROUND(SUM(qty * s.price * discount * 0.01)) AS total_discount 
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
  ON s.prod_id = d.product_id
GROUP BY 
  category_id,
  category_name;

````

| category_name | total_quantity | total_revenue | total_discount |
| ------------- | -------------- | ------------- | -------------- |
| Mens          | 22482          | 714120        | 86608          |
| Womens        | 22734          | 575333        | 69621          |

<br/>

**5. What is the top selling product for each category?**

````sql

WITH ranking_cte AS
(

SELECT
category_name,
product_name,
SUM(qty) AS total_quantity,
DENSE_RANK() OVER(PARTITION BY category_name ORDER BY SUM(qty) DESC) AS ranking
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
  ON s.prod_id = d.product_id
GROUP BY
  category_name,
  product_name

)

SELECT 
  category_name,
  product_name,
  total_quantity
FROM ranking_cte
WHERE ranking = 1; 

````

| category_name | product_name                 | total_quantity |
| ------------- | ---------------------------- | -------------- |
| Mens          | Blue Polo Shirt - Mens       | 3819           |
| Womens        | Grey Fashion Jacket - Womens | 3876           |

<br/>

**6. What is the percentage split of revenue by product for each segment?**

````sql

WITH products_for_each_segment AS
(

SELECT 
  segment_name,
  product_name,
  SUM(qty * s.price) AS revenue
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
  ON s.prod_id = d.product_id
GROUP BY 
  segment_name,
  product_name

)

SELECT
  segment_name,
  product_name,
  revenue,
  ROUND((revenue / SUM(revenue) OVER(PARTITION BY segment_name) * 100.0), 1) AS percentage
FROM products_for_each_segment;

````

| segment_name | product_name                     | revenue | percentage |
| ------------ | -------------------------------- | ------- | ---------- |
| Jacket       | Indigo Rain Jacket - Womens      | 71383   | 19.5       |
| Jacket       | Khaki Suit Jacket - Womens       | 86296   | 23.5       |
| Jacket       | Grey Fashion Jacket - Womens     | 209304  | 57.0       |
| Jeans        | Navy Oversized Jeans - Womens    | 50128   | 24.1       |
| Jeans        | Black Straight Jeans - Womens    | 121152  | 58.1       |
| Jeans        | Cream Relaxed Jeans - Womens     | 37070   | 17.8       |
| Shirt        | White Tee Shirt - Mens           | 152000  | 37.4       |
| Shirt        | Blue Polo Shirt - Mens           | 217683  | 53.6       |
| Shirt        | Teal Button Up Shirt - Mens      | 36460   | 9.0        |
| Socks        | Navy Solid Socks - Mens          | 136512  | 44.3       |
| Socks        | White Striped Socks - Mens       | 62135   | 20.2       |
| Socks        | Pink Fluro Polkadot Socks - Mens | 109330  | 35.5       |

<br/>

**7. What is the percentage split of revenue by segment for each category?**

````sql

WITH segments_for_each_category AS
(

SELECT
  category_name,  
  segment_name,
  SUM(qty * s.price) AS revenue
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
  ON s.prod_id = d.product_id
GROUP BY 
  category_name,	
  segment_name
  
)

SELECT
  category_name,
  segment_name,
  revenue,
  ROUND((revenue / SUM(revenue) OVER(PARTITION BY category_name) * 100.0), 1) AS percentage
FROM segments_for_each_category;

````

| category_name | segment_name | revenue | percentage |
| ------------- | ------------ | ------- | ---------- |
| Mens          | Socks        | 307977  | 43.1       |
| Mens          | Shirt        | 406143  | 56.9       |
| Womens        | Jeans        | 208350  | 36.2       |
| Womens        | Jacket       | 366983  | 63.8       |

<br/>

**8. What is the percentage split of total revenue by category?**

````sql

WITH revenue_distribution_by_category AS
(

SELECT
  category_name,  
  SUM(qty * s.price) AS revenue
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
  ON s.prod_id = d.product_id
GROUP BY category_name

)

SELECT 
  ROUND(100.0 * SUM(revenue) FILTER (WHERE category_name = 'Mens') / SUM(revenue) FILTER (WHERE category_name = 'Mens' OR category_name = 'Womens'), 1) AS mens_pct,
  ROUND(100.0 * SUM(revenue) FILTER (WHERE category_name = 'Womens') / SUM(revenue) FILTER (WHERE category_name = 'Mens' OR category_name = 'Womens'), 1) AS womens_pct
FROM revenue_distribution_by_category;

````

| mens_pct | womens_pct |
| -------- | ---------- |
| 55.4     | 44.6       |

**9. What is the total transaction “penetration” for each product?**
