### Assignment 1 [ SQL Query Basics ]:

<img width="442" alt="screenshot-1" src="https://github.com/Jeru-John/SQL_case_study/assets/141055457/211e07f9-670c-46c3-9951-19b2a026853d">

##### Create the above table:
```sql
CREATE TABLE students (student_no int, student_name varchar(10), age int);
```

###### adding primary key:
```sql
ALTER TABLE students ADD PRIMARY KEY (student_no);
```

###### inserting values:
```sql
INSERT INTO students VALUES(1, 'Michael', 19),(2,'Doug',18),(3,'Samantha',21),(4,'Pete',20),(5, 'Ralph',19), (6, 'Arnold', 22),(7, 'Michael', 19),(8, 'Jack', 19), (9, 'Rand', 17), (10, 'Sylvia', 20)
```

#### 1. Write a query to display the names of those students that are between the ages of 18 and 20.

##### way1: 
```sql
SELECT student_name FROM students WHERE age IN (18,19,20);
```

###### way 2: 
```sql
SELECT student_name FROM students WHERE age BETWEEN 18 AND 20;
```

###### output:
| student_name |
|---|
| Michael |
| Doug |
| Pete |
| Ralph |
| Michael |
| Jack |
| Sylvia |

#### 2.Write a query to display all of those students that contain the letters "ch" in their name or their name ends with the letters "nd".

##### solution:
```sql
SELECT DISTINCT(student_name) FROM students WHERE student_name LIKE '%ch%' OR student_name LIKE '%nd';
```

###### output:
| student_name |
|---|
| Michael |
| Rand |

#### 3. Write a query to display the name of those students that have the letters "ae" or "ph" in their name and are NOT 19 years old.

##### solution:
```sql
SELECT student_name, age FROM students WHERE (student_name LIKE '%ae%' OR student_name LIKE '%nph%') AND age!=19 ;
```

###### output:
the result set is empty.

#### 4.Write a query that lists the names of students sorted by their age from largest to smallest.
```sql
SELECT student_name, age FROM students ORDER BY age desc;
```

###### output:
| student_name | age |
|---|---|
| Arnold | 22 |
| Samantha | 21 |
| Pete | 20 |
| Sylvia | 20 |
| Michael | 19 |
| Ralph | 19 |
| Michael | 19 |
| Jack | 19 |
| Doug | 18 |
| Rand | 17 |

#### 5. Write a query that displays the names and ages of the top 4 oldest students.
```sql
SELECT student_name, age FROM students ORDER BY age desc LIMIT 4;
```
###### output:
| student_name | age |
|---|---|
| Arnold | 22 |
| Samantha | 21 |
| Pete | 20 |
| Sylvia | 20 |

#### 6. Write a query that returns students based on the following criteria:

The student must not be older than age 20 if their student_no is either between 3 and 5 or their student_no is 7. Your query should also return students older than age 20 but in that case they must have a student_no that is at least 4.

```sql
SELECT * FROM students WHERE AGE <= 20 AND ( student_no BETWEEN 3 AND 5 OR student_no = 7 ) OR (AGE > 20 AND student_no >= 4);
```

###### output:
| student_no |	student_name |	age |
|---|---|---|
| 4	| Pete | 20 |
| 5	| Ralph | 19 |
| 6 | Arnold | 22 |
| 7	| Michael | 19 |

### Assignment 2 [ Using Functions ]:

<img width="440" alt="screenshot-2" src="https://github.com/Jeru-John/SQL_case_study/assets/141055457/7c6aed46-a93c-4516-bd46-d6aba85d1914">

##### Create the above table:
```sql
CREATE TABLE professors (last_name varchar(20), department varchar(12), salary int, hire_date date);
```

```sql
INSERT INTO professors
VALUES
    ('Chong', 'Science', 88000, '2006-04-18'),
    ('Brown', 'Math', 97000, '2002-08-22'),
    ('Jones', 'History', 67000, '2009-11-17'),
    ('Wilson', 'Astronomy', 110000, '2005-01-15'),
    ('Miller', 'Agriculture', 82000, '2008-05-08'),
    ('Williams', 'Law', 105000, '2001-06-05');
```

#### 1. Write a query against the professors table that can output the following in the result: "Chong works in the Science department"
```sql
SELECT (last_name || ' works in the ' || department || ' department') AS output FROM professors WHERE last_name = 'Chong';
```

###### output:
| output |
|---|
| Chong works in the Science department |

#### 2. Write a SQL query against the professors table that would return the following result:

"It is false that professor Chong is highly paid"
"It is true that professor Brown is highly paid"
"It is false that professor Jones is highly paid"
"It is true that professor Wilson is highly paid"
"It is false that professor Miller is highly paid"
"It is true that professor Williams is highly paid"

NOTE: A professor is highly paid if they make greater than 95000.

##### solution(in mysql):
```sql
SELECT ('It is ' || CASE WHEN salary > 95000 THEN 'true' ELSE 'false' END || ' that professor ' || last_name || ' is highly paid') AS output FROM professors;
```
##### solution(in postgresql):
```sql
SELECT ('It is ' || (salary > 95000) || ' that professor ' || last_name || ' is highly paid') AS output FROM professors
```

###### output:
| output |
|---|
| It is false that professor Chong is highly paid |
| It is true that professor Brown is highly paid |
| It is false that professor Jones is highly paid |
| It is true that professor Wilson is highly paid |
| It is false that professor Miller is highly paid |
| It is true that professor Williams is highly paid |

#### 3. Write a query that returns all of the records and columns from the professors table but shortens the department names to only the first three characters in upper case.

##### solution(in mysql):
```sql
SELECT last_name, UPPER(SUBSTR(department, 1,3)) as department, salary, hire_date FROM professors;
```
##### solution(in postgresql):
```sql
SELECT last_name, UPPER(SUBSTRING(department, 1,3)) as department, salary, hire_date FROM professors;
```

###### output:
| last_name | department | salary | hire_date |
|---|---|---|---|
| Chong | SCI | 88000 | 2006-04-18 |
| Brown | MAT | 97000 | 2002-08-22 |
| Jones | HIS | 67000 | 2009-11-17 |
| Wilson | AST | 110000 | 2005-01-15 |
| Miller | AGR | 82000 | 2008-05-08 |
| Williams | LAW | 105000 | 2001-06-05 |

#### 4. Write a query that returns the highest and lowest salary from the professors table excluding the professor named 'Wilson'.

##### solution:
```sql
SELECT last_name, salary FROM professors WHERE last_name != 'Wilson' ORDER BY salary desc;
```

###### output:
| last_name | salary |
|---|---|
| Williams | 105000 |
| Brown | 97000 |
| Chong | 88000 |
| Miller | 82000 |
| Jones | 67000 |

#### 5. Write a query that will display the hire date of the professor that has been teaching the longest.

```sql
SELECT MIN(hire_date) AS longest_service_start_date FROM professors
```

###### output:
| longest_service_start_date |
|---|
| 2001-06-05 |
