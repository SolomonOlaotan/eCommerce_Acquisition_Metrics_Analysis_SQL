# The First Product Customer Purchased

## This query identifies the products that customers purchased first after registration.

### SQL query

--customer acquisition by first product purchased
WITH first_purchase AS(
   --product bought by each customer
   SELECT
    o.user_id,
    o.order_id,
    oi.product_id,
    ROW_NUMBER() OVER(PARTITION BY o.user_id ORDER BY o.created_at) AS purchase_order
FROM
   bigquery-public-data.thelook_ecommerce.orders o
JOIN 
    bigquery-public-data.thelook_ecommerce.order_items oi
  ON o.order_id = oi.order_id
 ),
first_product AS (
  --first product bought by each user/customer
  SELECT 
    fp.user_id,
    fp.product_id
  FROM 
    first_purchase fp
  WHERE 
    fp.purchase_order = 1
),
product_names AS (
  -- Get the category name for each product
  SELECT 
    p.id,
    p.category
  FROM 
    bigquery-public-data.thelook_ecommerce.products p
)
SELECT
  pc.category,
  COUNT(DISTINCT fp.user_id) AS users_in_category
FROM
  first_product fp
JOIN 
  product_names pc
ON 
  fp.product_id = pc.id
GROUP BY 
  pc.category
ORDER BY 
  users_in_category DESC
