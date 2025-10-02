# Customer Lifetime Value CLV
## CLV measures the revenue a business can expect from a customer throughout the entire relationship with the company. This can also be used to segment customers into different profitability levels.
### The SQL query.

--Customer Lifetime Value CLV---
WITH customer_orders_value AS(
  --calculate the total revenue 
  --first order date
  --last order date
  --days since first purchase
SELECT
  o.user_id AS customerID,
  COUNT(o.order_id) AS purchaseFrequency,
  SUM(oi.sale_price * o.num_of_item) AS total_revenue,
  AVG((oi.sale_price * o.num_of_item)) AS avgRevenueValue,
  MIN(DATE(o.created_at)) AS first_order_date,
  MAX(DATE(o.created_at)) AS last_order_date,
  DATE_DIFF(MAX(o.created_at),MIN(o.created_at), DAY) AS DaysSinceFirstPurchase
FROM
  bigquery-public-data.thelook_ecommerce.orders o 
JOIN 
  bigquery-public-data.thelook_ecommerce.order_items oi
  ON o.order_id = oi.order_id
WHERE o.status NOT IN ('Cancelled', 'Returned', 'Processing')
GROUP BY 
  o.user_id
)
SELECT
  customerID,
  purchaseFrequency,
  CAST(total_revenue AS INT) totalRevenue,
  CAST(avgRevenueValue AS INT) avgRevenueValue,
  DaysSinceFirstPurchase,
  --AOV * purchaseFrequency * CustomerLifespan (converted to year)
  --the formula used is :CLV = 
  --Average Revenue Value * Purchase Frequency * (Lifespan in years)
  --Lifespan is calculated as DaysSinceFirstPurchase / 365.0 to get years
  ROUND(avgRevenueValue * purchaseFrequency * 
    (DaysSinceFirstPurchase / 365.0), 2) AS CLV,
  --customer segmentation
  CASE
  WHEN ROUND(avgRevenueValue * purchaseFrequency * 
    (DaysSinceFirstPurchase / 365.0), 2) < 1000 THEN 'Non-Profitable'
  WHEN ROUND(avgRevenueValue * purchaseFrequency * 
    (DaysSinceFirstPurchase / 365.0), 2) BETWEEN 1000 AND 5000 THEN 'Profitable'
  ELSE 'Very Profitable'
  END AS customerSegment

FROM
  customer_orders_value
WHERE DATE_DIFF(last_order_date,first_order_date, DAY) > 0
