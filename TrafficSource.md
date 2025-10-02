# Traffic Source
## This is a key metric that indicates different channels that drive customers to the company webpage.


--users acquisition and conversion % by traffic_source
WITH user_order AS(
  SELECT
    user_id,
    MIN(DATE(created_at)) AS first_order_date
  FROM
    bigquery-public-data.thelook_ecommerce.orders
  WHERE status = 'Complete'
  GROUP BY
    user_id
),
--join orders with traffic_source
user_source AS(
  SELECT
    c. traffic_source,
    c.id AS user_id,
    uo.first_order_date
  FROM
    bigquery-public-data.thelook_ecommerce.users c
  LEFT JOIN
    user_order uo
    ON c.id =uo.user_id
),source_summary AS( 
  SELECT
    COUNT(DISTINCT user_id) AS total_users,
    traffic_source,
    COUNTIF(first_order_date IS NOT NULL) AS converted_users
FROM
  user_source
GROUP BY 
  traffic_source 
)
--calculate conversion rate
SELECT
  traffic_source,
  total_users,
  converted_users,
  ROUND(SAFE_DIVIDE(converted_users, total_users)*100,2) AS conversion_rate
FROM
  source_summary
