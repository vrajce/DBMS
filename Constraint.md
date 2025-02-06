
# Oracle SQL Constraints

Constraints in Oracle SQL are rules enforced on data in a table to maintain accuracy, integrity, and consistency. Below are the key types of constraints used when creating a table:

---

## 1. NOT NULL Constraint
The `NOT NULL` constraint ensures that a column cannot contain NULL values. It guarantees that every row must have a value for this column.

Domain Constraint.

### Example:
```sql
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    name VARCHAR2(100) NOT NULL
);
```

---

## 2. UNIQUE Constraint
The `UNIQUE` constraint ensures that all values in a column or combination of columns are distinct. It allows NULL values but ensures that non-NULL values are unique.

tuple uniqueness constraints.

### Example:
```sql
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    email VARCHAR2(150) UNIQUE
);
```

---

## 3. PRIMARY KEY Constraint
The `PRIMARY KEY` constraint is a combination of `NOT NULL` and `UNIQUE`. It ensures that each row in a table has a unique and non-NULL identifier. A table can have only one primary key.

Tuple uniqueness constraints.

### Example:
```sql
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    name VARCHAR2(100) NOT NULL
);
```

---

## 4. FOREIGN KEY Constraint
The `FOREIGN KEY` constraint establishes a relationship between two tables. It ensures that a column’s value in one table matches a value in another table's primary or unique key.

### Example:
```sql
CREATE TABLE departments (
    dept_id NUMBER PRIMARY KEY,
    dept_name VARCHAR2(100)
);

CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    name VARCHAR2(100) NOT NULL,
    dept_id NUMBER,
    CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);
```

### What is a Foreign Key?
A foreign key is a column (or set of columns) in a table that is used to establish a relationship between two tables. It links a column in the child table to the primary key of the parent table.

- **Parent Table:** The table that holds the primary key.
- **Child Table:** The table that references the primary key of the parent table.

### Why Use a Foreign Key?
- To enforce referential integrity: Ensure that the data in the child table always corresponds to valid data in the parent table.
- To prevent invalid data from being inserted into the child table.

### Example:
Step 1: Parent Table (departments)
```sql
CREATE TABLE departments (
    dept_id NUMBER PRIMARY KEY,
    dept_name VARCHAR2(100)
);
```
Data in `departments`:
| dept_id | dept_name |
|---------|-----------|
| 1       | HR        |
| 2       | Finance   |

Step 2: Child Table (employees)
```sql
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(100),
    dept_id NUMBER,
    CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);
```

### Inserting Data:
- **Valid Data:** The `dept_id` must exist in the `departments` table.
```sql
INSERT INTO departments (dept_id, dept_name) VALUES (1, 'HR');
INSERT INTO departments (dept_id, dept_name) VALUES (2, 'Finance');

INSERT INTO employees (emp_id, emp_name, dept_id) VALUES (101, 'Alice', 1);
INSERT INTO employees (emp_id, emp_name, dept_id) VALUES (102, 'Bob', 2);
```

- **Invalid Data:** If `dept_id` does not exist in the `departments` table, it will throw an error.
```sql
-- This will fail because dept_id = 3 does not exist in departments
INSERT INTO employees (emp_id, emp_name, dept_id) VALUES (103, 'Charlie', 3);
-- Error: ORA-02291: integrity constraint (FK_DEPT) violated - parent key not found
```

### Deleting Data:
Without `ON DELETE CASCADE`:
```sql
DELETE FROM departments WHERE dept_id = 1;
-- Error: ORA-02292: integrity constraint (FK_DEPT) violated - child record found
```

With `ON DELETE CASCADE`:
```sql
DELETE FROM departments WHERE dept_id = 1;
```
**Result:** The department with `dept_id = 1` is deleted from the `departments` table, and all employees with `dept_id = 1` are automatically deleted from the `employees` table.

---

## 5. CHECK Constraint
The `CHECK` constraint ensures that all values in a column satisfy a specific condition. You can use expressions to define constraints.

### Example:
```sql
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    salary NUMBER CHECK (salary > 0)
);
```

---

## 6. DEFAULT Constraint
The `DEFAULT` constraint assigns a default value to a column if no value is provided during insertion.

### Example:
```sql
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    name VARCHAR2(100),
    join_date DATE DEFAULT SYSDATE
);
```

---

## 7. COMPOSITE KEY
A composite key is a combination of two or more columns that uniquely identifies a row. It is often used when no single column can uniquely identify a row.

### Example:
```sql
CREATE TABLE project_assignments (
    emp_id NUMBER,
    project_id NUMBER,
    start_date DATE,
    PRIMARY KEY (emp_id, project_id)
);
```

---

## 8. ON DELETE CASCADE / SET NULL
The `ON DELETE CASCADE` clause is used in a foreign key constraint to define the behavior when a referenced row in the parent table is deleted. With this clause, if a row in the parent table is deleted, all corresponding rows in the child table are automatically deleted as well. This ensures that no orphaned rows are left in the child table.

### Example:
```sql
CREATE TABLE departments (
    dept_id NUMBER PRIMARY KEY,
    dept_name VARCHAR2(100)
);

CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(100),
    dept_id NUMBER,
    CONSTRAINT fk_dept FOREIGN KEY (dept_id)
        REFERENCES departments(dept_id)
        ON DELETE CASCADE
);
```

### Explanation:
Foreign Key Definition:
- The `dept_id` in the `employees` table references the `dept_id` in the `departments` table.
- The `ON DELETE CASCADE` ensures that when a department is deleted, all employees belonging to that department are also deleted.

### Behavior:
- **Insert Data:**
```sql
INSERT INTO departments (dept_id, dept_name) VALUES (1, 'HR');
INSERT INTO departments (dept_id, dept_name) VALUES (2, 'Finance');

INSERT INTO employees (emp_id, emp_name, dept_id) VALUES (101, 'Alice', 1);
INSERT INTO employees (emp_id, emp_name, dept_id) VALUES (102, 'Bob', 1);
INSERT INTO employees (emp_id, emp_name, dept_id) VALUES (103, 'Charlie', 2);
```

- **Delete Parent Row:**
```sql
DELETE FROM departments WHERE dept_id = 1;
```
**Result:** The rows in the `employees` table where `dept_id = 1` are automatically deleted.

### Advantages:
- **Maintains Data Integrity:** Prevents child rows from being orphaned.
- **Simplifies Cleanup:** Automatically removes dependent data.
- **Efficient Management:** Useful in hierarchies or relationships where dependent data becomes invalid if the parent record is removed.

### Important Considerations:
- **Use with Care:** Deleting a parent row can lead to a cascade of deletions in multiple child tables.
- **Avoid for Critical Data:** If child data is critical or requires preservation, consider alternatives like `ON DELETE SET NULL`.
- **Performance:** Cascading deletes can affect performance if many dependent rows are involved.

---

End of the Constraints Overview.
