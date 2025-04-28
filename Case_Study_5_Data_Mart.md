
# 🛒 Case Study #5: Data Mart

## 📘 Problem Summary

Data Mart is a virtual supermarket needing insights into customer transactions and purchase behavior.
This case study focuses on analyzing monthly sales, customer growth, revenue trends, and customer segmentation.

## 🧰 Tools Used

- SQL (PostgreSQL / MySQL compatible)
- Joins
- Aggregations
- Common Table Expressions (CTEs)
- Window Functions (RANK)
- CASE Statements
- Customer Behavior Analysis

## 📑 Table of Contents
1. Total customers and transactions each month
2. Sales revenue generated each month
3. Monthly average revenue per customer
4. Top 3 products by sales each month
5. Customer lifetime value
6. Monthly customer segmentation based on spending
7. Highest spending customers

---

## 📊 Business Questions & SQL Solutions

### 1. What is the total number of customers and transactions each month?
Short Summary: Count customers and transactions grouped by month.
```sql
SELECT
  DATE_TRUNC('month', txn_date) AS month,
  COUNT(DISTINCT customer_id) AS total_customers,
  COUNT(txn_id) AS total_transactions
FROM sales
GROUP BY month
ORDER BY month;
```

### 2. What is the total sales revenue generated each month?
Short Summary: Sum transaction amounts grouped by month.
```sql
SELECT
  DATE_TRUNC('month', txn_date) AS month,
  SUM(txn_amount) AS total_revenue
FROM sales
GROUP BY month
ORDER BY month;
```

### 3. What is the monthly average revenue per customer?
Short Summary: Average spend per customer monthly.
```sql
WITH monthly_data AS (
  SELECT
    DATE_TRUNC('month', txn_date) AS month,
    customer_id,
    SUM(txn_amount) AS customer_revenue
  FROM sales
  GROUP BY month, customer_id
)
SELECT
  month,
  ROUND(AVG(customer_revenue), 2) AS avg_revenue_per_customer
FROM monthly_data
GROUP BY month
ORDER BY month;
```

### 4. What are the top 3 products by total sales each month?
Short Summary: Rank products by monthly sales revenue.
```sql
WITH product_monthly_sales AS (
  SELECT
    DATE_TRUNC('month', txn_date) AS month,
    product_id,
    SUM(txn_amount) AS total_sales,
    RANK() OVER (PARTITION BY DATE_TRUNC('month', txn_date) ORDER BY SUM(txn_amount) DESC) AS sales_rank
  FROM sales
  GROUP BY month, product_id
)
SELECT
  month,
  product_id,
  total_sales
FROM product_monthly_sales
WHERE sales_rank <= 3;
```

### 5. What is the lifetime value of each customer?
Short Summary: Sum of transaction amounts for each customer.
```sql
SELECT
  customer_id,
  SUM(txn_amount) AS lifetime_value
FROM sales
GROUP BY customer_id
ORDER BY lifetime_value DESC;
```

### 6. How can we segment customers each month based on spending?
Short Summary: Categorize customers into segments by monthly spend.
```sql
WITH customer_monthly_spending AS (
  SELECT
    customer_id,
    DATE_TRUNC('month', txn_date) AS month,
    SUM(txn_amount) AS monthly_spend
  FROM sales
  GROUP BY customer_id, month
)
SELECT
  customer_id,
  month,
  CASE
    WHEN monthly_spend >= 500 THEN 'High-Value'
    WHEN monthly_spend BETWEEN 200 AND 499 THEN 'Mid-Value'
    ELSE 'Low-Value'
  END AS segment
FROM customer_monthly_spending;
```

### 7. Who are the highest spending customers overall?
Short Summary: Top customers by lifetime spending.
```sql
SELECT
  customer_id,
  SUM(txn_amount) AS total_spent
FROM sales
GROUP BY customer_id
ORDER BY total_spent DESC
LIMIT 10;
```

---

## 🧠 Key Learnings

- Learned cohort analysis and revenue tracking.
- Practical experience with customer LTV and segmentation.
- Developed SQL techniques for business analytics on transactions.

## 🎯 Skills Practiced

| Skill |
|:------|
| SQL Joins |
| Aggregations |
| CTEs |
| Window Functions |
| Revenue Analysis |
| Customer Segmentation |
| Lifetime Value Calculations |
