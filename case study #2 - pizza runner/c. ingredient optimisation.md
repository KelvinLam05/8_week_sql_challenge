**1. What are the standard ingredients for each pizza?**

````sql

WITH toppings_cte AS
(

SELECT
  pizza_id,
  UNNEST(STRING_TO_ARRAY(toppings, ',')::INT[]) AS toppings
FROM pizza_runner.pizza_recipes
  
),

pizza_toppings_cte AS
(

SELECT
  pizza_id,
  STRING_AGG(topping_name, ', ') AS toppings
FROM toppings_cte AS tc
JOIN pizza_runner.pizza_toppings AS t
  ON tc.toppings = t.topping_id
GROUP BY pizza_id

)

SELECT 
  pizza_name,
  toppings
FROM pizza_toppings_cte
JOIN pizza_runner.pizza_names AS n USING(pizza_id)

````

| pizza_name | toppings                                                              |
| ---------- | --------------------------------------------------------------------- |
| Meatlovers | Bacon, BBQ Sauce, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami |
| Vegetarian | Cheese, Mushrooms, Onions, Peppers, Tomatoes, Tomato Sauce            |

<br/>

**2. What was the most commonly added extra?**

````sql

WITH extras_cte AS
(

SELECT
  order_id,	
  UNNEST(STRING_TO_ARRAY(extras, ',')::INT[]) AS extras
FROM pizza_runner.customer_orders
WHERE extras <> 'null'
  
),

number_of_pizzas_cte AS
(

SELECT
  extras,
  COUNT(*) AS number_of_pizzas
FROM extras_cte
GROUP BY extras
  
)

SELECT
  topping_name,
  number_of_pizzas
FROM number_of_pizzas_cte AS nop
JOIN pizza_runner.pizza_toppings AS pt
  ON nop.extras = pt.topping_id
LIMIT 1;

````

| topping_name | number_of_pizzas |
| ------------ | ---------------- |
| Bacon        | 4                |

<br/>

**3. What was the most common exclusion?**

````sql

WITH exclusions_cte AS
(

SELECT
  order_id,	
  UNNEST(STRING_TO_ARRAY(exclusions, ',')::INT[]) AS exclusions
FROM pizza_runner.customer_orders
WHERE exclusions <> 'null'
  
),

number_of_pizzas_cte AS
(

SELECT
  exclusions,
  COUNT(*) AS number_of_pizzas
FROM exclusions_cte
GROUP BY exclusions
  
)

SELECT
  topping_name,
  number_of_pizzas
FROM number_of_pizzas_cte AS nop
JOIN pizza_runner.pizza_toppings AS pt
  ON nop.exclusions = pt.topping_id
ORDER BY number_of_pizzas DESC
LIMIT 1;

````

| topping_name | number_of_pizzas |
| ------------ | ---------------- |
| Cheese       | 4                |
