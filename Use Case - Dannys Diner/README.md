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
### Output

| customer_id |product_id | product_name |count_orders |
|-------------|--------------|-------------|--------------|
| A           |3            | ramen           |3            |
| B           |1            | sushi           |2            |
| B           |2            | curry           |2            |
| B           |3            | ramesn           |2            |
| C           |3            | ramen           |3            |

### 6. Which item was purchased first by the customer after they became a member?
```sql
with first_purchase as (select s.customer_id , m.join_date , s.order_date ,s.product_id , row_number() over(partition by s.customer_id order by s.order_date) as row_numbr from sales s join members m on s.customer_id = m.customer_id where s.order_date > m.join_date)

select fp.customer_id , m.product_name from first_purchase fp
join
menu m
on 
fp.product_id = m.product_id
where row_numbr = 1
order by fp.customer_id
;
```
### Output

| customer_id |product_name |
|-------------|--------------|
| A           |ramen            |
| B           |sushi            |

### 7. Which item was purchased just before the customer became a member?
```sql
with cte as (select sales.customer_id ,max(sales.order_date) as last_day_b4_membership_purchase, sales.product_id , row_number() over(partition by customer_id order by max(sales.order_date) desc) as just_b4 from sales join members on sales.customer_id = members.customer_id where members.join_date > sales.order_date group by sales.customer_id , sales.product_id)

select c.customer_id , m.product_name from cte c join menu m on c.product_id = m.product_id where just_b4 = 1
;
```

### Output

| customer_id |product_name |
|-------------|--------------|
| A           |sushi            |
| B           |sushi            |

### 8. What is the total items and amount spent for each member before they became a member?
```sql
with cte as (SELECT 
    sales.customer_id,
    (sales.order_date),
    members.join_date,
    sales.product_id,
    menu.price
FROM
    sales
        JOIN
    members ON sales.customer_id = members.customer_id
		JOIN
	menu ON sales.product_id = menu.product_id
WHERE
    members.join_date > sales.order_date)
    
select customer_id , count(product_id) as total_items, sum(price) as total_spent from cte group by customer_id  order by customer_id  
    ;
```
### Output

| customer_id |total_items |total_spent|
|-------------|--------------|--------------|
| A           |2            |25            |
| B           |3            |40            |
