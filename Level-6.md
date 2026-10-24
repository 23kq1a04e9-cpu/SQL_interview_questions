# Level 6: Design, Data Quality, and Performance

> SQL interview preparation for fresher and entry-level roles.

Answers are written in natural fresher-friendly English. Speak at a comfortable pace and adjust wording to your own style.

## 51. What is database normalization, and why is it important?

**Interview answer:** Normalization is the process of organizing relational tables to reduce unnecessary repetition and prevent data anomalies. Instead of storing the same department name in every employee row, we can store department details in a Departments table and refer to it through department_id. This makes updates more consistent and reduces the risk of one row having an old department name while another has the new name. Normalization uses rules called normal forms, such as 1NF, 2NF, and 3NF. The main goal is to structure data so it is reliable and easier to maintain.

**Example / query:**

```sql
Employees(employee_id, employee_name, department_id); Departments(department_id, department_name)
```

**Memory cue:** Reduce repeated facts and avoid data anomalies.

## 52. What are the differences between First Normal Form (1NF), Second Normal Form (2NF), and Third Normal Form (3NF)?

**Interview answer:** First Normal Form means each field contains a single, atomic value rather than a list of values in one cell. Second Normal Form requires 1NF and says each non-key attribute must depend on the whole candidate key, not just part of a composite key. Third Normal Form requires 2NF and removes certain transitive dependencies, so non-key attributes depend on the key rather than another non-key attribute. For example, storing department_name in an employee table when it depends on department_id can create a 3NF issue. I remember the progression as atomic values, whole key, and nothing but the key.

**Example / query:**

```sql
Example design: Students(student_id, student_name); Departments(department_id, department_name); Students.department_id references Departments.department_id.
```

**Memory cue:** 1NF = atomic; 2NF = whole key; 3NF = no transitive dependency.

## 53. What is Boyce-Codd Normal Form (BCNF)?

**Interview answer:** Boyce-Codd Normal Form is a stronger version of Third Normal Form. A table is in BCNF when every determinant is a candidate key; in simple terms, whenever an attribute or set of attributes determines another attribute, that determinant must be a candidate key. BCNF addresses some unusual dependency cases that 3NF can still allow. In a basic interview, I would explain that 3NF removes many transitive dependency problems, while BCNF is stricter about which columns are allowed to determine other columns. I would check the functional dependencies before deciding whether a table satisfies BCNF.

**Example / query:**

```sql
Conceptual example: if instructor_id determines course_id, then instructor_id must be a candidate key for that relation to satisfy BCNF.
```

**Memory cue:** 1NF = atomic; 2NF = whole key; 3NF = no transitive dependency.

## 54. What are insertion, update, and deletion anomalies in a database?

**Interview answer:** Anomalies are problems caused by storing multiple kinds of facts together with repeated data. An insertion anomaly happens when we cannot add one fact without adding an unrelated fact. An update anomaly happens when the same fact must be changed in several rows and one copy is missed. A deletion anomaly happens when removing one row accidentally removes the only copy of another important fact. For example, if department details are repeated in every employee row, deleting the last employee in a department might also remove the department information. Normalization helps prevent these problems.

**Example / query:**

```sql
Better design: store department details once in Departments and reference them from Employees.
```

**Memory cue:** Insertion = cannot add; update = repeated edits; deletion = lost fact.

## 55. What is denormalization, and when might it be useful?

**Interview answer:** Denormalization deliberately adds some repeated data or precomputed summaries to improve read performance or simplify reporting. It may be useful in analytics systems where dashboards repeatedly combine large tables. For example, a reporting table might store monthly sales totals so a dashboard does not recalculate them from millions of order lines every time. The trade-off is that repeated data needs to stay consistent and storage may increase. I would not denormalize without measuring the performance problem first. In short, normalization prioritizes data consistency; denormalization can prioritize faster reads when carefully managed.

**Example / query:**

```sql
Example: a monthly_sales_summary table storing month, product_id, total_sales.
```

**Memory cue:** Reduce repeated facts and avoid data anomalies.

## 56. What is an index, and how can it improve query performance?

**Interview answer:** An index is a data structure that helps the database find rows without scanning every row in a table. It is similar to an index in a book: it can make searches faster when queries filter, join, or sort on indexed columns. For example, an index on employee_id can help find one employee quickly. However, an index is not guaranteed to help every query; its usefulness depends on the data, query, and database optimizer. I check query plans and workload before adding indexes, rather than creating them randomly.

**Example / query:**

```sql
CREATE INDEX idx_employees_department ON Employees(department_id);
```

**Memory cue:** Faster reads can cost storage and slower writes.

## 57. What are the disadvantages of creating too many indexes?

**Interview answer:** Indexes can speed up reads, but they also have costs. Every insert, update, or delete may need to maintain relevant indexes, which can slow data changes. Indexes also consume storage and can increase maintenance work. Too many indexes may overlap or provide little benefit, so they should be based on real query patterns. For example, if a table receives many new orders each second, adding unnecessary indexes to many columns can make inserts slower. I review execution plans and usage before adding an index and remove redundant indexes only after checking their impact.

**Example / query:**

```sql
Example review: compare query plans before and after adding an index on a frequently filtered column.
```

**Memory cue:** Faster reads can cost storage and slower writes.

## 58. What is a transaction, and what do COMMIT, ROLLBACK, and SAVEPOINT do?

**Interview answer:** A transaction is a group of database operations treated as one logical unit. COMMIT makes the transaction's changes permanent. ROLLBACK cancels uncommitted changes, and SAVEPOINT creates a point within a transaction to which we can roll back. For example, transferring money between two accounts should subtract from one account and add to the other together. If the second operation fails, we should not leave the money removed from only one account. Transactions help protect data consistency, although exact behavior depends on the database and storage engine.

**Example / query:**

```sql
START TRANSACTION; UPDATE Accounts SET balance = balance - 100 WHERE account_id = 1; UPDATE Accounts SET balance = balance + 100 WHERE account_id = 2; COMMIT;
```

**Memory cue:** COMMIT saves; ROLLBACK cancels; SAVEPOINT marks a return point.

## 59. What are ACID properties in a database transaction?

**Interview answer:** ACID describes four important transaction properties. Atomicity means all operations happen or none do. Consistency means a transaction preserves the database's rules. Isolation means concurrent transactions are controlled so they do not interfere incorrectly. Durability means committed changes survive failures according to the database's guarantees. A bank transfer is a good example: money should not be removed from one account without being added to the other, and a committed transfer should remain saved. I remember ACID as the properties that help transactions stay reliable, especially when multiple users or failures are involved.

**Example / query:**

```sql
Example: transfer money between two accounts in one transaction, then commit only if both updates succeed.
```

**Memory cue:** Atomicity, Consistency, Isolation, Durability.

## 60. How would you investigate and improve a slow SQL query?

**Interview answer:** I would first reproduce the slow query and understand what result it needs to return. Then I would inspect the execution plan to see whether the database is scanning too many rows, using an inefficient join, or sorting a large result. I would check filters, join conditions, indexes, and whether the query returns more columns or rows than necessary. I would measure the change after each adjustment using realistic data, because adding an index or rewriting a query does not always improve performance. My approach is measure, inspect the plan, change one thing, and measure again.

**Example / query:**

```sql
Example first step: use EXPLAIN on the SELECT query and inspect the access method and indexes chosen.
```

**Memory cue:** Measure first, inspect EXPLAIN, change one thing, measure again.
