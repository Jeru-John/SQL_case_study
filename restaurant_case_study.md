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

### 3.What was the first item from the menu purchased by each customer?
```sql
SELECT customer_id, min(order_date) AS FirstOrderDate, menu.product_name
FROM sales
INNER JOIN menu
ON sales.product_id = menu.product_id GROUP BY customer_id ;
```

###### output
| customer_id | FirstOrderDate | product_name
|---|---|---|
| A | 2021-01-01 | sushi |
| B | 2021-01-01 | curry |
| C | 2021-01-01 | ramen |

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