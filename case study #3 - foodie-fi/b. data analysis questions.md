**1. How many customers has Foodie-Fi ever had?**

```` sql

SELECT 
  COUNT (DISTINCT customer_id)
FROM subscriptions; 

````

| count |
| ----- |
| 1000  |

**2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value**

```` sql

SELECT 
  DATE_PART('month', start_date) AS month_number, 
  TO_CHAR(start_date, 'Month') AS month_name,
  COUNT(*) AS trial_subscriptions
FROM subscriptions s
JOIN plans p USING (plan_id)
WHERE plan_id = 0
GROUP BY 
  DATE_PART('month', start_date), 
  TO_CHAR(start_date, 'Month')
ORDER BY month_number;

````

| month_number | month_name | trial_subscriptions |
| ------------ | ---------- | ------------------- |
| 1            | January    | 88                  |
| 2            | February   | 68                  |
| 3            | March      | 94                  |
| 4            | April      | 81                  |
| 5            | May        | 88                  |
| 6            | June       | 79                  |
| 7            | July       | 89                  |
| 8            | August     | 88                  |
| 9            | September  | 87                  |
| 10           | October    | 79                  |
| 11           | November   | 75                  |
| 12           | December   | 84                  |



