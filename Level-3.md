# Level 3: Grouping and Joins

> SQL interview preparation for fresher and entry-level roles.

Answers are written in natural fresher-friendly English. Speak at a comfortable pace and adjust wording to your own style.

## 21. What is GROUP BY, and why is it used?

**Interview answer:** GROUP BY combines rows that share the same value in one or more columns, allowing aggregate functions to calculate a result for each group. For example, a company may want to know the number of employees in every department instead of seeing only the total number of employees. I group by department_id and use COUNT to calculate each department's headcount. Columns in the SELECT list generally need to be grouped or used in an aggregate, depending on the database rules. Remember: GROUP BY changes row-level data into grouped summaries.

**Example / query:**

```sql
SELECT department_id, COUNT(*) AS employee_count FROM Employees GROUP BY department_id;
```

**Memory cue:** GROUP BY creates groups for summary calculations.

## 22. What is the difference between WHERE and HAVING when filtering grouped data?

**Interview answer:** WHERE filters rows before the groups are formed, while HAVING filters the grouped results after aggregation. Suppose I want average salary by department, but only for employees who are active. I can use WHERE to remove inactive employees first. Then I use GROUP BY department and HAVING to keep only departments whose average salary exceeds a threshold. Using WHERE for row conditions and HAVING for aggregate conditions makes the query easier to understand and can reduce the amount of data that needs grouping.

**Example / query:**

```sql
SELECT department_id, AVG(salary) AS avg_salary FROM Employees WHERE status = 'Active' GROUP BY department_id HAVING AVG(salary) > 50000;
```

**Memory cue:** WHERE = rows; HAVING = groups.

## 23. What is an INNER JOIN?

**Interview answer:** An INNER JOIN returns rows where the join condition matches in both tables. It is useful when information is stored in separate related tables. For example, Employees may store department_id, while Departments stores the department name. An INNER JOIN can show each employee alongside the name of their department. Employees whose department_id has no matching department are not returned by this join. I always check that the join condition uses the correct keys, because an incorrect join can create duplicate or misleading results.

**Example / query:**

```sql
SELECT e.employee_name, d.department_name FROM Employees e INNER JOIN Departments d ON e.department_id = d.department_id;
```

**Memory cue:** State the purpose, give a simple example, and mention the key trade-off or rule.

## 24. What is the difference between INNER JOIN and LEFT JOIN?

**Interview answer:** INNER JOIN returns only rows with matching values in both tables. LEFT JOIN returns every row from the left table and matching rows from the right table; if there is no match, right-table columns are returned as NULL. For example, INNER JOIN shows employees who have a matching department. LEFT JOIN can show all employees, including those whose department is not assigned or whose department record is missing. I choose the join based on whether unmatched rows should be excluded or retained.

**Example / query:**

```sql
SELECT e.employee_name, d.department_name FROM Employees e LEFT JOIN Departments d ON e.department_id = d.department_id;
```

**Memory cue:** INNER = matches only; LEFT = all left rows.

## 25. What is the difference between LEFT JOIN and RIGHT JOIN?

**Interview answer:** LEFT JOIN keeps every row from the table written on the left and adds matching rows from the right table. RIGHT JOIN keeps every row from the table written on the right. Both can express similar relationships if I reverse the table order and use the corresponding join. For readability, many developers prefer LEFT JOIN because it makes the preserved table easy to identify from left to right. For example, if I need every department even when it has no employees, I can place Departments on the left and use LEFT JOIN.

**Example / query:**

```sql
SELECT d.department_name, e.employee_name FROM Departments d LEFT JOIN Employees e ON d.department_id = e.department_id;
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 26. What is a FULL OUTER JOIN, and how can it be simulated in MySQL?

**Interview answer:** A FULL OUTER JOIN returns matching rows plus unmatched rows from both tables. MySQL does not directly support FULL OUTER JOIN syntax, so a common approach is to combine a LEFT JOIN with a RIGHT JOIN using UNION, taking care not to duplicate matching rows. This is useful when reconciling two lists and identifying records present in either source. The exact query should be tested with the data and the desired duplicate behavior. The key idea is that a full join preserves unmatched records from both sides.

**Example / query:**

```sql
SELECT a.id, a.name, b.name FROM A a LEFT JOIN B b ON a.id = b.id UNION SELECT a.id, a.name, b.name FROM A a RIGHT JOIN B b ON a.id = b.id;
```

**Memory cue:** FULL OUTER JOIN keeps matches and unmatched rows from both sides.

## 27. What is a CROSS JOIN?

**Interview answer:** A CROSS JOIN returns every possible combination of rows from two tables. If one table has three colors and another has four sizes, the result has twelve combinations. It is useful when generating combinations, such as product options or calendar scaffolding, but it can create a very large result if the tables contain many rows. Unlike an ordinary join, a CROSS JOIN does not need a matching condition. I use it intentionally and estimate the result size before running it.

**Example / query:**

```sql
SELECT c.color_name, s.size_name FROM Colors c CROSS JOIN Sizes s;
```

**Memory cue:** CROSS JOIN = every combination.

## 28. What is a SELF JOIN, and when would you use it?

**Interview answer:** A SELF JOIN joins a table to itself. It is useful when rows in a table are related to other rows in that same table. A common example is an employee table where each employee has a manager_id that refers to another employee's employee_id. I use aliases so SQL can treat the same table as two separate roles: one for the employee and one for the manager. The join condition connects the manager_id to the manager's ID. This lets me show employee and manager names in one result.

**Example / query:**

```sql
SELECT e.employee_name AS employee, m.employee_name AS manager FROM Employees e LEFT JOIN Employees m ON e.manager_id = m.employee_id;
```

**Memory cue:** Same table, two aliases, a relationship between rows.

## 29. What is the difference between UNION and UNION ALL?

**Interview answer:** UNION combines the results of two compatible SELECT queries and removes duplicate rows. UNION ALL combines them without removing duplicates, so it is often faster when duplicate removal is unnecessary. Both queries must return the same number of columns with compatible data types in corresponding positions. For example, if I combine current and archived orders, UNION gives unique result rows, while UNION ALL preserves every row from both sources. I choose based on the business requirement rather than automatically removing duplicates.

**Example / query:**

```sql
SELECT city FROM CurrentCustomers UNION ALL SELECT city FROM ArchivedCustomers;
```

**Memory cue:** UNION removes duplicates; UNION ALL keeps them.

## 30. How do you find rows in one table that have no matching rows in another table?

**Interview answer:** One common method is a LEFT JOIN followed by a filter for NULL on the right-hand table. Another option is NOT EXISTS. For example, if I want customers who have never placed an order, I start with Customers, left join Orders, and keep only rows where no order matched. I must check a column that cannot be NULL for a real matching order, such as its primary key. This is a common anti-join pattern for finding missing relationships or unmatched records.

**Example / query:**

```sql
SELECT c.customer_id, c.customer_name FROM Customers c LEFT JOIN Orders o ON o.customer_id = c.customer_id WHERE o.order_id IS NULL;
```

**Memory cue:** State the purpose, give a simple example, and mention the key trade-off or rule.
