#Conversion Rate

##This metric evaluates the fraction of users who actually registered from visitors

###The SQL query

--conversion rate
WITH customer_orders AS (
  --find the number of customers who made an order
  SELECT
    o.user_id
  FROM 
    bigquery-public-data.thelook_ecommerce.orders o  
  GROUP BY
    o.user_id
),
total_users AS(
  SELECT
    -- total number of registered customers
    COUNT(*) AS total_users
  FROM
    bigquery-public-data.thelook_ecommerce.users u
)
SELECT
COUNT(co.user_id) AS customer_with_order,
tu.total_users,
(COUNT(Co.user_id) / tu.total_users) * 100 AS conversion_rate
FROM  
  customer_orders co  
JOIN 
  total_users tu
  ON TRUE
GROUP BY
  tu.total_users
 
