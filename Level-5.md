# Level 5: Window Functions and Query Challenges

> SQL interview preparation for fresher and entry-level roles.

Answers are written in natural fresher-friendly English. Speak at a comfortable pace and adjust wording to your own style.

## 41. What is a window function, and how is it different from an aggregate function?

**Interview answer:** A window function calculates a value across related rows while keeping each row in the result. A regular aggregate query with GROUP BY usually reduces each group to one output row. For example, AVG(salary) with GROUP BY department returns one average per department. AVG(salary) OVER (PARTITION BY department_id) can show each employee together with their department's average salary. Window functions are useful for ranking, running totals, and comparing a row with other rows without losing row-level details.

**Example / query:**

```sql
SELECT employee_name, department_id, salary, AVG(salary) OVER (PARTITION BY department_id) AS dept_avg FROM Employees;
```

**Memory cue:** Window functions keep row detail while calculating across related rows.

## 42. What is the purpose of the OVER() clause in a window function?

**Interview answer:** The OVER clause tells a window function which set of rows to use for its calculation. It can include PARTITION BY to divide rows into groups and ORDER BY to define an order within each group. For example, ROW_NUMBER() OVER (ORDER BY salary DESC) numbers employees from highest salary to lowest. If I add PARTITION BY department_id, numbering restarts for each department. Without understanding OVER, it is difficult to control how a window function calculates results. I remember it as the clause that defines the function's window.

**Example / query:**

```sql
SELECT employee_name, ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num FROM Employees;
```

**Memory cue:** Window functions keep row detail while calculating across related rows.

## 43. What is the difference between ROW_NUMBER(), RANK(), and DENSE_RANK()?

**Interview answer:** These three window functions assign positions, but they handle ties differently. ROW_NUMBER gives every row a different number, even when values tie. RANK gives tied rows the same rank and leaves a gap after the tie. DENSE_RANK gives tied rows the same rank but does not leave a gap. For example, salaries of 90, 90, and 80 receive row numbers 1, 2, 3; ranks 1, 1, 3; and dense ranks 1, 1, 2. I choose based on how the business defines ranking.

**Example / query:**

```sql
SELECT employee_name, salary, ROW_NUMBER() OVER (ORDER BY salary DESC) AS rn, RANK() OVER (ORDER BY salary DESC) AS rnk, DENSE_RANK() OVER (ORDER BY salary DESC) AS drnk FROM Employees;
```

**Memory cue:** ROW_NUMBER = unique positions; RANK = gaps after ties; DENSE_RANK = no gaps.

## 44. What is PARTITION BY, and how is it different from GROUP BY?

**Interview answer:** PARTITION BY divides rows into groups for a window function but keeps the individual rows in the output. GROUP BY combines rows into summary rows, usually one per group when using aggregates. For example, GROUP BY department_id with AVG(salary) returns one row per department. AVG(salary) OVER (PARTITION BY department_id) returns every employee and adds their department's average salary. I use GROUP BY when I want a summary and PARTITION BY when I need details and group-level calculations together.

**Example / query:**

```sql
SELECT employee_name, department_id, AVG(salary) OVER (PARTITION BY department_id) AS dept_avg FROM Employees;
```

**Memory cue:** Window functions keep row detail while calculating across related rows.

## 45. How do you find the second-highest salary in a table?

**Interview answer:** First, I clarify whether the question means the second-highest distinct salary or the second employee in sorted order. If it means the second-highest distinct salary, DENSE_RANK is a reliable approach because tied salaries share the same rank. I rank salaries from highest to lowest and select rank two. This avoids returning the same salary twice when several employees share the top salary. The query can be adjusted to return employee details as well. I always consider ties because they are a common interview edge case.

**Example / query:**

```sql
WITH Ranked AS (SELECT employee_name, salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk FROM Employees) SELECT employee_name, salary FROM Ranked WHERE rnk = 2;
```

**Memory cue:** Clarify distinct salary; DENSE_RANK handles ties.

## 46. How do you find the top three highest-paid employees in each department?

**Interview answer:** I use a window function with PARTITION BY department_id so ranking starts again in each department. Then I sort salaries in descending order and filter the ranking to the first three positions. If the interviewer means the top three salary levels including ties, I use DENSE_RANK; if they need at most three employees per department, ROW_NUMBER may be more appropriate, with a tie-breaker such as employee_id. This question tests both window functions and whether I clarify what 'top three' means.

**Example / query:**

```sql
WITH Ranked AS (SELECT employee_id, employee_name, department_id, salary, DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk FROM Employees) SELECT * FROM Ranked WHERE rnk <= 3;
```

**Memory cue:** State the purpose, give a simple example, and mention the key trade-off or rule.

## 47. How do you identify duplicate records in a table?

**Interview answer:** I first decide what makes a record a duplicate, because it may be a repeated email, repeated phone number, or a duplicate combination of several columns. Then I GROUP BY those columns and use HAVING COUNT(*) greater than one. For example, to find emails used by more than one customer, I group by email. I would usually exclude NULL values if they are not meaningful for the duplicate check. Identifying duplicates is different from deleting them; I inspect the results before taking any destructive action.

**Example / query:**

```sql
SELECT email, COUNT(*) AS occurrences FROM Customers WHERE email IS NOT NULL GROUP BY email HAVING COUNT(*) > 1;
```

**Memory cue:** Define duplicate columns first; inspect before deleting.

## 48. How do you delete duplicate records while keeping one copy?

**Interview answer:** I would first identify duplicates and back up or inspect the affected rows before deleting anything. A common method is to assign ROW_NUMBER to rows within each duplicate group, order them by a stable unique key, and delete rows numbered above one. The exact DELETE syntax varies by database, so I would test it in a transaction or safe environment first. Keeping the lowest ID is only an example; the correct row to preserve should follow the business rule. The important steps are define duplicates, choose the survivor, preview, then delete carefully.

**Example / query:**

```sql
WITH Duplicates AS (SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn FROM Customers) DELETE FROM Customers WHERE id IN (SELECT id FROM Duplicates WHERE rn > 1);
```

**Memory cue:** Define duplicate columns first; inspect before deleting.

## 49. How do you calculate a running total using a window function?

**Interview answer:** A running total adds the current row's value to the values that came before it in a defined order. I use SUM as a window function with ORDER BY, and I include a stable ordering column such as order_date and order_id so the result is predictable. For example, a sales report can show each day's sale and the cumulative sales up to that day. Unlike a grouped total, a running total keeps one output row for each original row. I also check the window frame behavior supported by the database.

**Example / query:**

```sql
SELECT order_date, order_id, amount, SUM(amount) OVER (ORDER BY order_date, order_id ROWS UNBOUNDED PRECEDING) AS running_total FROM Orders;
```

**Memory cue:** Window functions keep row detail while calculating across related rows.

## 50. How do you find the highest salary in each department and return the employee details?

**Interview answer:** I can rank employees within each department by salary in descending order using ROW_NUMBER or DENSE_RANK. Then I filter to rank one. ROW_NUMBER returns one employee per department if I add a deterministic tie-breaker, while DENSE_RANK returns all employees tied for the highest salary. I choose based on whether the requirement expects one row or every tied employee. This is safer than selecting MAX(salary) alone, because MAX gives the salary value but does not automatically return the employee's other columns.

**Example / query:**

```sql
WITH Ranked AS (SELECT employee_id, employee_name, department_id, salary, DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk FROM Employees) SELECT * FROM Ranked WHERE rnk = 1;
```

**Memory cue:** State the purpose, give a simple example, and mention the key trade-off or rule.
