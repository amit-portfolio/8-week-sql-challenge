
# 🏦 Case Study #4: Data Bank

## 📘 Problem Summary

Data Bank is a financial institution that collects customer signup and product usage data. 
The goal of this case study is to analyze customer growth trends, onboarding journeys, product usage, and revenue metrics.

## 🧰 Tools Used

- SQL (PostgreSQL / MySQL compatible)
- Joins
- Aggregations
- Common Table Expressions (CTEs)
- CASE Statements
- Window Functions
- Date Manipulations

## 📑 Table of Contents
1. Monthly customer signups
2. Product-wise customer signups
3. Region-wise customer distribution
4. Churned customers identification
5. Monthly revenue tracking
6. Average products used per customer
7. Revenue breakdown by product and region

---

## 📊 Business Questions & SQL Solutions

### 1. How many customers have joined each month?
Short Summary: Group customers by signup month.
```sql
SELECT
  DATE_TRUNC('month', start_date) AS signup_month,
  COUNT(customer_id) AS new_customers
FROM customers
GROUP BY signup_month
ORDER BY signup_month;
```

### 2. How many customers signed up with each product?
Short Summary: Customer count per product.
```sql
SELECT
  p.product_name,
  COUNT(c.customer_id) AS customer_count
FROM customers c
JOIN products p
  ON c.primary_product_id = p.product_id
GROUP BY p.product_name;
```

### 3. How are customers distributed across different regions?
Short Summary: Region-wise distribution of customers.
```sql
SELECT
  r.region_name,
  COUNT(c.customer_id) AS customers_in_region
FROM customers c
JOIN regions r
  ON c.region_id = r.region_id
GROUP BY r.region_name
ORDER BY customers_in_region DESC;
```

### 4. Which customers have churned (no transactions recorded)?
Short Summary: Customers without any transactions are considered churned.
```sql
SELECT
  c.customer_id
FROM customers c
LEFT JOIN customer_transactions t
  ON c.customer_id = t.customer_id
WHERE t.customer_id IS NULL;
```

### 5. What is the monthly revenue trend?
Short Summary: Monthly revenue generated from transactions.
```sql
SELECT
  DATE_TRUNC('month', txn_date) AS revenue_month,
  SUM(txn_amount) AS total_revenue
FROM customer_transactions
GROUP BY revenue_month
ORDER BY revenue_month;
```

### 6. What is the average number of products used by customers?
Short Summary: Find the average number of different products used.
```sql
SELECT
  AVG(products_used) AS avg_products_used
FROM (
  SELECT
    customer_id,
    COUNT(DISTINCT product_id) AS products_used
  FROM customer_transactions
  GROUP BY customer_id
) subquery;
```

### 7. What is the revenue breakdown by product and region?
Short Summary: Revenue earned by each product across regions.
```sql
SELECT
  p.product_name,
  r.region_name,
  SUM(t.txn_amount) AS total_revenue
FROM customer_transactions t
JOIN customers c
  ON t.customer_id = c.customer_id
JOIN products p
  ON c.primary_product_id = p.product_id
JOIN regions r
  ON c.region_id = r.region_id
GROUP BY p.product_name, r.region_name
ORDER BY total_revenue DESC;
```

---

## 🧠 Key Learnings

- Hands-on cohort analysis and revenue tracking.
- Practical usage of CTEs and window functions.
- Customer churn analysis based on transaction history.

## 🎯 Skills Practiced

| Skill |
|:------|
| SQL Joins |
| CTEs |
| Aggregations |
| Churn Analysis |
| Revenue Reporting |
| Customer Lifecycle Analysis |
| Date Functions |
