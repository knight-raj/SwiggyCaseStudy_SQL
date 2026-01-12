# 🍔 Swiggy SQL Analytics Project

## Project Overview

**Project Title**: Swiggy SQL Analytics
**Level**: Intermediate
**Database**: `swiggy`

This project demonstrates **real-world SQL analytics skills** using a food delivery platform use case similar to Swiggy. The focus is on converting raw transactional data into **actionable business insights** related to customer behavior, restaurant performance, revenue growth, and product strategy.

This project is suitable for **Data Analyst / BI Analyst** roles and reflects **interview-level SQL problem solving**.

---

## Objectives

1. Create and use a Swiggy-style relational database
2. Analyze customer, restaurant, order, and food-level data
3. Solve real business problems using SQL
4. Apply advanced SQL concepts such as joins, CTEs, subqueries, and window functions

---

## Project Structure

### 1. Database Setup

```sql
CREATE DATABASE swiggy;
USE swiggy;
```

**Key Tables Used**:

* `users`
* `restaurants`
* `orders`
* `order_details`
* `food`
* `menu`

These tables form a **normalized food-delivery schema** enabling end-to-end business analysis.

---

## Business Analysis & SQL Queries

### 1. Customers Who Have Never Ordered

```sql
SELECT name
FROM users
WHERE user_id NOT IN (SELECT user_id FROM orders);
```

**Insight**: Identifies inactive users for targeted re-engagement campaigns.

---

### 2. Average Price per Dish

```sql
SELECT f.f_name, AVG(price) AS avg_price
FROM menu m
JOIN food f ON m.f_id = f.f_id
GROUP BY f.f_name;
```

**Insight**: Supports pricing optimization and competitive analysis.

---

### 3. Top Restaurant by Monthly Orders

```sql
SELECT r.r_name, COUNT(*) AS total_orders
FROM orders o
JOIN restaurants r ON o.r_id = r.r_id
WHERE MONTHNAME(date) = 'June'
GROUP BY r.r_name
ORDER BY total_orders DESC
LIMIT 1;
```

**Insight**: Identifies high-demand restaurants for promotions and capacity planning.

---

### 4. Restaurants with Monthly Revenue > X

```sql
SELECT r.r_name, SUM(amount) AS revenue
FROM orders o
JOIN restaurants r ON o.r_id = r.r_id
WHERE MONTHNAME(date) = 'June'
GROUP BY r.r_name
HAVING revenue > 500;
```

**Insight**: Highlights top revenue-generating restaurant partners.

---

### 5. Customer Orders in a Date Range

```sql
SELECT o.order_id, r.r_name, f.f_name
FROM orders o
JOIN restaurants r ON o.r_id = r.r_id
JOIN order_details od ON o.order_id = od.order_id
JOIN food f ON f.f_id = od.f_id
WHERE user_id = (SELECT user_id FROM users WHERE name = 'Ankit')
AND date BETWEEN '2022-06-10' AND '2022-07-10';
```

**Insight**: Useful for personalization, customer support, and order history analysis.

---

### 6. Restaurant with Maximum Repeat Customers

```sql
SELECT r.r_name, COUNT(*) AS loyal_customers
FROM (
    SELECT r_id, user_id
    FROM orders
    GROUP BY r_id, user_id
    HAVING COUNT(*) > 1
) t
JOIN restaurants r ON r.r_id = t.r_id
GROUP BY r.r_name
ORDER BY loyal_customers DESC
LIMIT 1;
```

**Insight**: Measures customer loyalty at restaurant level.

---

### 7. Month-over-Month Revenue Growth (Restaurant-wise)

```sql
WITH monthly_revenue AS (
    SELECT r_id,
           MONTH(date) AS month_no,
           MONTHNAME(date) AS month,
           SUM(amount) AS revenue
    FROM orders
    GROUP BY r_id, month_no, month
),
growth AS (
    SELECT *,
           LAG(revenue) OVER (PARTITION BY r_id ORDER BY month_no) AS prev_revenue
    FROM monthly_revenue
)
SELECT r.r_name, month, revenue,
       ROUND(((revenue - prev_revenue) / prev_revenue) * 100, 2) AS mom_growth
FROM growth g
JOIN restaurants r ON r.r_id = g.r_id
WHERE prev_revenue IS NOT NULL;
```

**Insight**: Tracks revenue trends and performance momentum.

---

### 8. Customer’s Favorite Food

```sql
WITH temp AS (
    SELECT o.user_id, od.f_id, COUNT(*) AS frequency
    FROM orders o
    JOIN order_details od ON o.order_id = od.order_id
    GROUP BY o.user_id, od.f_id
)
SELECT u.name, f.f_name, frequency
FROM temp t
JOIN users u ON u.user_id = t.user_id
JOIN food f ON f.f_id = t.f_id
WHERE frequency = (
    SELECT MAX(frequency)
    FROM temp
    WHERE user_id = t.user_id
);
```

**Insight**: Enables personalization and recommendation systems.

---

### 9. Most Paired Products

```sql
SELECT f1.f_name AS product_1,
       f2.f_name AS product_2,
       COUNT(*) AS pair_count
FROM order_details od1
JOIN order_details od2
ON od1.order_id = od2.order_id
AND od1.f_id < od2.f_id
JOIN food f1 ON f1.f_id = od1.f_id
JOIN food f2 ON f2.f_id = od2.f_id
GROUP BY product_1, product_2
ORDER BY pair_count DESC;
```

**Insight**: Drives combo offers, cross-selling, and higher Average Order Value (AOV).

---

## Findings

* Identified inactive and loyal customers
* Highlighted top-performing and high-growth restaurants
* Discovered strong product pairing opportunities
* Analyzed customer preferences for personalization

---

## Conclusion

This project showcases how **SQL can be used as a strategic analytics tool**, not just for reporting. The insights derived can directly support **customer retention, revenue growth, personalization, and restaurant partnerships** in a food delivery business.

---

## Author

**Ankit Raj**
Data Analyst | SQL | Business Analytics

This project is part of my **Data Analytics portfolio**.

---

⭐ *Always learning. Always building.*
