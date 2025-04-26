
# 🍣 Case Study #1: Danny's Diner

## Problem Summary

Danny wants to use data to better understand his customers' buying behavior at his restaurant. The goal is to perform analysis on customer visits, most purchased items, total spending, and loyalty program impact.

## Tools Used

- SQL (PostgreSQL / MySQL compatible)
- Joins
- Aggregations
- Window Functions
- Common Table Expressions (CTEs)
- CASE Statements

## Business Questions & SQL Solutions

### 1. What is the total amount each customer spent at the restaurant?
```sql
SELECT
  s.customer_id,
  SUM(m.price) AS total_spent
FROM sales s
JOIN menu m
  ON s.product_id = m.product_id
GROUP BY s.customer_id;
```

### 2. How many days has each customer visited the restaurant?
```sql
SELECT
  customer_id,
  COUNT(DISTINCT order_date) AS visit_days
FROM sales
GROUP BY customer_id;
```

### 3. What was the first item from the menu purchased by each customer?
```sql
WITH first_order AS (
  SELECT
    customer_id,
    order_date,
    product_id,
    RANK() OVER (PARTITION BY customer_id ORDER BY order_date ASC) AS rnk
  FROM sales
)
SELECT
  f.customer_id,
  m.product_name
FROM first_order f
JOIN menu m
  ON f.product_id = m.product_id
WHERE f.rnk = 1;
```

### 4. What is the most purchased item on the menu and how many times was it purchased?
```sql
SELECT
  m.product_name,
  COUNT(*) AS purchase_count
FROM sales s
JOIN menu m
  ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY purchase_count DESC
LIMIT 1;
```

### 5. Which item was the most popular for each customer?
```sql
WITH customer_fav AS (
  SELECT
    customer_id,
    product_id,
    COUNT(*) AS purchase_count,
    RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(*) DESC) AS rnk
  FROM sales
  GROUP BY customer_id, product_id
)
SELECT
  cf.customer_id,
  m.product_name,
  cf.purchase_count
FROM customer_fav cf
JOIN menu m
  ON cf.product_id = m.product_id
WHERE cf.rnk = 1;
```

### 6. Which item was purchased first by the customer after they became a member?
```sql
WITH joined_data AS (
  SELECT
    s.customer_id,
    s.order_date,
    s.product_id,
    m.join_date
  FROM sales s
  JOIN members m
    ON s.customer_id = m.customer_id
  WHERE s.order_date >= m.join_date
),
ranked_data AS (
  SELECT
    customer_id,
    product_id,
    RANK() OVER (PARTITION BY customer_id ORDER BY order_date ASC) AS rnk
  FROM joined_data
)
SELECT
  rd.customer_id,
  mn.product_name
FROM ranked_data rd
JOIN menu mn
  ON rd.product_id = mn.product_id
WHERE rnk = 1;
```

### 7. Which item was purchased just before the customer became a member?
```sql
WITH before_membership AS (
  SELECT
    s.customer_id,
    s.order_date,
    s.product_id,
    m.join_date
  FROM sales s
  JOIN members m
    ON s.customer_id = m.customer_id
  WHERE s.order_date < m.join_date
),
ranked_data AS (
  SELECT
    customer_id,
    product_id,
    RANK() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rnk
  FROM before_membership
)
SELECT
  rd.customer_id,
  mn.product_name
FROM ranked_data rd
JOIN menu mn
  ON rd.product_id = mn.product_id
WHERE rnk = 1;
```

### 8. What is the total items and amount spent for each member before they became a member?
```sql
SELECT
  s.customer_id,
  COUNT(*) AS total_items,
  SUM(m.price) AS total_spent
FROM sales s
JOIN menu m
  ON s.product_id = m.product_id
JOIN members mem
  ON s.customer_id = mem.customer_id
WHERE s.order_date < mem.join_date
GROUP BY s.customer_id;
```

### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points did each customer earn?
```sql
SELECT
  s.customer_id,
  SUM(
    CASE
      WHEN m.product_name = 'sushi' THEN m.price * 20
      ELSE m.price * 10
    END
  ) AS total_points
FROM sales s
JOIN menu m
  ON s.product_id = m.product_id
GROUP BY s.customer_id;
```

### 10. In the first week after a customer joins the program (including join date), they earn 2x points on all items, not just sushi — how many points do customers A and B have at the end of January?
```sql
WITH sales_with_points AS (
  SELECT
    s.customer_id,
    s.order_date,
    m.product_name,
    m.price,
    mem.join_date,
    CASE
      WHEN s.order_date BETWEEN mem.join_date AND DATE_ADD(mem.join_date, INTERVAL 6 DAY) THEN m.price * 20
      WHEN m.product_name = 'sushi' THEN m.price * 20
      ELSE m.price * 10
    END AS points_earned
  FROM sales s
  JOIN menu m
    ON s.product_id = m.product_id
  JOIN members mem
    ON s.customer_id = mem.customer_id
  WHERE s.order_date <= '2021-01-31'
)
SELECT
  customer_id,
  SUM(points_earned) AS total_points
FROM sales_with_points
WHERE customer_id IN ('A', 'B')
GROUP BY customer_id;
```

## 🔍 Key Learnings

- Practical experience in solving business queries using SQL.
- Hands-on practice with joins, aggregates, ranking functions, and date filtering.
- Reinforced the use of CTEs and CASE logic for advanced business rules.
- Learned how to translate business requirements into efficient SQL queries.
