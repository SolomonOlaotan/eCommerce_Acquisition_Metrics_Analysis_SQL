# Customer Demography
## This is a display of customer count per country.


SELECT
  COUNT(id) AS customer_count,
  country,
  MIN(DATE(created_at)) AS created_date
FROM
  bigquery-public-data.thelook_ecommerce.users
GROUP BY
  country
ORDER BY
  customer_count DESC
