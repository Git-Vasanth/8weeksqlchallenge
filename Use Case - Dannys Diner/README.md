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

#### Interpretation:

This query calculates the total spending of each customer by joining sales data with the menu to get the prices of the products purchased.

#### Output: 

Customer A spent $76, B spent $74, and C spent $36.

#### Business Insight:

Customer A is the highest spender, which might indicate loyalty or a preference for more expensive items. This could inform marketing strategies targeting high-value customers.

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

#### Interpretation:

This query counts the unique days each customer visited the restaurant.

#### Output:

A visited 4 days, B 6 days, and C 2 days.

#### Business Insight:

Customer B is the most frequent visitor. Understanding visit frequency can help identify loyal customers and tailor promotions or loyalty programs.

### 3. What was the first item from the menu purchased by each customer?
```sql
with ranks as (select customer_id , order_date , product_id , row_number() Over(partition by customer_id order by order_date) as roow_number from sales)

select r.customer_id , r.order_date , r.product_id , m.product_name from ranks r
join menu m
on r.product_id = m.product_id
where r.roow_number = 1
;

```

#### Interpretation:

This query identifies the first menu item each customer purchased.

#### Output:

A bought sushi, B bought curry, and C bought ramen on their first visit.
#### Business Insight:

Knowing first purchases can help in crafting personalized marketing strategies, encouraging customers to try new items based on their initial choices.

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

#### Interpretation:

This query finds the most frequently ordered item across all customers.

#### Output:

Ramen was ordered 8 times.

#### Business Insight:

Ramen is a best-seller and could be featured in promotions or bundled offers to attract more customers.

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

#### Interpretation:

This query identifies the most popular item for each customer based on their order history.

#### Output:

A's favorite is ramen, B's is sushi and curry, and C's is ramen.

#### Business Insight:

This data can help in personalizing customer experiences, perhaps by suggesting favorite items or introducing new dishes similar to their preferences.

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

#### Interpretation:

This query finds the first item purchased by customers after they became members.

#### Output:

A bought ramen, B bought sushi.

#### Business Insight:

This information could guide promotional strategies for new members, highlighting items they might like based on their first purchases.

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

#### Interpretation:

This query identifies the item purchased just before customers became members.

#### Output:

Both A and B purchased sushi.

#### Business Insight:
This could indicate a strong preference for sushi among members, which can be leveraged in loyalty programs or special promotions for new members.

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

#### Interpretation:

This query calculates how much each member spent and the total number of items they purchased before joining.

#### Output:

A purchased 2 items for $25, B purchased 3 items for $40.

#### Business Insight:

Understanding spending habits before membership can inform marketing strategies to retain customers and encourage them to join loyalty programs.

### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
SELECT 
    s.customer_id,
    SUM(CASE
        WHEN m.product_id = 1 THEN m.price * 20
        ELSE m.price * 10
    END) AS total_points
FROM
    sales s
        JOIN
    menu m ON s.product_id = m.product_id
GROUP BY s.customer_id;
```

### Output

| customer_id |total_points |
|-------------|--------------|
| A           |860            |
| B           |940            |
| C           |360            |

#### Interpretation:

This query calculates loyalty points for each customer based on their spending.

#### Output:

A has 860 points, B has 940, C has 360.

#### Business Insight:

This points system incentivizes spending. High points for high spenders can be used to encourage repeat visits or to create targeted promotions.

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```sql
SELECT 
    s.customer_id,
    SUM(
        CASE
            WHEN s.order_date BETWEEN mem.join_date AND DATE_ADD(mem.join_date, INTERVAL 7 DAY) 
                THEN m.price * 20  
            ELSE 
                m.price * 10  
        END
    ) AS total_points 
FROM 
    sales s
JOIN 
    members mem ON s.customer_id = mem.customer_id
JOIN 
    menu m ON s.product_id = m.product_id
WHERE 
    s.order_date <= '2021-01-31'  -- Filter to end of January
GROUP BY 
    s.customer_id
ORDER BY 
    s.customer_id;
```
### Output

| customer_id |total_points |
|-------------|--------------|
| A           |1270           |
| B           |840            |

#### Interpretation:

This query calculates the points earned by customers A and B during their first week of membership.

#### Output:

A earned 1270 points, B earned 840 points.

#### Business Insight:

Customers earn more points initially, which encourages spending right after joining. This data can help assess the effectiveness of the loyalty program and adjust strategies accordingly.
