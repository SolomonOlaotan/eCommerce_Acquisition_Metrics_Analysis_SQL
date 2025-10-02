# Days to first purchase
## This measure evaluates the average days between when a customer registers and makes a purchase.
### SQL query


--calculate the days taken between users registration date to first order
WITH first_order AS(
  SELECT
    o.user_id,
    MIN(DATE(o.created_at)) AS first_order_date
  FROM
    bigquery-public-data.thelook_ecommerce.orders o
  GROUP BY
    o.user_id
),
--select user per registration day
users_cohort AS(
  SELECT
    c.id,
    DATE(c.created_at) AS registration_date,
    DATE_DIFF(fo.first_order_date, DATE(c.created_at), DAY) AS days_to_first_order,
    fo.first_order_date
  FROM
    bigquery-public-data.thelook_ecommerce.users c
  JOIN
    first_order fo
    ON c.id = fo.user_id
)
SELECT
  COUNT(users_cohort.id) AS users,
  users_cohort.registration_date,
  users_cohort.first_order_date,
  CAST(AVG(users_cohort.days_to_first_order)AS INT) AS avg_days_to_purchase
FROM
  users_cohort
GROUP BY first_order_date, registration_date
