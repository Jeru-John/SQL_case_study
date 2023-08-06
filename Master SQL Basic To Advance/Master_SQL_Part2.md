### Assignment 3 [ Grouping and Aggregating ]:

<img width="450" alt="screenshot-3" src="https://github.com/Jeru-John/SQL_case_study/assets/141055457/02950baf-4101-4fb2-ae63-2c227c9b855d">

#### Creating the above table:

```sql
CREATE TABLE fruit_imports
(
	id integer,
	name varchar(20),
	season varchar(10),
	state varchar(20),
	supply integer,
	cost_per_unit decimal
);

insert into fruit_imports values(1, 'Apple', 'All Year', 'Kansas', 32900, 0.22), (2, 'Avocado', 'All Year', 'Nebraska', 27000, 0.15), (3, 'Coconut', 'All Year', 'California', 15200, 0.75), (4, 'Orange', 'Winter', 'California', 17000, 0.22), (5, 'Pear', 'Winter', 'Iowa', 37250, 0.17), (6, 'Lime', 'Spring', 'Indiana', 40400, 0.15), (7, 'Mango', 'Spring', 'Texas', 13650, 0.60), (8, 'Orange', 'Spring', 'Iowa', 18000, 0.26), (9, 'Apricot', 'Spring', 'Indiana', 55000, 0.20), (10, 'Cherry', 'Summer', 'Texas', 62150, 0.02), (11, 'Cantaloupe', 'Summer', 'Texas', 8000, 0.49), (12, 'Apricot', 'Summer', 'Kansas', 14500, 0.20), (13, 'Mango', 'Summer', 'Texas', 17000, 0.68), (14, 'Pear', 'Fall', 'Nebraska', 30500, 0.12), (15, 'Grape', 'Fall', 'Illinois', 72500, 0.35);
```

#### 1. Write a query that displays only the state with the largest amount of fruit supply.
```sql
Select sum(supply) as max_supply, state from fruit_imports
group by state order by sum(supply) desc limit 1;
```

###### output:
| max_supply | state |
|---|---|
| 100800 | Texas |

#### 2. Write a query that returns the most expensive cost_per_unit of every season. The query should display 2 columns, the season and the cost_per_unit
```sql
Select max(cost_per_unit) as sum_cost, season from fruit_imports
group by season;
```

###### output:
| sum_cost | season |
|---|---|
| 0.75 | All Year |
| 0.35 | Fall |
| 0.6 | Spring |
| 0.68 | Summer |
| 0.22 | Winter |

#### 3. Write a query that returns the state that has more than 1 import of the same fruit.
```sql
select state, name, count(name) AS import_counts from fruit_imports
group by state, name having count(name) > 1
```

###### output:
| state | name | import_counts |
|---|---|---|
| Texas | Mango | 2 |

#### 4. Write a query that returns the seasons that produce either 3 fruits or 4 fruits.
```sql
SELECT season, COUNT(name) AS import_counts FROM fruit_imports 
GROUP BY season HAVING count(name) = 3 OR count(name) = 4
```

###### output: 
| season | import_counts |
|---|---|
| All Year | 3 |
| Spring | 4 |
| Summer | 4 |

#### 5. Write a query that takes into consideration the supply and cost_per_unit columns for determining the total cost and returns the most expensive state with the total cost.
```sql
SELECT state, SUM(supply*cost_per_unit) total_cost FROM fruit_imports 
GROUP BY state ORDER BY total_cost desc LIMIT 1;
```

###### output:
| state | total_cost |
|---|---|
| Illinois | 25375 |

#### Execute the below SQL script and answer the question that follows:
```sql
CREATE table fruits (fruit_name varchar(10));
INSERT INTO fruits VALUES ('Orange');
INSERT INTO fruits VALUES ('Apple');
INSERT INTO fruits VALUES (NULL);
INSERT INTO fruits VALUES (NULL);
```
Write a query that returns the count of 4. You'll need to count on the column fruit_name and not use COUNT(*)
HINT: You'll need to use an additional function inside of count to make this work

##### solution:
```sql
SELECT COUNT(COALESCE(fruit_name, 'SOMEVALUE')) AS total_no_of_rows
FROM fruits;
```

###### output:
| total_no_of_rows |
|---|
| 4 |

### Assignment 4 [Using subqueries]: 

<img width="481" alt="screenshot-4" src="https://github.com/Jeru-John/SQL_case_study/assets/141055457/cd8ddd6a-40ad-4276-aded-14739efab61e">

#### creating the above tables:
1. students table:
```sql
CREATE TABLE students (
  student_no int NOT NULL,
  student_name varchar(20),
  age int,
  PRIMARY KEY (student_no));
```

2. courses table:
```sql
CREATE TABLE courses (
  course_no varchar(5),
  course_title varchar(20),
  credits int
  );
```

3. students_enrollment table:
```sql
CREATE TABLE students_enrollment (
  FOREIGN KEY (student_no) REFERENCES students(student_no),
  FOREIGN KEY (course_no) REFERENCES courses(course_no)
  )
```

#### 1. Using subqueries only, write a SQL statement that returns the names of those students that are taking the courses Physics and US History.
```sql
SELECT student_name
FROM students WHERE student_no
IN (SELECT student_no FROM student_enrollment WHERE course_no IN ( SELECT course_no FROM courses WHERE course_title IN ('Physics', 'US History')));
```

#### 2. Using subqueries only, write a query that returns the name of the student that is taking the highest number of courses.
```sql
SELECT student_name FROM students WHERE student_no IN ( SELECT student_no FROM (SELECT student_no, COUNT(course_no) course_cnt  FROM STUDENT_ENROLLMENT GROUP BY student_no ORDER BY course_cnt desc LIMIT 1 ) a )
```

#### 3. Write a query to find the student that is the oldest. You are not allowed to use LIMIT or the ORDER BY clause to solve this problem.
```sql
SELECT * FROM students WHERE age = (SELECT MAX(age) FROM students)
```

### Assignment 5: [CASE CLAUSE]

<img width="450" alt="screenshot-3" src="https://github.com/Jeru-John/SQL_case_study/assets/141055457/df82024a-244f-4414-9202-a4cee9e74a7e">

##### 1. Write a query that displays 3 columns. The query should display the fruit and it's total supply along with a category of either LOW, ENOUGH or FULL. Low category means that the total supply of the fruit is less than 20,000. The enough category means that the total supply is between 20,000 and 50,000. If the total supply is greater than 50,000 then that fruit falls in the full category.
```sql
SELECT name, sum(supply) AS total_supply, 
CASE
 WHEN sum(supply) < 20000 THEN 'LOW'
 WHEN sum(supply) BETWEEN 20000 AND 50000 THEN 'ENOUGH'
 ELSE 'FULL'
END AS category
FROM fruit_imports GROUP BY name
```

###### output:
| name       | total_supply | category |
|------------|--------------|----------|
| Apple      | 32,900       | ENOUGH   |
| Apricot    | 69,500       | FULL     |
| Avocado    | 27,000       | ENOUGH   |
| Cantaloupe | 8,000        | LOW      |
| Cherry     | 62,150       | FULL     |
| Coconut    | 15,200       | LOW      |
| Grape      | 72,500       | FULL     |
| Lime       | 40,400       | ENOUGH   |
| Mango      | 30,650       | ENOUGH   |
| Orange     | 35,000       | ENOUGH   |
| Pear       | 67,750       | FULL     |

#### 2. Taking into consideration the supply column and the cost_per_unit column, you should be able to tabulate the total cost to import fruits by each season. The result will look something like this:

"Winter" "10072.50"
"Summer" "19623.00"
"All Year" "22688.00"
"Spring" "29930.00"
"Fall" "29035.00"

Write a query that would transpose this data so that the seasons become columns and the total cost for each season fills the first row?

##### solution(horizontal):
```sql
SELECT season, SUM(supply*cost_per_unit) AS total_cost FROM fruit_imports GROUP BY season;
```
###### output:
| season   | total_cost                  |
|----------|-----------------------------|
| All Year | 22,688                      |
| Fall     | 29,035                      |
| Spring   | 29,930                      |
| Summer   | 19,623                      |
| Winter   | 10,072.5                    |

##### solution(vertically):
```sql
SELECT 
SUM(CASE WHEN season = 'Winter' THEN total_cost end) as Winter_total,
SUM(CASE WHEN season = 'Summer' THEN total_cost end) as Summer_total,
SUM(CASE WHEN season = 'Spring' THEN total_cost end) as Spring_total,
SUM(CASE WHEN season = 'Fall' THEN total_cost end) as Spring_total,
SUM(CASE WHEN season = 'All Year' THEN total_cost end) as Spring_total
FROM (
  select season, sum(supply * cost_per_unit) total_cost
  from fruit_imports
  group by season
) a
```

###### output:
| Winter_total | Summer_total | Spring_total | Fall_total | All_Year_total |
|--------------|--------------|--------------|------------|----------------|
| 10,072.5     | 19,623       | 29,930       | 29,035     | 22,688         |
