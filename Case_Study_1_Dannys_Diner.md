# 🍣 Case Study #1: Danny's Diner

**Challenge Source:** [8 Week SQL Challenge – Case Study #1](https://8weeksqlchallenge.com/case-study-1/)

## 📘 Problem Summary
Danny wants to use data to better understand his customers' buying behavior at his restaurant. The goal is to perform analysis on customer visits, most purchased items, total spending, and loyalty membership effects.

## 🧰 Tools Used
- SQL (PostgreSQL/MySQL compatible)
- Joins, Aggregates, Window Functions, CASE

---

## 📊 Business Questions & Solutions

### 1. What is the total amount each customer spent at the restaurant?

```sql
SELECT
  s.customer_id,
  SUM(m.price) AS total_spent
FROM sales s
JOIN menu m
  ON s.product_id = m.product_id
GROUP BY s.customer_id;
