**1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**

````sql

SELECT 
  TO_CHAR(registration_date, 'W') AS registration_week,
  COUNT(runner_id) AS runner_sign_up
FROM runners
GROUP BY TO_CHAR(registration_date, 'W')
ORDER BY registration_week;

````

| registration_week | runner_sign_up |
| ----------------- | -------------- |
| 1                 | 2              |
| 2                 | 1              |
| 3                 | 1              |

<br/>

**2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**
