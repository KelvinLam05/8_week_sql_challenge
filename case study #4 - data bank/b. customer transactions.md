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
