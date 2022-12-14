**1. How many customers has Foodie-Fi ever had?**

```` sql

SELECT 
  COUNT (DISTINCT customer_id)
FROM subscriptions; 

````

| count |
| ----- |
| 1000  |

<br/>

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

<br/>

**3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name**

```` sql

SELECT 
  plan_id, 
  plan_name,
  COUNT (*) AS events
FROM subscriptions s
JOIN plans p USING (plan_id)
WHERE start_date >= '2021-01-01'
GROUP BY 
  plan_id, 
  plan_name
ORDER BY plan_id;

````

| plan_id | plan_name     | events |
| ------- | ------------- | ------ |
| 1       | basic monthly | 8      |
| 2       | pro monthly   | 60     |
| 3       | pro annual    | 63     |
| 4       | churn         | 71     |

<br/>

**4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?**

```` sql

SELECT 
  COUNT(customer_id) AS churners,
  ROUND(100 * COUNT(customer_id)::NUMERIC / 
    (SELECT COUNT(DISTINCT customer_id)
    FROM subscriptions), 1) AS percentage_of_churners
FROM subscriptions s
JOIN plans p USING(plan_id)
WHERE plan_id = 4;

````

| churners | percentage_of_churners |
| -------- | ---------------------- |
| 307      | 30.7                   |

<br/>

**5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?**

```` sql

WITH churned AS 
(

SELECT 
  *,  
  ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY plan_id)
FROM subscriptions s
JOIN plans p USING(plan_id)

)

SELECT 
  COUNT(*) AS churners,
  ROUND(100 * COUNT(*)::NUMERIC / 
    (SELECT COUNT(DISTINCT customer_id) 
    FROM subscriptions), 0) AS percentage_of_churners
FROM churned
WHERE plan_id = 4 AND row_number = 2;

````

| churners | percentage_of_churners |
| -------- | ---------------------- |
| 92       | 9                      |

<br/>

**6. What is the number and percentage of customer plans after their initial free trial?**

```` sql

WITH next_plan_cte AS
(
  
SELECT 
  customer_id, 
  plan_id, 
  LEAD(plan_id, 1) OVER(PARTITION BY customer_id 
                        ORDER BY plan_id) 
                        AS next_plan
FROM subscriptions
  
)

SELECT 
  next_plan, 
  COUNT(*) AS conversions,
  ROUND(100 * COUNT(*)::NUMERIC / 
    (SELECT COUNT(DISTINCT customer_id) 
    FROM subscriptions), 1) AS conversion_rate
FROM next_plan_cte
WHERE plan_id = 0 AND next_plan IS NOT NULL
GROUP BY next_plan;

````

| next_plan | conversions | conversion_rate |
| --------- | ----------- | --------------- |
| 1         | 546         | 54.6            |
| 2         | 325         | 32.5            |
| 3         | 37          | 3.7             |
| 4         | 92          | 9.2             |

<br/>

**8. How many customers have upgraded to an annual plan in 2020?**

```` sql

SELECT 
  COUNT(DISTINCT customer_id) AS number_of_customers
FROM subscriptions
WHERE plan_id = 3 AND start_date <= '2020-12-31';

````

| number_of_customers |
| ------------------- |
| 195                 |

<br/>

**9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?**

```` sql

WITH trial_plan AS
(
  
SELECT 
  customer_id, 
  start_date AS trial_date
FROM subscriptions
WHERE plan_id = 0
  
),

annual_plan AS
(
  
SELECT
  customer_id, 
  start_date AS annual_date
FROM subscriptions
WHERE plan_id = 3
  
)

SELECT 
  ROUND(AVG(annual_date - trial_date), 0) AS avg_days_to_upgrade
FROM trial_plan tp
JOIN annual_plan ap USING(customer_id);

````

| avg_days_to_upgrade |
| ------------------- |
| 105                 |

<br/>

**11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?**

```` sql

customer_id, 
  plan_id, 
  start_date,
  LEAD(plan_id, 1) OVER(PARTITION BY customer_id 
                        ORDER BY plan_id) 
                        AS next_plan
FROM subscriptions

)

SELECT 
  COUNT(*) AS downgraded
FROM next_plan_cte
WHERE start_date <= '2020-12-31' AND plan_id = 2 AND next_plan = 1;

````

| downgraded |
| ---------- |
| 0          |
