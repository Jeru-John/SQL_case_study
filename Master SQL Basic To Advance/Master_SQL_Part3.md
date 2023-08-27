### JOINS:

<img width="492" alt="Screenshot 2023-08-06 at 8 38 09 PM" src="https://github.com/Jeru-John/SQL_case_study/assets/141055457/e5be5efd-d38b-4a2e-a3a9-00b538cf3cf0">


### UNIONS:

<img width="534" alt="Screenshot 2023-08-06 at 8 39 21 PM" src="https://github.com/Jeru-John/SQL_case_study/assets/141055457/2b098262-4f8f-4d8e-91c3-2a1737c97063">

<!-- screenshot-7 -->

### Assignment 6 [ UNIONS AND JOINS]: 

#### 1. Need the latest hired person and 1st hired person details in a table.

##### solution 1: 
```sql
SELECT * FROM (
    SELECT * FROM employee ORDER BY hire_date ASC LIMIT 1
) AS first_employee
UNION
SELECT * FROM (
    SELECT * FROM employee ORDER BY hire_date DESC LIMIT 1
) AS last_employee;
```

###### output:
| employee_id | first_name | last_name  | email                        | hire_date  | department | gender | salary | region_id |
|-------------|------------|------------|------------------------------|------------|------------|--------|--------|-----------|
| 271         | Norbie     | Bleasdille | nbleasdille7i@foxnews.com    | 2003-01-01 | First Aid  | M      | 82215  | 6         |
| 791         | Barby      | Daniell    | bdaniellly@cbc.ca            | 2016-12-26 | Clothing   | F      | 164588 |           |

##### solution 2:
```sql
SELECT first_name, hire_date FROM employees e
INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MIN(hire_date) FROM employees e)
UNION ALL
SELECT first_name, hire_date FROM employees e
INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MAX(hire_date) FROM employees e);
```

###### output:
| first_name | hire_date  |
|------------|------------|
| Norbie     | 2003-01-01 |
| Cassandra  | 2003-01-01 |
| Barby      | 2016-12-26 |



