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


