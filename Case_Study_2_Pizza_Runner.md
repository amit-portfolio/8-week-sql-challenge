
# 🍕 Case Study #2: Pizza Runner

## 📘 Problem Summary

Danny is expanding his food business by launching a pizza delivery service called Pizza Runner. The objective is to understand and optimize delivery operations using the data collected.

## 🧰 Tools Used

- SQL (PostgreSQL / MySQL compatible)
- Joins
- Aggregations
- Common Table Expressions (CTEs)
- CASE Statements
- Date and Time calculations

## 📑 Table of Contents
1. Successful Orders by Runner
2. Total Distance Run by Each Runner
3. Average Speed per Runner
4. Cancellation Counts and Reasons
5. Runner Performance Insights
6. Percentage of Successful Deliveries
7. Average Delivery Time per Runner
8. Differences in Customer vs Runner Orders
9. Pizza Popularity Analysis
10. Customer Lifetime Pizza Orders

---

## 📊 Business Questions & SQL Solutions

### 1. How many successful orders were delivered by each runner?
Short Summary: Counting successful deliveries where pickup time is not null.
```sql
SELECT
  runner_id,
  COUNT(*) AS successful_deliveries
FROM runner_orders
WHERE pickup_time IS NOT NULL
GROUP BY runner_id;
```

### 2. What is the total distance traveled by each runner?
Short Summary: Summing up distance for each runner for successful deliveries.
```sql
SELECT
  runner_id,
  SUM(distance) AS total_distance_km
FROM runner_orders
WHERE pickup_time IS NOT NULL
GROUP BY runner_id;
```

### 3. What was the average speed for each runner for each delivery?
Short Summary: Calculating average speed using distance and duration.
```sql
SELECT
  runner_id,
  ROUND(AVG(distance / (duration/60)), 2) AS avg_speed_kmph
FROM runner_orders
WHERE pickup_time IS NOT NULL
GROUP BY runner_id;
```

### 4. What is the count of cancellations for each runner?
Short Summary: Counting orders where cancellation reason is provided.
```sql
SELECT
  runner_id,
  COUNT(*) AS cancellation_count
FROM runner_orders
WHERE cancellation IS NOT NULL
GROUP BY runner_id;
```

### 5. What are the reasons for cancellation and their counts?
Short Summary: Finding top reasons customers cancel their orders.
```sql
SELECT
  cancellation,
  COUNT(*) AS reason_count
FROM runner_orders
WHERE cancellation IS NOT NULL
GROUP BY cancellation
ORDER BY reason_count DESC;
```

### 6. What percentage of orders were successfully delivered?
Short Summary: Calculating success rate out of total orders.
```sql
SELECT
  ROUND(
    (SUM(CASE WHEN pickup_time IS NOT NULL THEN 1 ELSE 0 END) * 100.0) / COUNT(*),
    2
  ) AS success_rate_percentage
FROM runner_orders;
```

### 7. What is the average delivery time taken by each runner?
Short Summary: Finding how long it typically takes a runner to deliver an order.
```sql
SELECT
  runner_id,
  ROUND(AVG(duration), 2) AS avg_delivery_time_mins
FROM runner_orders
WHERE pickup_time IS NOT NULL
GROUP BY runner_id;
```

### 8. How many pizzas were ordered by each customer?
Short Summary: Counting pizza orders per customer.
```sql
SELECT
  customer_id,
  COUNT(*) AS pizzas_ordered
FROM customer_orders
GROUP BY customer_id;
```

### 9. What was the most popular pizza?
Short Summary: Identifying the pizza ordered the most across all customers.
```sql
SELECT
  pn.pizza_name,
  COUNT(co.pizza_id) AS pizza_count
FROM customer_orders co
JOIN pizza_names pn
  ON co.pizza_id = pn.pizza_id
GROUP BY pn.pizza_name
ORDER BY pizza_count DESC
LIMIT 1;
```

### 10. How many pizzas has each customer ordered in total over their lifetime?
Short Summary: Total pizza consumption per customer.
```sql
SELECT
  customer_id,
  COUNT(*) AS total_pizzas
FROM customer_orders
GROUP BY customer_id;
```

---

## 🧠 Key Learnings

- Practical experience using date and time fields for delivery calculations.
- Hands-on working with JOINs, aggregates, and NULL condition handling.
- Learned business metrics tracking like delivery success rates, average speeds, and customer ordering behavior.

## 🎯 Skills Practiced

| Skill |
|:------|
| SQL Joins |
| Aggregations |
| Filtering & Null Checks |
| CTEs |
| Time Calculations |
| Business Analytics |
