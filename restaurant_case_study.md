# Restaurant Database Creation and Querying

This guide demonstrates the creation of a simple database for a restaurant, with the structure and querying process explained.

## Step 1: Database - Server Instance
```sql
CREATE DATABASE restaurant;
```

## Step 2: Schema - Folder-like Structure Inside a Database
```sql
CREATE SCHEMA choix_meals;
```

## Step 3: Creating Sales Table
```sql
CREATE TABLE sales (
  "customer_id" VARCHAR(1),
  "order_date" DATE,
  "product_id" INTEGER
);
```

## Step 4: Inserting Values into a Table
```sql
INSERT INTO sales
  ("customer_id", "order_date", "product_id")
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
```

## Step 5: Creating Menu Table
```sql
CREATE TABLE menu (
  "product_id" INTEGER,
  "product_name" VARCHAR(5),
  "price" INTEGER
);

INSERT INTO menu
  ("product_id", "product_name", "price")
VALUES
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
```

## Step 6: Creating Members Table
```sql
CREATE TABLE members (
  "customer_id" VARCHAR(1),
  "join_date" DATE
);

INSERT INTO members
  ("customer_id", "join_date")
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
```

## Step 7: Displaying Tables
```sql
SELECT * FROM sales;
SELECT * FROM menu;
SELECT * FROM members;
```

#### Output:

###### sales table:
| customer_id | order_date | product_id |
|---|---|---|
| A | 2021-01-01 | 1 |
| A | 2021-01-01 | 2 |
| A | 2021-01-07 | 2 |
| A | 2021-01-10 | 3 |
| A | 2021-01-11 | 3 |
| A | 2021-01-11 | 3 |
| B | 2021-01-01 | 2 |
| B | 2021-01-02 | 2 |
| B | 2021-01-04 | 1 |
| B | 2021-01-11 | 1 |
| B | 2021-01-16 | 3 |
| B | 2021-02-01 | 3 |
| C | 2021-01-01 | 3 |
| C | 2021-01-01 | 3 |
| C | 2021-01-07 | 3 |


###### menu table:
| product_id | product_name | price |
|---|---|---|
| 1 | sushi | 10 |
| 2 | curry | 15 |
| 3 | ramen | 12 |


###### members table:
| customer_id | join_date |
|---|---|
| A | 2021-01-07 |
| B | 2021-01-09 |

## Case Study Questions:

### 1.What is the total amount each customer spent at the restaurant?
```sql
SELECT customer_id, sum(sales.product_id * menu.price) AS amount_spent_by_customer
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id GROUP BY customer_id ;
```

###### output:
| customer_id | amount_spent_by_customer |
|---|---|
| A | 178 |
| B | 152 |
| C | 108 |

###### Inference: 
**Amount spent by customer A is $178, by customer B is $152 and by customer C is $108**

### 2.How many days has each customer visited the restaurant?
```sql
SELECT customer_id, count(order_date) AS days_spent_by_customer
FROM sales GROUP BY customer_id ;
```

###### output
| customer_id | days_spent_by_customer |
|---|---|
| A | 6 |
| B | 6 |
| C | 3 |

###### Inference: 
**For Customer C - spent only 3 days at the restaurant, while customer A and B spent 6 days at the restaurant.**

### 3.What was the first item from the menu purchased by each customer?
```sql
SELECT customer_id, min(order_date) AS FirstOrderDate, menu.product_name
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id GROUP BY customer_id ;
```

###### output
| customer_id | FirstOrderDate | product_name |
|---|---|---|
| A | 2021-01-01 | sushi |
| B | 2021-01-01 | curry |
| C | 2021-01-01 | ramen |

###### Inference: 
**For Customer A - sushi is the 1st ordered item.**
**For Customer B - curry is the 1st ordered item.**
**For Customer C - ramen is the 1st ordered item.**

### 4.What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
SELECT menu.product_name AS most_selled, COUNT(sales.product_id) AS no_of_times_selled
FROM sales 
INNER JOIN menu
ON sales.product_id = menu.product_id 
GROUP BY sales.product_id ORDER BY COUNT(sales.product_id) desc LIMIT 1;
```

###### output:
| product_name | no_of_times_selled |
|---|---|
| ramen | 8 |

###### Inference: 
**Ramen is the most selled product.**

### Which item was the most popular for each customer?

##### step 1: 
The below query will give the count based on grouping of customer_id and product_name from sales and menu table
```sql
SELECT s.customer_id, m.product_name, COUNT(*) as count 
  FROM sales s JOIN menu m ON s.product_id = m.product_id
  GROUP BY s.customer_id, m.product_name
```

###### output:
| customer_id | product_name | count |
|---|---|---|
| A | curry | 2 |
| A | ramen | 3 |
| A | sushi | 1 |
| B | curry | 2 |
| B | ramen | 2 |
| B | sushi | 2 |
| C | ramen | 3 |

##### step 2: 
The below query will give the rank based on the number of times a product exists.
```sql
  SELECT customer_id, product_name, count,
    RANK() OVER (PARTITION BY customer_id ORDER BY count DESC) as rank
  FROM (SELECT s.customer_id, m.product_name, COUNT(*) as count 
  FROM sales s JOIN menu m ON s.product_id = m.product_id
  GROUP BY s.customer_id, m.product_name);
```

###### output:
| customer_id | product_name | count | rank |
|---|---|---|---|
| A | ramen | 3 | 1 |
| A | curry | 2 | 2 |
| A | sushi | 1 | 3 |
| B | curry | 2 | 1 |
| B | ramen | 2 | 1 |
| B | sushi | 2 | 1 |
| C | ramen | 3 | 1 |

##### step 3:
With the 1st table as customer_purchases get the 2nd table as ranked_purchases. Then collect the detail only where the rank is 1.

```sql
WITH customer_purchases AS (
  SELECT s.customer_id, m.product_name, COUNT(*) as count
  FROM sales s
  JOIN menu m ON s.product_id = m.product_id
  GROUP BY s.customer_id, m.product_name
),
ranked_purchases AS (
  SELECT customer_id, product_name, count,
    RANK() OVER (PARTITION BY customer_id ORDER BY count DESC) as rank
  FROM customer_purchases
)
SELECT customer_id, product_name, count, rank
FROM ranked_purchases
WHERE rank = 1;
```

###### output:
| customer_id | product_name | count | rank |
|---|---|---|---|
| A | ramen | 3 | 1 |
| B | curry | 2 | 1 |
| B | ramen | 2 | 1 |
| B | sushi | 2 | 1 |
| C | ramen | 3 | 1 |

###### Inference: 
**For customer A - ramen is the most favourite or popular item.**
**For customer B - ramen, curry, sushi are at the same level of favourites**
**For customer C - ramen is the most favourite or popular item.**

### 6. Which item was purchased first by the customer after they became a member?
```sql
WITH member_purchases AS (
  SELECT 
    s.customer_id, 
    s.order_date, 
    me.product_name, 
    RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) as rank
  FROM 
    sales s
    JOIN menu me ON s.product_id = me.product_id
    JOIN members m ON s.customer_id = m.customer_id
  WHERE 
    s.order_date >= m.join_date
)
SELECT 
  customer_id, 
  product_name, 
  order_date
FROM 
  member_purchases
WHERE 
  rank = 1;
```

###### output:
| customer_id | product_name | order_date |
|---|---|---|
| A | curry | 2021-01-07 |
| B | sushi | 2021-01-11 |

###### Inference: 
**customer A has purchased 1st item after he got his membership as curry**
**customer B has purchased 1st item after he got his membership as sushi**

### 7. Which item was purchased just before the customer became a member?
```sql
WITH member_purchases AS (
  SELECT 
    s.customer_id, 
    s.order_date, 
    me.product_name, 
    RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date desc) as rank
  FROM 
    sales s
    JOIN menu me ON s.product_id = me.product_id
    JOIN members m ON s.customer_id = m.customer_id
  WHERE 
    s.order_date < m.join_date
)
SELECT 
  customer_id, 
  product_name, 
  order_date
FROM 
  member_purchases
WHERE 
  rank = 1;
```

###### output:
| customer_id | product_name | order_date |
|---|---|---|
| A | sushi | 2021-01-01 |
| A | curry | 2021-01-01 |
| B | sushi | 2021-01-04 |

###### Inference: 
**customer A has purchased sushi and curry just before he got to be a member of the restaurant**
**customer B has purchased sushi just before he got to be a member of the restaurant**

### 8. What is the total items and amount spent for each member before they became a member?
```sql
  SELECT 
    s.customer_id,
    GROUP_CONCAT(me.product_name, ', ') as purchased_products,
    sum(s.product_id*me.price) as price_total
  FROM 
    sales s
    JOIN menu me ON s.product_id = me.product_id
    JOIN members m ON s.customer_id = m.customer_id
  WHERE 
    s.order_date < m.join_date
  GROUP BY s.customer_id
```

###### output:
| customer_id | purchased_products | price_total |
|---|---|---|
| A | sushi, curry | 40 |
| B | curry, curry, sushi | 70 |

###### Inference: 
**customer A has purchased sushi,curry and spent $40 just before he got to be a member of the restaurant**
**customer B has purchased curry twice, sushi once and spent $70 just before he got to be a member of the restaurant**