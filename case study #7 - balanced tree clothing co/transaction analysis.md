**1. How many unique transactions were there?**

````sql

SELECT 
  COUNT(DISTINCT txn_id) AS unique_transactions
FROM balanced_tree.sales

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
