# Level 4: Subqueries and CTEs

> SQL interview preparation for fresher and entry-level roles.

Answers are written in natural fresher-friendly English. Speak at a comfortable pace and adjust wording to your own style.

## 31. What is a subquery, and what types of subqueries can you use?

**Interview answer:** A subquery is a query written inside another SQL statement. It can return a single value, a list of values, or a table-like result, depending on how it is used. For example, I can find employees whose salary is higher than the company's average salary by calculating the average in a subquery. Subqueries can appear in places such as WHERE, FROM, or SELECT, although readability and performance depend on the query and database optimizer. I use a subquery when one query's result is needed to answer the outer query.

**Example / query:**

```sql
SELECT employee_name, salary FROM Employees WHERE salary > (SELECT AVG(salary) FROM Employees);
```

**Memory cue:** An inner query supplies a result to an outer query.

## 32. What is the difference between a correlated and a non-correlated subquery?

**Interview answer:** A non-correlated subquery can run independently of the outer query because it does not refer to columns from the outer query. A correlated subquery uses values from the current outer row, so its result depends on that row. For example, finding employees above the overall average salary can use a non-correlated subquery. Finding employees whose salary is above the average salary of their own department is a correlated example. Database optimizers may transform these queries, so I focus on understanding the dependency and checking performance with realistic data.

**Example / query:**

```sql
SELECT e.employee_name FROM Employees e WHERE e.salary > (SELECT AVG(e2.salary) FROM Employees e2 WHERE e2.department_id = e.department_id);
```

**Memory cue:** An inner query supplies a result to an outer query.

## 33. What is the difference between EXISTS and IN?

**Interview answer:** IN checks whether a value matches any value returned by a list or subquery. EXISTS checks whether the subquery returns at least one row. Both can solve similar problems, such as finding customers who have orders. EXISTS is often convenient when the relationship is the important part, while IN can be clear for a short list of values. NULL handling can make their behavior different in some cases, especially with NOT IN, so I avoid assuming they are always interchangeable. I choose the form that expresses the requirement clearly and verify its behavior.

**Example / query:**

```sql
SELECT c.customer_name FROM Customers c WHERE EXISTS (SELECT 1 FROM Orders o WHERE o.customer_id = c.customer_id);
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 34. What is the difference between EXISTS and JOIN?

**Interview answer:** JOIN combines columns from related rows in the output. EXISTS is usually used to test whether at least one matching row exists, without adding columns from the inner query to the result. If a customer has five orders, a join can return that customer five times unless I aggregate or use DISTINCT. EXISTS can return the customer once because it checks for a match rather than listing every matching order. I use JOIN when I need data from both tables and EXISTS when I only need to know whether a related record is present.

**Example / query:**

```sql
SELECT c.customer_id, c.customer_name FROM Customers c WHERE EXISTS (SELECT 1 FROM Orders o WHERE o.customer_id = c.customer_id);
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 35. What is a Common Table Expression (CTE)?

**Interview answer:** A Common Table Expression, or CTE, is a named temporary result defined with the WITH clause for use in a single SQL statement. It helps break a complex query into readable steps. For example, I can first calculate total sales for each customer in a CTE and then select customers whose total exceeds a threshold. A CTE does not automatically mean the data is permanently stored, and its execution behavior depends on the database system. I use CTEs to make complex logic easier to read, test, and maintain.

**Example / query:**

```sql
WITH CustomerTotals AS (SELECT customer_id, SUM(amount) AS total_amount FROM Orders GROUP BY customer_id) SELECT * FROM CustomerTotals WHERE total_amount > 10000;
```

**Memory cue:** CTE = a named result for one statement.

## 36. What is the difference between a CTE and a subquery?

**Interview answer:** A subquery is nested inside another query, while a CTE is given a name at the start of a statement using WITH. Both can express similar logic, and neither automatically guarantees better performance. CTEs can make a long query easier to understand because each step has a meaningful name. Subqueries can be concise for a small calculation used once. For example, I might use a subquery to compare salary with the overall average, but use a CTE when a query has several stages. I choose based on clarity and the database's behavior.

**Example / query:**

```sql
WITH AveragePay AS (SELECT AVG(salary) AS avg_salary FROM Employees) SELECT e.employee_name FROM Employees e CROSS JOIN AveragePay a WHERE e.salary > a.avg_salary;
```

**Memory cue:** CTE = a named result for one statement.

## 37. What is a recursive CTE, and when might it be useful?

**Interview answer:** A recursive CTE refers to itself to process hierarchical or repeated relationships. It usually has an anchor query that starts the result and a recursive query that adds the next level. A common use is finding all employees under a manager or exploring a category tree. For example, the anchor selects the top manager, and the recursive part finds employees whose manager_id matches a previously found employee. Recursive queries need a clear stopping condition to avoid endless recursion. I use them when the number of hierarchy levels is not fixed.

**Example / query:**

```sql
WITH RECURSIVE Org AS (SELECT employee_id, manager_id, employee_name FROM Employees WHERE manager_id IS NULL UNION ALL SELECT e.employee_id, e.manager_id, e.employee_name FROM Employees e JOIN Org o ON e.manager_id = o.employee_id) SELECT * FROM Org;
```

**Memory cue:** CTE = a named result for one statement.

## 38. What is the difference between ANY and ALL in SQL?

**Interview answer:** ANY compares a value with the results of a subquery and succeeds if the comparison is true for at least one returned value. ALL requires the comparison to be true for every returned value. For example, salary greater than ANY salaries in the Sales department means it is greater than at least one of those salaries. Salary greater than ALL means it is greater than every salary in that group. These operators can express comparisons against a set without manually calculating a single value. I pay attention to empty sets and NULLs because they can affect logic.

**Example / query:**

```sql
SELECT employee_name FROM Employees WHERE salary > ALL (SELECT salary FROM Employees WHERE department_id = 3);
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 39. What is a view, and how is it different from a table?

**Interview answer:** A table stores data as database rows and columns. A view is a named query that presents data from one or more tables as a virtual table. A standard view usually stores the query definition rather than a separate copy of the results, although materialized views in systems that support them can store results. Views can simplify complicated queries and limit which columns users see. For example, I could create a view that shows employee names and departments but excludes confidential salary information. I still need to understand the underlying tables and permissions.

**Example / query:**

```sql
CREATE VIEW PublicEmployees AS SELECT employee_id, employee_name, department_id FROM Employees;
```

**Memory cue:** Table stores data; view presents a saved query.

## 40. What is a stored procedure, and how is it different from a function?

**Interview answer:** A stored procedure is a saved group of SQL statements that can be executed to perform a task. A function is generally designed to return a value or a table, depending on the database system, and can often be used within expressions. Procedures are useful for workflows such as processing a batch of records, while functions can encapsulate reusable calculations. The exact rules differ across MySQL, SQL Server, and PostgreSQL. In an interview, I explain the general purpose first, then mention that syntax and capabilities depend on the database platform.

**Example / query:**

```sql
CALL UpdateEmployeeStatus(101, 'Active');
```

**Memory cue:** Procedure runs a saved task; function returns a value/result.
