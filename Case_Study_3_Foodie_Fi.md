
# 🍽️ Case Study #3: Foodie-Fi

## 📘 Problem Summary

Foodie-Fi is a subscription-based food delivery service similar to Netflix or Spotify but for restaurant meals. 
Customers can trial a free plan, upgrade to paid plans, and downgrade/cancel their subscriptions over time. 
The goal is to analyze customer subscription behaviors and revenue insights.

## 🧰 Tools Used

- SQL (PostgreSQL / MySQL compatible)
- Joins
- Aggregations
- Common Table Expressions (CTEs)
- Window Functions
- CASE Statements
- Date calculations

## 📑 Table of Contents
1. Customers who have subscribed to each plan
2. Number of customers who have trialed but not subscribed
3. Paying customers by plan type
4. Revenue calculation for Foodie-Fi
5. First subscription plan bought after trial
6. Total time spent on each plan
7. Customer subscription lifetime breakdown

---

## 📊 Business Questions & SQL Solutions

### 1. How many customers have ever subscribed to each plan?
Short Summary: Count distinct customers per plan.
```sql
SELECT
  plan_id,
  COUNT(DISTINCT customer_id) AS customer_count
FROM subscriptions
GROUP BY plan_id;
```

### 2. How many customers have trialed but not subscribed to a paid plan?
Short Summary: Customers only stayed on trial plan and didn't move.
```sql
SELECT
  COUNT(DISTINCT customer_id) AS trial_only_customers
FROM subscriptions
WHERE customer_id NOT IN (
  SELECT DISTINCT customer_id
  FROM subscriptions
  WHERE plan_id IN (2, 3, 4) -- Paid plans
);
```

### 3. How many customers have ever paid for a subscription?
Short Summary: Customers who paid at least once.
```sql
SELECT
  COUNT(DISTINCT customer_id) AS paying_customers
FROM subscriptions
WHERE plan_id IN (2, 3, 4);
```

### 4. What is the total revenue earned by Foodie-Fi?
Short Summary: Join plans with subscriptions and sum up revenue.
```sql
SELECT
  SUM(p.price) AS total_revenue
FROM subscriptions s
JOIN plans p
  ON s.plan_id = p.plan_id
WHERE p.price > 0;
```

### 5. What is the first subscription plan the customer signed up for after the trial?
Short Summary: Rank customer subscriptions by start date.
```sql
WITH customer_rank AS (
  SELECT
    customer_id,
    plan_id,
    start_date,
    RANK() OVER (PARTITION BY customer_id ORDER BY start_date) AS rnk
  FROM subscriptions
)
SELECT
  customer_id,
  plan_id
FROM customer_rank
WHERE rnk = 2; -- After trial (assuming trial is always 1st)
```

### 6. How long does each customer stay on each plan?
Short Summary: Difference between start dates or default assumption.
```sql
WITH plan_duration AS (
  SELECT
    customer_id,
    plan_id,
    start_date,
    LEAD(start_date) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_start_date
  FROM subscriptions
)
SELECT
  customer_id,
  plan_id,
  start_date,
  COALESCE(next_start_date, CURRENT_DATE) AS end_date
FROM plan_duration;
```

### 7. How many customers downgraded from a Pro plan to a Basic plan?
Short Summary: Finding downgrade transition customers.
```sql
WITH transitions AS (
  SELECT
    customer_id,
    plan_id,
    LEAD(plan_id) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_plan
  FROM subscriptions
)
SELECT
  COUNT(*)
FROM transitions
WHERE plan_id = 2 -- Pro Plan
  AND next_plan = 1; -- Basic Plan
```

---

## 🧠 Key Learnings

- Learned how to track customer transitions over time with SQL.
- Practical exposure to LEAD() window function to identify next events.
- Hands-on with calculating subscription lifecycles and company revenue.

## 🎯 Skills Practiced

| Skill |
|:------|
| SQL Joins |
| Aggregations |
| Window Functions (RANK, LEAD) |
| CTEs |
| Customer Journey Analysis |
| Revenue Calculation |

