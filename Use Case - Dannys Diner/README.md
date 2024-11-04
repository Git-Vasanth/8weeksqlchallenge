# 🍜 Case Study #1: Danny's Diner

![Image](https://8weeksqlchallenge.com/images/case-study-designs/1.png)  <!-- Replace with actual image URL -->

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/). <!-- Replace with actual link -->

## Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite.

## Entity Relationship Diagram
![Entity Relationship Diagram](https://github.com/user-attachments/assets/fa164ad7-b9e5-49d5-991c-bd2d7eaa20c4)  <!-- Replace with actual image URL -->

## Question and Solution


### 1. What is the total amount each customer spent at the restaurant?
```sql
SELECT 
    s.customer_id, SUM(m.price) AS total_spent
FROM
    sales s
        JOIN
    menu m
WHERE
    s.product_id = m.product_id
GROUP BY s.customer_id
```

### Output

| customer_id |total_spent |
|-------------|--------------|
| A           |76            |
| B           |74            |
| C           |36            |

