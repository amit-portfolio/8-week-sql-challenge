
# 🖱️ Case Study #6: Clique Bait

## 📘 Problem Summary

Clique Bait is an online click-based advertising platform that captures user interaction data across their website pages.
The focus is to analyze user navigation paths, click-through behavior, bounce rates, and funnel conversion performance.

## 🧰 Tools Used

- SQL (PostgreSQL / MySQL compatible)
- Joins
- Aggregations
- Common Table Expressions (CTEs)
- Window Functions (LEAD)
- Funnel Analysis

## 📑 Table of Contents
1. Unique users who visited each page
2. Percentage of users who visited the homepage
3. Percentage of users who visited about, product, and careers pages
4. Percentage of users who visited the contact page
5. Bounce rate calculation
6. Page navigation funnel analysis
7. Conversion rate from homepage to contact page

---

## 📊 Business Questions & SQL Solutions

### 1. How many unique users visited each page?
Short Summary: Count distinct users per page.
```sql
SELECT
  p.page_name,
  COUNT(DISTINCT e.user_id) AS unique_visitors
FROM events e
JOIN page_hierarchy p
  ON e.page_id = p.page_id
GROUP BY p.page_name
ORDER BY unique_visitors DESC;
```

### 2. What percentage of users visited the homepage?
Short Summary: Homepage visitors out of total users.
```sql
WITH total_users AS (
  SELECT COUNT(DISTINCT user_id) AS total_count FROM events
)
SELECT
  ROUND(
    (COUNT(DISTINCT e.user_id) * 100.0) / (SELECT total_count FROM total_users),
    2
  ) AS homepage_visit_percentage
FROM events e
WHERE e.page_id = 1;
```

### 3. What percentage of users visited about, product, and careers pages?
Short Summary: Visit percentage for major informational pages.
```sql
WITH total_users AS (
  SELECT COUNT(DISTINCT user_id) AS total_count FROM events
)
SELECT
  p.page_name,
  ROUND(
    (COUNT(DISTINCT e.user_id) * 100.0) / (SELECT total_count FROM total_users),
    2
  ) AS visit_percentage
FROM events e
JOIN page_hierarchy p
  ON e.page_id = p.page_id
WHERE p.page_name IN ('About', 'Product', 'Careers')
GROUP BY p.page_name;
```

### 4. What percentage of users visited the contact page?
Short Summary: Contact page visit rate.
```sql
WITH total_users AS (
  SELECT COUNT(DISTINCT user_id) AS total_count FROM events
)
SELECT
  ROUND(
    (COUNT(DISTINCT e.user_id) * 100.0) / (SELECT total_count FROM total_users),
    2
  ) AS contact_page_visit_percentage
FROM events e
WHERE e.page_id = 5;
```

### 5. What is the bounce rate?
Short Summary: Users who viewed only one page.
```sql
WITH user_activity AS (
  SELECT
    user_id,
    COUNT(DISTINCT page_id) AS pages_visited
  FROM events
  GROUP BY user_id
)
SELECT
  ROUND(
    (COUNT(CASE WHEN pages_visited = 1 THEN 1 END) * 100.0) / COUNT(*),
    2
  ) AS bounce_rate_percentage
FROM user_activity;
```

### 6. How do users navigate between pages?
Short Summary: Page transition analysis.
```sql
WITH navigation_paths AS (
  SELECT
    user_id,
    page_id,
    LEAD(page_id) OVER (PARTITION BY user_id ORDER BY event_time) AS next_page_id
  FROM events
)
SELECT
  p1.page_name AS current_page,
  p2.page_name AS next_page,
  COUNT(*) AS transitions
FROM navigation_paths np
JOIN page_hierarchy p1
  ON np.page_id = p1.page_id
JOIN page_hierarchy p2
  ON np.next_page_id = p2.page_id
WHERE np.next_page_id IS NOT NULL
GROUP BY current_page, next_page
ORDER BY transitions DESC;
```

### 7. What is the conversion rate from homepage to contact page?
Short Summary: % of users who moved from homepage to contact page.
```sql
WITH homepage_users AS (
  SELECT DISTINCT user_id
  FROM events
  WHERE page_id = 1
),
contact_users AS (
  SELECT DISTINCT user_id
  FROM events
  WHERE page_id = 5
)
SELECT
  ROUND(
    (COUNT(DISTINCT cu.user_id) * 100.0) / (SELECT COUNT(*) FROM homepage_users),
    2
  ) AS homepage_to_contact_conversion_percentage
FROM contact_users cu
JOIN homepage_users hu
  ON cu.user_id = hu.user_id;
```

---

## 🧠 Key Learnings

- Clickstream analysis and customer funnel drop-off points.
- Bounce rate and transition path calculations using window functions.
- Conversion rate analysis for web performance tracking.

## 🎯 Skills Practiced

| Skill |
|:------|
| SQL Joins |
| Window Functions (LEAD) |
| Aggregations |
| Funnel Analysis |
| Clickstream Analysis |
| Bounce Rate Calculation |
