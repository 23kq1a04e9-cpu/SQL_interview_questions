# Level 2: Filtering, Sorting, and Functions

> SQL interview preparation for fresher and entry-level roles.

Answers are written in natural fresher-friendly English. Speak at a comfortable pace and adjust wording to your own style.

## 11. What is the difference between IN and BETWEEN?

**Interview answer:** IN checks whether a value matches one of the values in a list. BETWEEN checks whether a value falls within a range, including both endpoints in standard SQL usage. For example, I can use IN to find employees from the Sales, HR, or IT departments. I can use BETWEEN to find salaries from 30,000 to 50,000. These operators make filtering easier to read than writing many OR conditions. One thing to remember is that BETWEEN includes the start and end values, so I should check whether inclusive boundaries match the requirement.

**Example / query:**

```sql
SELECT * FROM Employees WHERE department IN ('Sales', 'HR', 'IT');
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 12. What is the difference between LIKE and the equality operator (=)?

**Interview answer:** The equality operator checks for an exact match, while LIKE is used for pattern matching. In LIKE patterns, percent sign represents zero or more characters, and underscore represents exactly one character. For example, WHERE name = 'Ravi' finds the exact value Ravi. WHERE name LIKE 'R%' finds names that start with R, such as Ravi or Rani. LIKE is useful when we know only part of a value. I choose equals for exact comparisons and LIKE when the task asks for a prefix, suffix, or character pattern.

**Example / query:**

```sql
SELECT * FROM Employees WHERE employee_name LIKE 'R%';
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 13. How do AND, OR, and NOT work in a WHERE clause?

**Interview answer:** AND requires all connected conditions to be true, OR requires at least one condition to be true, and NOT reverses a condition. For example, I might find employees in the IT department whose salary is above 40,000 using AND. I might find employees from IT or HR using OR. NOT can exclude a department or a condition. I use parentheses when combining AND and OR so the intended logic is clear, because operator precedence can affect the result. The easy memory tip is: AND narrows, OR broadens, and NOT excludes or reverses.

**Example / query:**

```sql
SELECT * FROM Employees WHERE (department = 'IT' OR department = 'HR') AND salary > 40000;
```

**Memory cue:** AND narrows; OR broadens; NOT reverses.

## 14. What is the purpose of ORDER BY, and how do you sort in descending order?

**Interview answer:** ORDER BY sorts the rows returned by a query. By default, ascending order is commonly used, and DESC requests descending order. I can sort numbers from low to high, dates from earlier to later, or names alphabetically. For example, to see the highest-paid employees first, I sort salary in descending order. I can also sort by multiple columns, such as department first and salary second. Sorting changes the presentation of the results; it does not permanently rearrange how the data is stored in the table.

**Example / query:**

```sql
SELECT employee_name, salary FROM Employees ORDER BY salary DESC;
```

**Memory cue:** ORDER BY sorts output; DESC reverses the order.

## 15. What are aggregate functions in SQL? Explain COUNT, SUM, AVG, MIN, and MAX.

**Interview answer:** Aggregate functions calculate a result from multiple rows. COUNT counts rows or non-NULL values, SUM adds numeric values, AVG calculates an average, and MIN and MAX return the smallest and largest values. For example, a company may use SUM(salary) to estimate its total salary cost and AVG(salary) to understand the average salary. Most aggregate functions ignore NULL values in the expression, while COUNT(*) counts rows. These functions become especially useful with GROUP BY when we need results for each department, region, or category rather than one overall result.

**Example / query:**

```sql
SELECT COUNT(*) AS employee_count, AVG(salary) AS average_salary, MAX(salary) AS highest_salary FROM Employees;
```

**Memory cue:** COUNT counts; SUM adds; AVG averages; MIN/MAX find extremes.

## 16. What is the difference between COUNT(*) and COUNT(column_name)?

**Interview answer:** COUNT(*) counts every row in the result, including rows where some columns contain NULL. COUNT(column_name) counts only rows where that particular column is not NULL. For example, if a customer table has 100 rows but only 85 customers have entered an email address, COUNT(*) returns 100 while COUNT(email) returns 85. This difference is useful when checking data completeness. I remember it as: star counts rows; a column counts its non-NULL values. If the goal is to count unique values, I can use COUNT(DISTINCT column_name), depending on the requirement.

**Example / query:**

```sql
SELECT COUNT(*) AS all_customers, COUNT(email) AS customers_with_email FROM Customers;
```

**Memory cue:** COUNT(*) counts rows; COUNT(column) skips NULLs.

## 17. How do you handle NULL values using IS NULL, IS NOT NULL, and COALESCE?

**Interview answer:** IS NULL finds rows where a value is missing, and IS NOT NULL finds rows where a value is present. COALESCE returns the first non-NULL value from the values provided. For example, if some employees have no commission recorded, I can display zero instead of NULL for reporting. I should be careful, though: replacing NULL with zero is appropriate only when zero is a sensible business interpretation. NULL can mean unknown, not necessarily zero. So I use IS NULL to filter missing values and COALESCE when I deliberately need a fallback value.

**Example / query:**

```sql
SELECT employee_name, COALESCE(commission, 0) AS commission FROM Employees;
```

**Memory cue:** NULL is missing/unknown; test it with IS NULL.

## 18. What is the difference between a single-row function and an aggregate function?

**Interview answer:** A single-row function processes each row separately and returns a result for that row. Examples include UPPER, LOWER, and ROUND. An aggregate function combines values from multiple rows into one result per group, such as SUM or AVG. For example, UPPER(employee_name) changes each employee's name to uppercase, while AVG(salary) calculates an average across employees. Aggregate functions can also work with GROUP BY to return one result per group. The main difference is the input scope: single-row functions work row by row, while aggregate functions summarize multiple rows.

**Example / query:**

```sql
SELECT UPPER(employee_name) FROM Employees;
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 19. How do you extract the year or month from a date in SQL?

**Interview answer:** The exact syntax depends on the database system. In MySQL, YEAR(date_column) returns the year and MONTH(date_column) returns the month number. This is useful for reports that compare sales by year or count orders by month. For example, I can filter orders from 2025 or group them by month. When working with date ranges, I also consider whether a range filter is more efficient and accurate than applying a function to every date. The important point is to know the database dialect and use date functions consistently.

**Example / query:**

```sql
SELECT YEAR(order_date) AS order_year, MONTH(order_date) AS order_month FROM Orders;
```

**Memory cue:** Use database-specific date functions and verify the dialect.

## 20. How do you use CASE WHEN in a SQL query?

**Interview answer:** CASE WHEN lets me apply conditional logic inside a query, similar to if-else logic in programming. SQL checks each WHEN condition and returns the matching result; ELSE is used when none of the conditions match. For example, I can label salaries as High, Medium, or Low without changing the stored salary values. It is useful for creating readable categories, custom labels, and conditional calculations in reports. I make sure the conditions are ordered carefully so that a value matches the intended category first.

**Example / query:**

```sql
SELECT employee_name, CASE WHEN salary >= 60000 THEN 'High' WHEN salary >= 35000 THEN 'Medium' ELSE 'Low' END AS salary_band FROM Employees;
```

**Memory cue:** CASE WHEN = SQL's if/else-style logic.
