**1. What is the unique count and total amount for each transaction type?**

```` sql

SELECT
  txn_type,
  COUNT(DISTINCT customer_id) AS unique_count,
  SUM(txn_amount) AS total_amount
FROM data_bank.customer_transactions
GROUP BY txn_type;

````

| txn_type   | unique_count | total_amount |
| ---------- | ------------ | ------------ |
| deposit    | 500          | 1359168      |
| purchase   | 448          | 806537       |
| withdrawal | 439          | 793003       |

<br/>

**2. What is the average total historical deposit counts and amounts for all customers?**

```` sql

SELECT
  COUNT(*) / COUNT(DISTINCT customer_id) AS average_deposit_count,
  ROUND(AVG(txn_amount), 2) AS average_amount
FROM data_bank.customer_transactions
WHERE txn_type = 'deposit'
GROUP BY txn_type;

````

| average_deposit_count | average_amount |
| --------------------- | -------------- |
| 5                     | 508.86         |

<br/>

**3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?**

```` sql

WITH month_number_cte AS
(
  
SELECT	
  DATE_PART('month', txn_date) AS month_number,
  customer_id,
  COUNT(*) FILTER (WHERE txn_type = 'deposit') AS deposit_count,
  COUNT(*) FILTER (WHERE txn_type = 'purchase') AS purchase_count,
  COUNT(*) FILTER (WHERE txn_type = 'withdrawal') AS withdrawal_count
FROM data_bank.customer_transactions
GROUP BY 
  DATE_PART('month', txn_date), 
  customer_id
  
),

count_cte AS
(

SELECT
  month_number,
  customer_id,
  deposit_count,
  purchase_count,
  withdrawal_count
FROM month_number_cte
WHERE deposit_count > 1 AND purchase_count = 1 OR deposit_count > 1 AND withdrawal_count = 1 

)

SELECT
  month_number,
  COUNT(*) AS "count"
FROM count_cte
GROUP BY month_number;

````

| month_number | count |
| ------------ | ----- |
| 1            | 115   |
| 2            | 108   |
| 3            | 113   |
| 4            | 50    |
