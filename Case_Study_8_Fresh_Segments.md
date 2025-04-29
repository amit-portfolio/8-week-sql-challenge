
# 🍏 Case Study #8: Fresh Segments

## 📘 Problem Summary

Fresh Segments is a marketing company that analyzes user demographic and transactional behavior to help design better advertising strategies.
This case study focuses on user segmentation by age, region, and income, as well as campaign targeting and spending patterns.

## 🧰 Tools Used

- SQL (PostgreSQL / MySQL compatible)
- Joins
- Aggregations
- Common Table Expressions (CTEs)
- CASE Statements
- User Segmentation & Behavior Analysis

## 📑 Table of Contents
1. Customer distribution by region
2. Age group and gender segmentation
3. Income level categorization
4. Average spend per income group
5. Campaign reach by age and region
6. Campaign effectiveness by income group
7. Top spending users in each region

---

## 📊 Business Questions & SQL Solutions

### 1. How many users are there in each region?
Short Summary: Count of users per region.
```sql
SELECT
  region,
  COUNT(user_id) AS user_count
FROM user_profiles
GROUP BY region
ORDER BY user_count DESC;
```

### 2. How are users distributed by age group and gender?
Short Summary: Segmentation based on age brackets and gender.
```sql
SELECT
  gender,
  CASE
    WHEN age < 25 THEN 'Under 25'
    WHEN age BETWEEN 25 AND 34 THEN '25-34'
    WHEN age BETWEEN 35 AND 44 THEN '35-44'
    WHEN age BETWEEN 45 AND 54 THEN '45-54'
    ELSE '55+'
  END AS age_group,
  COUNT(*) AS user_count
FROM user_profiles
GROUP BY gender, age_group
ORDER BY age_group, gender;
```

### 3. How many users fall into each income category?
Short Summary: Group users by income level.
```sql
SELECT
  income_level,
  COUNT(*) AS users
FROM user_profiles
GROUP BY income_level
ORDER BY users DESC;
```

### 4. What is the average spending per income level?
Short Summary: Mean spending based on income group.
```sql
SELECT
  up.income_level,
  ROUND(AVG(t.amount), 2) AS avg_spending
FROM user_profiles up
JOIN transactions t
  ON up.user_id = t.user_id
GROUP BY up.income_level
ORDER BY avg_spending DESC;
```

### 5. What is the reach of each campaign across different age groups and regions?
Short Summary: Campaign engagement by demographics.
```sql
SELECT
  c.campaign_name,
  up.region,
  CASE
    WHEN up.age < 25 THEN 'Under 25'
    WHEN up.age BETWEEN 25 AND 34 THEN '25-34'
    WHEN up.age BETWEEN 35 AND 44 THEN '35-44'
    WHEN up.age BETWEEN 45 AND 54 THEN '45-54'
    ELSE '55+'
  END AS age_group,
  COUNT(DISTINCT c.user_id) AS reached_users
FROM campaigns c
JOIN user_profiles up
  ON c.user_id = up.user_id
GROUP BY c.campaign_name, up.region, age_group
ORDER BY c.campaign_name, region;
```

### 6. How effective were the campaigns across income groups?
Short Summary: Transactional engagement from campaign users.
```sql
SELECT
  up.income_level,
  COUNT(DISTINCT c.user_id) AS targeted_users,
  COUNT(DISTINCT t.user_id) AS converted_users,
  ROUND(COUNT(DISTINCT t.user_id) * 100.0 / COUNT(DISTINCT c.user_id), 2) AS conversion_rate
FROM campaigns c
JOIN user_profiles up
  ON c.user_id = up.user_id
LEFT JOIN transactions t
  ON c.user_id = t.user_id
GROUP BY up.income_level
ORDER BY conversion_rate DESC;
```

### 7. Who are the top spending users in each region?
Short Summary: High-value customers by geography.
```sql
WITH total_spent AS (
  SELECT
    t.user_id,
    SUM(t.amount) AS total_amount,
    up.region
  FROM transactions t
  JOIN user_profiles up
    ON t.user_id = up.user_id
  GROUP BY t.user_id, up.region
)
SELECT *
FROM (
  SELECT
    user_id,
    region,
    total_amount,
    RANK() OVER (PARTITION BY region ORDER BY total_amount DESC) AS region_rank
  FROM total_spent
) ranked
WHERE region_rank = 1;
```

---

## 🧠 Key Learnings

- Applied demographic segmentation with CASE logic.
- Measured campaign reach and conversion effectiveness by user profiles.
- Identified high-spending customer segments across regions.

## 🎯 Skills Practiced

| Skill |
|:------|
| SQL Joins |
| Aggregations |
| Demographic Segmentation |
| CASE Expressions |
| Campaign Effectiveness Analysis |
| Spending Behavior Analytics |
