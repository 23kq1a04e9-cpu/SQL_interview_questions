# Level 1: SQL Fundamentals

> SQL interview preparation for fresher and entry-level roles.

Answers are written in natural fresher-friendly English. Speak at a comfortable pace and adjust wording to your own style.

## 1. What is SQL, and what is it used for?

**Interview answer:** SQL stands for Structured Query Language. It is used to communicate with a relational database. With SQL, we can store, retrieve, update, and delete data. For example, a college database may contain student names, roll numbers, and marks. I can use a SELECT query to display students, an INSERT query to add a new student, or an UPDATE query to change a mark. SQL is useful because it lets us work with large amounts of organized data efficiently. A simple way to remember it is: SQL helps us manage data inside relational databases.

**Example / query:**

```sql
SELECT student_name, marks FROM Students;
```

**Memory cue:** State the purpose, give a simple example, and mention the key trade-off or rule.

## 2. What is the difference between SQL and MySQL?

**Interview answer:** SQL is a language, while MySQL is a database management system that understands and uses SQL. I think of SQL as the language we speak and MySQL as the system we speak to. SQL commands such as SELECT and INSERT can be used in MySQL to work with data. Other database systems, such as PostgreSQL and SQL Server, also use SQL, although their features and syntax can differ. So, SQL itself is not a database. MySQL is software that stores and manages databases.

**Example / query:**

```sql
SELECT * FROM Employees;
```

**Memory cue:** SQL = language; MySQL = database system.

## 3. What are the different types of SQL commands (DDL, DML, DQL, DCL, and TCL)?

**Interview answer:** SQL commands are commonly grouped by what they do. DDL defines database structures; examples are CREATE, ALTER, and DROP. DML changes data, using INSERT, UPDATE, and DELETE. DQL retrieves data, mainly with SELECT. DCL controls permissions, with commands such as GRANT and REVOKE. TCL manages transactions, using COMMIT, ROLLBACK, and sometimes SAVEPOINT. For example, CREATE TABLE makes a new table, while INSERT adds a row to it. Remember the groups as structure, data changes, queries, permissions, and transactions.

**Example / query:**

```sql
CREATE TABLE Students (student_id INT, student_name VARCHAR(50));
```

**Memory cue:** State the purpose, give a simple example, and mention the key trade-off or rule.

## 4. What is the difference between a primary key and a unique key?

**Interview answer:** A primary key uniquely identifies every row in a table, and it cannot contain NULL values. A table has only one primary-key constraint, although that key can contain multiple columns. A unique constraint also prevents duplicate values, but the way it handles NULL values can depend on the database system. A table can have multiple unique constraints. For example, student_id can be the primary key, while email can have a unique constraint so two students cannot use the same email. The key difference is that the primary key is the main identifier for each row.

**Example / query:**

```sql
CREATE TABLE Students (student_id INT PRIMARY KEY, email VARCHAR(100) UNIQUE);
```

**Memory cue:** Primary key = main row identifier; UNIQUE = no duplicate values.

## 5. What is a foreign key, and why is it used?

**Interview answer:** A foreign key connects a column in one table to a primary key or unique key in another table. It helps maintain referential integrity, which means we cannot create a relationship to a parent record that does not exist, subject to the database rules. For example, an Employees table may have a department_id that refers to the Departments table. This lets us connect employees to their departments without repeating department details for every employee. I remember it as a key that links related tables and helps prevent invalid relationships.

**Example / query:**

```sql
CREATE TABLE Employees (employee_id INT PRIMARY KEY, department_id INT, FOREIGN KEY (department_id) REFERENCES Departments(department_id));
```

**Memory cue:** A foreign key links tables and protects relationships.

## 6. What is the difference between WHERE and HAVING?

**Interview answer:** WHERE filters individual rows before grouping takes place. HAVING filters groups after GROUP BY has created them. For example, if I want only employees whose salary is above 30,000, I use WHERE. If I want only departments whose average salary is above 50,000, I use HAVING because I am filtering an aggregate result. This distinction is important when writing grouped queries. An easy memory tip is: WHERE filters rows; HAVING filters groups. In many queries, both can be used for different purposes.

**Example / query:**

```sql
SELECT department_id, AVG(salary) AS avg_salary FROM Employees GROUP BY department_id HAVING AVG(salary) > 50000;
```

**Memory cue:** WHERE = rows; HAVING = groups.

## 7. What is the difference between DELETE, TRUNCATE, and DROP?

**Interview answer:** DELETE removes selected rows from a table and can use a WHERE condition. TRUNCATE removes all rows from a table more directly, and its transactional and identity-reset behavior depends on the database system. DROP removes the table object itself, including its structure. For example, DELETE can remove one employee whose ID is 10, while TRUNCATE empties the employee table and DROP removes the table entirely. I always check the command carefully before running it, especially in a production database, because these operations can have significant effects.

**Example / query:**

```sql
DELETE FROM Employees WHERE employee_id = 10;
```

**Memory cue:** DELETE = selected rows; TRUNCATE = all rows; DROP = table structure.

## 8. What is the difference between CHAR and VARCHAR?

**Interview answer:** CHAR is a fixed-length character type, while VARCHAR stores variable-length text up to a defined limit. CHAR can be suitable for values that have a consistent length, such as a two-character code. VARCHAR is usually more suitable for names, emails, or addresses because their lengths vary. For example, a country code might use CHAR(2), while a person's name could use VARCHAR(100). Exact storage behavior can differ by database system, but the simple idea is fixed length versus variable length. Choosing an appropriate type makes the schema clearer and can avoid unnecessary storage.

**Example / query:**

```sql
country_code CHAR(2), person_name VARCHAR(100)
```

**Memory cue:** IN = a list; BETWEEN = an inclusive range.

## 9. What is NULL in SQL, and how is it different from zero or an empty string?

**Interview answer:** NULL means a value is missing, unknown, or not provided. It is not the same as zero, which is a numeric value, or an empty string, which is text containing no characters. Because NULL represents an unknown value, we usually check it with IS NULL or IS NOT NULL instead of using equals. For example, if an employee has not provided a phone number, phone may be NULL. A phone value of an empty string means something different. Remember: NULL means missing or unknown, zero is a number, and an empty string is text.

**Example / query:**

```sql
SELECT * FROM Employees WHERE phone IS NULL;
```

**Memory cue:** NULL is missing/unknown; test it with IS NULL.

## 10. What is the purpose of DISTINCT in a SELECT query?

**Interview answer:** DISTINCT returns unique combinations of the selected columns by removing duplicate result rows. It is useful when I want to see which departments exist or which cities appear in a customer table without listing the same value repeatedly. For example, a customer table may contain hundreds of customers from only ten cities. SELECT DISTINCT city returns each city once. If I select two columns, the distinct combination of both columns is considered, not each column independently. I also avoid using DISTINCT automatically, because it can hide duplicates caused by an incorrect join.

**Example / query:**

```sql
SELECT DISTINCT city FROM Customers;
```

**Memory cue:** DISTINCT removes duplicate result rows.
