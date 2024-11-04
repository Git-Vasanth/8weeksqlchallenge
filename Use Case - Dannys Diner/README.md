# 🍜 Case Study #1: Danny's Diner

![Image](image-url-here)  <!-- Replace with actual image URL -->

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](link-url-here). <!-- Replace with actual link -->

## Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite.

## Entity Relationship Diagram
![Entity Relationship Diagram](image-url-here)  <!-- Replace with actual image URL -->

## Question and Solution
Please join me in executing the queries using PostgreSQL on DB Fiddle. It would be great to work together on the questions! 

### 1. What is the total amount each customer spent at the restaurant?
```sql
SELECT 
  sales.customer_id, 
  SUM(menu.price) AS total_sales
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id ASC;
