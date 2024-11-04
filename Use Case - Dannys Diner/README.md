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

### 2. How many days has each customer visited the restaurant?
```sql
SELECT 
  s.customer_id AS customer_id, 
  COUNT(DISTINCT s.order_date) AS visitng_count
FROM 
  dannys_diner.sales AS s
GROUP BY 
  s.customer_id
ORDER BY 
  s.customer_id ASC;

```
### Output

| customer_id |visiting_count |
|-------------|--------------|
| A           |4            |
| B           |6            |
| C           |2            |

### 3. What was the first item from the menu purchased by each customer?
```sql
with ranks as (select customer_id , order_date , product_id , row_number() Over(partition by customer_id order by order_date) as roow_number from sales)

select r.customer_id , r.order_date , r.product_id , m.product_name from ranks r
join menu m
on r.product_id = m.product_id
where r.roow_number = 1
;

```

### Output

| customer_id |order_date | product_id |product_count |
|-------------|--------------|-------------|--------------|
| A           |2021-01-01            | 1           |sushi            |
| B           |2021-01-01            | 2           |curry            |
| C           |2021-01-01            | 3           |ramen            |

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
with max_order as (select s.customer_id , s.product_id , m.product_name from sales s
join
menu m 
on 
s.product_id = m.product_id)

select product_name , count(product_name) as ordered_count from max_order group by product_name order by ordered_count desc limit 1
;

```

### Output

| product_name |ordered_count |
|-------------|--------------|
| ramen           |8            |

### 5. Which item was the most popular for each customer?
```sql
with ranked_table as (select customer_id ,  product_id , count(product_id)  as count_orders, rank() over(partition by customer_id order by count(product_id) desc) as ranked_orders from sales s group by customer_id ,  product_id) 

select rt.customer_id , rt.product_id , m.product_name , rt.count_orders from ranked_table rt
join menu m
on 
rt.product_id = m.product_id
where rt.ranked_orders = 1
order by customer_id
;
```
| customer_id |product_id | product_name |count_orders |
|-------------|--------------|-------------|--------------|
| A           |3            | ramen           |3            |
| B           |1            | sushi           |2            |
| B           |2            | curry           |2            |
| B           |3            | ramesn           |2            |
| C           |3            | ramen           |3            |
