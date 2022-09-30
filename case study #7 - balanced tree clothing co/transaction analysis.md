**1. How many unique transactions were there?**

````sql

SELECT 
  COUNT(DISTINCT txn_id) AS unique_transactions
FROM balanced_tree.sales;

````

| unique_transactions |
| ------------------- |
| 2500                |

<br/>

**2. What is the average unique products purchased in each transaction?**

````sql

WITH unique_products_purchased_cte AS
(

SELECT
  txn_id,
  COUNT(DISTINCT prod_id) AS unique_products_purchased
FROM balanced_tree.sales
GROUP BY txn_id

)

SELECT 
  ROUND(AVG(unique_products_purchased)) AS average_unique_products_purchased
FROM unique_products_purchased_cte;

````

| average_unique_products_purchased |
| --------------------------------- |
| 6                                 |

<br/>

**3. What are the 25th, 50th and 75th percentile values for the revenue per transaction?**

 ````sql
 
WITH revenue_per_transaction_cte AS
(

SELECT
  txn_id,
  SUM(qty * price) AS revenue
FROM balanced_tree.sales
GROUP BY txn_id
  
)

SELECT
  PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY revenue) AS "25th percentile",
  PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY revenue) AS "50th percentile",
  PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY revenue) AS "75th percentile"
FROM revenue_per_transaction_cte;

````

| 25th percentile | 50th percentile | 75th percentile |
| --------------- | --------------- | --------------- |
| 375             | 509             | 647             |

<br/>

**4. What is the average discount value per transaction?**

 ````sql

WITH discount_value_per_transaction AS
(

SELECT
  txn_id,
  SUM((qty * price) * (discount * 0.01)) AS total_discount_amount
FROM balanced_tree.sales
GROUP BY txn_id

)

SELECT 
  ROUND(AVG(total_discount_amount), 2) AS average_discount_value
FROM discount_value_per_transaction;

````

| average_discount_value |
| ---------------------- |
| 62.49                  |

<br/>

**5. What is the percentage split of all transactions for members vs non-members?**

````sql

WITH members_vs_non_members_cte AS
(
  
SELECT
  txn_id,
  member
FROM balanced_tree.sales
GROUP BY 
  txn_id, 
  member

)

SELECT
  ROUND(100.0 * SUM(CASE WHEN member = 'true' THEN 1 ELSE 0 END) /
  COUNT(*), 1) AS member_transactions,
  ROUND(100.0 * SUM(CASE WHEN member = 'false' THEN 1 ELSE 0 END) / 
  COUNT(*), 1) AS non_member_transaction
FROM members_vs_non_members_cte;

````

| member_transactions | non_member_transaction |
| ------------------- | ---------------------- |
| 60.2                | 39.8                   |

<br/>

**6. What is the average revenue for member transactions and non-member transactions?**

````sql

WITH revenue_cte AS
(

SELECT
  txn_id,
  member,
  SUM(qty * price) AS revenue
FROM balanced_tree.sales
GROUP BY 
  txn_id, 
  member
  
)

SELECT
  ROUND(SUM(CASE WHEN member = 'true' THEN revenue ELSE 0 END) / SUM(CASE WHEN member = 'true' THEN 1 ELSE 0 END), 2) AS average_revenue_for_member_transactions,
  ROUND(SUM(CASE WHEN member = 'false' THEN revenue ELSE 0 END) / SUM(CASE WHEN member = 'false' THEN 1 ELSE 0 END), 2) AS average_revenue_for_non_member_transactions
FROM revenue_cte;

````

| average_revenue_for_member_transactions | average_revenue_for_non_member_transactions |
| --------------------------------------- | ------------------------------------------- |
| 516.27                                  | 515.04                                      |
