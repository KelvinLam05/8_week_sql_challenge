**1. What was the total quantity sold for all products?**

````sql

SELECT 
    SUM(qty) AS total_quantity_sold
FROM balanced_tree.sales

````

| total_quantity_sold |
| ------------------- |
| 45216               |

<br/>

**2. What is the total generated revenue for all products before discounts?**

````sql

SELECT 
    SUM(qty * price) AS total_revenue
FROM balanced_tree.sales

````

| total_revenue |
| ------------- |
| 1289453       |

<br/>

**3. What was the total discount amount for all products?**

````sql

SELECT 
    ROUND(SUM((qty * price) * (discount * 0.01))) AS total_discount_amount
FROM balanced_tree.sales

````

| total_discount_amount |
| --------------------- |
| 156229                |

