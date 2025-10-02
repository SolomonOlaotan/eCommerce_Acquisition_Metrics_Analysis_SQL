# Average Order Value

## This is a key metric that measures sales performance and customer buying habits.

### SQL

--- average order value---

WITH average_order AS (
  SELECT 
    COUNT(o.order_id) AS number_of_orders,
    SUM(oi.sale_price * o.num_of_item) AS total_revenue,



FROM 
  bigquery-public-data.thelook_ecommerce.orders o
JOIN
 bigquery-public-data.thelook_ecommerce.order_items oi
 ON o.order_id = oi.id
WHERE o.status = 'Complete'
--WHERE o.status NOT IN( 'Cancelled', 'Returned', 'Processing') 
)
SELECT
ROUND(total_revenue / number_of_orders, 0) AS average_order_value
FROM
  average_order
