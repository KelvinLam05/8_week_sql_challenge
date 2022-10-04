**1. How many users are there?**

````sql

SELECT 
  COUNT(DISTINCT user_id) AS number_of_users
FROM clique_bait.users; 

````

| number_of_users |
| --------------- |
| 500             |

<br/>

**2. How many cookies does each user have on average?**

````sql

SELECT 
  COUNT(cookie_id) / COUNT(DISTINCT user_id) AS average_number_of_cookies_per_user
FROM clique_bait.users; 

````

| average_number_of_cookies_per_user |
| ---------------------------------- |
| 3                                  |

<br/>

**3. What is the unique number of visits by all users per month?**

````sql

WITH unique_number_of_visits_cte AS
(

SELECT
  TO_CHAR(event_time, 'MM') AS month_number,
  TO_CHAR(event_time, 'Month') AS month_name,
  COUNT(DISTINCT visit_id) AS unique_number_of_visits
FROM clique_bait.events 
GROUP BY 
  month_number,
  month_name
  
)

SELECT
  month_name,
  unique_number_of_visits
FROM unique_number_of_visits_cte

````

| month_name | unique_number_of_visits |
| ---------- | ----------------------- |
| January    | 876                     |
| February   | 1488                    |
| March      | 916                     |
| April      | 248                     |
| May        | 36                      |

<br/>

**4. What is the number of events for each event type?**

````sql

SELECT 
  event_name,
  COUNT(*) AS number_of_events 
FROM clique_bait.events 
JOIN clique_bait.event_identifier USING(event_type)
GROUP BY
  event_type,
  event_name
ORDER BY number_of_events DESC;

````

| event_name    | number_of_events |
| ------------- | ---------------- |
| Page View     | 20928            |
| Add to Cart   | 8451             |
| Purchase      | 1777             |
| Ad Impression | 876              |
| Ad Click      | 702              |

<br/>

**5. What is the percentage of visits which have a purchase event?**

````sql

SELECT
  ROUND(100.0 * COUNT(DISTINCT visit_id) / 
    (SELECT COUNT(DISTINCT visit_id) 
    FROM clique_bait.events), 1) AS percentage_purchase
FROM clique_bait.events
JOIN clique_bait.event_identifier USING(event_type)
WHERE event_type = 3;

````

| percentage_purchase |
| ------------------- |
| 49.9                |

<br/>

**6. What is the percentage of visits which view the checkout page but do not have a purchase event?**

**7. What are the top 3 pages by number of views?**

````sql

SELECT
  page_name,
  COUNT(*) AS number_of_views
FROM clique_bait.events  
JOIN clique_bait.page_hierarchy USING(page_id)
WHERE event_type = 1
GROUP BY
  page_id,
  page_name
ORDER BY number_of_views DESC
LIMIT 3;

````

| page_name    | number_of_views |
| ------------ | --------------- |
| All Products | 3174            |
| Checkout     | 2103            |
| Home Page    | 1782            |

<br/>

**8. What is the number of views and cart adds for each product category?**

````sql

SELECT
  product_category,
  SUM(CASE WHEN event_type = 1 THEN 1 ELSE 0 END) AS number_of_views,
  SUM(CASE WHEN event_type = 2 THEN 1 ELSE 0 END) AS number_of_cart_adds
FROM clique_bait.events  
JOIN clique_bait.page_hierarchy USING(page_id)
WHERE product_category IS NOT NULL
GROUP BY product_category;

````

| product_category | number_of_views | number_of_cart_adds |
| ---------------- | --------------- | ------------------- |
| Shellfish        | 6204            | 3792                |
| Fish             | 4633            | 2789                |
| Luxury           | 3032            | 1870                |

<br/>

**9. What are the top 3 products by purchases?**
