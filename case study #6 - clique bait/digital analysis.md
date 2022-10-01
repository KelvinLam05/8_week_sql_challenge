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
