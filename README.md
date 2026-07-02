# SQL Cheatsheet 📊

Welcome to the SQL Cheatsheet repository! This is a practical guide to SQL, covering data definition, queries, manipulation, control, and transactions. Whether you are a beginner or an experienced developer, this cheatsheet aims to simplify your SQL learning journey.

[![Download Releases](https://img.shields.io/badge/Download%20Releases-blue?style=for-the-badge&logo=github)](https://github.com/muhamadsuharto/sql-cheatsheet/releases)

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [SQL Basics](#sql-basics)
  - [Data Definition Language (DDL)](#data-definition-language-ddl)
  - [Data Manipulation Language (DML)](#data-manipulation-language-dml)
  - [Data Query Language (DQL)](#data-query-language-dql)
  - [Data Control Language (DCL)](#data-control-language-dcl)
  - [Transaction Control Language (TCL)](#transaction-control-language-tcl)
- [Advanced Topics](#advanced-topics)
- [Examples](#examples)
- [Contributing](#contributing)
- [License](#license)

## Introduction

SQL (Structured Query Language) is the standard language for managing and manipulating databases. This repository provides a concise overview of essential SQL commands and concepts. You can find the latest updates and releases [here](https://github.com/muhamadsuharto/sql-cheatsheet/releases).

## Getting Started

To get started with this cheatsheet, download the latest release from the link above. The release contains all necessary files to help you understand and practice SQL commands effectively.

## SQL Basics

### Data Definition Language (DDL)

DDL is used to define and manage all database objects. Here are some common DDL commands:

- **CREATE**: Used to create a new table or database.
- **ALTER**: Used to modify an existing database object.
- **DROP**: Used to delete a table or database.

**Example**:
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    position VARCHAR(50)
);
```

### Data Manipulation Language (DML)

DML is used for manipulating data within tables. Common DML commands include:

- **INSERT**: Adds new records to a table.
- **UPDATE**: Modifies existing records.
- **DELETE**: Removes records from a table.

**Example**:
```sql
INSERT INTO employees (id, name, position) VALUES (1, 'Alice', 'Manager');
```

### Data Query Language (DQL)

DQL is primarily concerned with querying data. The main command is:

- **SELECT**: Retrieves data from one or more tables.

**Example**:
```sql
SELECT * FROM employees WHERE position = 'Manager';
```

### Data Control Language (DCL)

DCL is used to control access to data in the database. Common DCL commands include:

- **GRANT**: Gives users access privileges to database objects.
- **REVOKE**: Removes access privileges.

**Example**:
```sql
GRANT SELECT ON employees TO user1;
```

### Transaction Control Language (TCL)

TCL manages transactions in the database. Key commands include:

- **COMMIT**: Saves all changes made during the current transaction.
- **ROLLBACK**: Undoes changes made during the current transaction.

**Example**:
```sql
BEGIN;
UPDATE employees SET position = 'Senior Manager' WHERE id = 1;
COMMIT;
```

## Advanced Topics

### Joins

Joins allow you to combine rows from two or more tables based on related columns. Common types of joins include:

- **INNER JOIN**: Returns records with matching values in both tables.
- **LEFT JOIN**: Returns all records from the left table and matched records from the right table.
- **RIGHT JOIN**: Returns all records from the right table and matched records from the left table.
- **FULL OUTER JOIN**: Returns records when there is a match in either left or right table records.

**Example**:
```sql
SELECT employees.name, departments.name 
FROM employees 
INNER JOIN departments ON employees.department_id = departments.id;
```

### Indexes

Indexes improve the speed of data retrieval operations on a database table. They can be created on one or more columns.

**Example**:
```sql
CREATE INDEX idx_employee_name ON employees (name);
```

### Views

A view is a virtual table based on the result set of a SQL statement. It can simplify complex queries.

**Example**:
```sql
CREATE VIEW manager_view AS 
SELECT * FROM employees WHERE position = 'Manager';
```

### Stored Procedures

Stored procedures are a set of SQL statements that can be stored and executed on the database server.

**Example**:
```sql
CREATE PROCEDURE GetEmployeeById (IN emp_id INT)
BEGIN
    SELECT * FROM employees WHERE id = emp_id;
END;
```

## Examples

Here are some practical examples to illustrate SQL commands:

### Creating a Table

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(10, 2)
);
```

### Inserting Data

```sql
INSERT INTO products (product_id, product_name, price) 
VALUES (1, 'Laptop', 999.99);
```

### Updating Data

```sql
UPDATE products 
SET price = 899.99 
WHERE product_id = 1;
```

### Deleting Data

```sql
DELETE FROM products 
WHERE product_id = 1;
```

## Contributing

Contributions are welcome! If you have suggestions or improvements, please create a pull request. You can also report issues or request new features.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

For more resources and updates, visit our [Releases](https://github.com/muhamadsuharto/sql-cheatsheet/releases) section. Happy coding!