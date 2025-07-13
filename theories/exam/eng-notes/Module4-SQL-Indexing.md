# Module 4: Structured Query Language (SQL) & Indexing

## Overview of SQL

**SQL (Structured Query Language):** A **standard programming language** designed for managing and manipulating relational databases.

### **Key Features:**
- **Declarative language** - specify what you want, not how to get it
- **Standardized** - works across different database systems
- **Case insensitive** - SELECT = select = Select
- **Comprehensive** - handles data definition, manipulation, and control

### **SQL Categories:**

| **Category** | **Purpose** | **Commands** |
|---|---|---|
| **DDL** (Data Definition) | Define structure | CREATE, ALTER, DROP |
| **DML** (Data Manipulation) | Manipulate data | SELECT, INSERT, UPDATE, DELETE |
| **DCL** (Data Control) | Access control | GRANT, REVOKE |
| **TCL** (Transaction Control) | Transaction management | COMMIT, ROLLBACK, SAVEPOINT |

**Marks: 4-5 marks**

---

## Data Definition Commands (DDL)

### **CREATE TABLE**
**Purpose:** Create new table structure

**Syntax:**
```sql
CREATE TABLE table_name (
    column1 datatype constraints,
    column2 datatype constraints,
    ...
    table_constraints
);
```

**Example:**
```sql
CREATE TABLE STUDENT (
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Age INT CHECK (Age > 0 AND Age < 100),
    Email VARCHAR(100) UNIQUE,
    Course_ID INT,
    Enrollment_Date DATE DEFAULT CURRENT_DATE,
    FOREIGN KEY (Course_ID) REFERENCES COURSE(Course_ID)
);
```

### **ALTER TABLE**
**Purpose:** Modify existing table structure

**Operations:**
```sql
-- Add column
ALTER TABLE STUDENT ADD Phone VARCHAR(15);

-- Modify column
ALTER TABLE STUDENT MODIFY Name VARCHAR(100);

-- Drop column  
ALTER TABLE STUDENT DROP COLUMN Phone;

-- Add constraint
ALTER TABLE STUDENT ADD CONSTRAINT chk_age CHECK (Age >= 18);

-- Drop constraint
ALTER TABLE STUDENT DROP CONSTRAINT chk_age;
```

### **DROP TABLE**
**Purpose:** Delete table and all its data

```sql
DROP TABLE STUDENT;  -- Complete removal
```

### **Data Types:**

| **Category** | **Types** | **Examples** |
|---|---|---|
| **Numeric** | INT, DECIMAL, FLOAT | Age INT, Price DECIMAL(10,2) |
| **String** | VARCHAR, CHAR, TEXT | Name VARCHAR(50), Description TEXT |
| **Date/Time** | DATE, TIME, TIMESTAMP | Birth_Date DATE, Created_At TIMESTAMP |
| **Boolean** | BOOLEAN | Is_Active BOOLEAN |

**DDL Commands: 6-7 marks**

---

## Data Manipulation Commands (DML)

### **INSERT Statement**
**Purpose:** Add new records to table

**Syntax Variations:**
```sql
-- Insert all columns
INSERT INTO STUDENT VALUES (101, 'John', 20, 'john@email.com', 1, '2024-01-15');

-- Insert specific columns
INSERT INTO STUDENT (Student_ID, Name, Age) VALUES (102, 'Mary', 19);

-- Insert multiple records
INSERT INTO STUDENT (Student_ID, Name, Age) VALUES 
(103, 'David', 21),
(104, 'Sarah', 20),
(105, 'Mike', 22);

-- Insert from another table
INSERT INTO PASS_STUDENTS 
SELECT * FROM STUDENT WHERE Grade = 'A';
```

### **UPDATE Statement**
**Purpose:** Modify existing records

**Syntax:**
```sql
-- Basic update
UPDATE STUDENT 
SET Age = 21 
WHERE Student_ID = 101;

-- Update multiple columns
UPDATE STUDENT 
SET Age = Age + 1, Email = 'newemail@domain.com' 
WHERE Student_ID = 102;

-- Conditional update
UPDATE STUDENT 
SET Grade = 'A+' 
WHERE Marks > 90;

-- Update with subquery
UPDATE STUDENT 
SET Course_ID = (SELECT Course_ID FROM COURSE WHERE Course_Name = 'Computer Science')
WHERE Name = 'John';
```

### **DELETE Statement**
**Purpose:** Remove records from table

**Syntax:**
```sql
-- Delete specific records
DELETE FROM STUDENT WHERE Age < 18;

-- Delete with multiple conditions
DELETE FROM STUDENT WHERE Grade = 'F' AND Attendance < 75;

-- Delete all records (structure remains)
DELETE FROM STUDENT;

-- Delete with subquery
DELETE FROM STUDENT 
WHERE Course_ID IN (SELECT Course_ID FROM COURSE WHERE Status = 'Discontinued');
```

### **SELECT Statement**
**Purpose:** Retrieve data from tables

**Basic Syntax:**
```sql
-- All columns
SELECT * FROM STUDENT;

-- Specific columns
SELECT Name, Age, Email FROM STUDENT;

-- With conditions
SELECT * FROM STUDENT WHERE Age > 20;

-- With sorting
SELECT * FROM STUDENT ORDER BY Age DESC, Name ASC;

-- With limit
SELECT * FROM STUDENT LIMIT 10;

-- Distinct values
SELECT DISTINCT City FROM STUDENT;
```

**DML Commands: 8-10 marks (Very Important)**

---

## Set Operations

**Requirement:** Tables must be **union compatible** (same number and type of columns)

### **UNION**
**Purpose:** Combine results from multiple queries, **removing duplicates**

```sql
SELECT Name FROM STUDENT WHERE Course = 'CS'
UNION
SELECT Name FROM STUDENT WHERE Course = 'IT';

-- Keep duplicates
SELECT Name FROM STUDENT WHERE City = 'Delhi'
UNION ALL
SELECT Name FROM STUDENT WHERE Age > 20;
```

### **INTERSECT**
**Purpose:** Return **common records** from both queries

```sql
SELECT Student_ID FROM STUDENT WHERE Course = 'CS'
INTERSECT
SELECT Student_ID FROM STUDENT WHERE City = 'Mumbai';
```

### **EXCEPT/MINUS**
**Purpose:** Return records from **first query but not in second**

```sql
SELECT Student_ID FROM STUDENT
EXCEPT
SELECT Student_ID FROM PASS_STUDENTS;
```

**Set Operations: 5-6 marks**

---

## Aggregate Functions

### **Basic Aggregate Functions:**

| **Function** | **Purpose** | **Example** |
|---|---|---|
| **COUNT()** | Count records | COUNT(*), COUNT(DISTINCT City) |
| **SUM()** | Sum of values | SUM(Marks), SUM(Salary) |
| **AVG()** | Average value | AVG(Age), AVG(Price) |
| **MAX()** | Maximum value | MAX(Salary), MAX(Date) |
| **MIN()** | Minimum value | MIN(Age), MIN(Price) |

### **Practical Examples:**
```sql
-- Basic statistics
SELECT 
    COUNT(*) AS Total_Students,
    COUNT(DISTINCT Course_ID) AS Unique_Courses,
    AVG(Age) AS Average_Age,
    MAX(Marks) AS Highest_Marks,
    MIN(Marks) AS Lowest_Marks,
    SUM(Fees) AS Total_Revenue
FROM STUDENT;

-- String aggregation
SELECT GROUP_CONCAT(Name SEPARATOR ', ') AS Student_List
FROM STUDENT WHERE Course_ID = 1;
```

### **Aggregate with GROUP BY:**
```sql
-- Students per course
SELECT Course_ID, COUNT(*) AS Student_Count
FROM STUDENT
GROUP BY Course_ID;

-- Statistics by city
SELECT 
    City,
    COUNT(*) AS Student_Count,
    AVG(Age) AS Average_Age,
    MAX(Marks) AS Highest_Marks
FROM STUDENT
GROUP BY City
HAVING COUNT(*) > 5;
```

**Aggregate Functions: 7-8 marks**

---

## NULL Values

### **NULL Characteristics:**
- **Represents unknown or missing data**
- **Not equal to zero or empty string**
- **Any arithmetic operation with NULL results in NULL**
- **Special operators required for comparison**

### **NULL Handling:**
```sql
-- Check for NULL values
SELECT * FROM STUDENT WHERE Phone IS NULL;
SELECT * FROM STUDENT WHERE Email IS NOT NULL;

-- Handle NULL in output
SELECT 
    Name, 
    COALESCE(Phone, 'No Phone') AS Contact,
    ISNULL(Email, 'No Email') AS Email_Status
FROM STUDENT;

-- NULL in calculations
SELECT 
    Name,
    Marks,
    Bonus,
    (Marks + Bonus) AS Total,  -- NULL if Bonus is NULL
    (Marks + COALESCE(Bonus, 0)) AS Safe_Total  -- Handle NULL
FROM STUDENT;
```

### **NULL in Aggregate Functions:**
```sql
-- COUNT(*) includes NULLs, COUNT(column) excludes NULLs
SELECT 
    COUNT(*) AS Total_Records,
    COUNT(Phone) AS Records_With_Phone,
    COUNT(DISTINCT City) AS Unique_Cities
FROM STUDENT;
```

**NULL Handling: 5-6 marks**

---

## Complex Retrieval Queries using GROUP BY

### **GROUP BY Fundamentals:**
```sql
-- Basic grouping
SELECT Course_ID, COUNT(*) AS Student_Count
FROM STUDENT
GROUP BY Course_ID;

-- Multiple column grouping
SELECT Course_ID, City, COUNT(*) AS Count
FROM STUDENT
GROUP BY Course_ID, City
ORDER BY Course_ID, Count DESC;
```

### **HAVING Clause:**
**Purpose:** Filter groups after aggregation (WHERE filters before aggregation)

```sql
-- Courses with more than 10 students
SELECT Course_ID, COUNT(*) AS Student_Count
FROM STUDENT
GROUP BY Course_ID
HAVING COUNT(*) > 10;

-- Combined WHERE and HAVING
SELECT Course_ID, AVG(Age) AS Average_Age
FROM STUDENT
WHERE Age > 18  -- Filter individual records first
GROUP BY Course_ID
HAVING AVG(Age) < 25;  -- Filter groups after grouping
```

### **Advanced GROUP BY Examples:**
```sql
-- Students per course per semester
SELECT 
    Course_ID,
    Semester,
    COUNT(*) AS Student_Count,
    AVG(Marks) AS Average_Marks,
    MAX(Marks) AS Highest_Marks
FROM ENROLLMENT
GROUP BY Course_ID, Semester
HAVING AVG(Marks) > 75
ORDER BY Course_ID, Semester;

-- Monthly enrollment statistics
SELECT 
    YEAR(Enrollment_Date) AS Year,
    MONTH(Enrollment_Date) AS Month,
    COUNT(*) AS New_Enrollments,
    SUM(Fees) AS Total_Fees
FROM STUDENT
GROUP BY YEAR(Enrollment_Date), MONTH(Enrollment_Date)
ORDER BY Year, Month;
```

**GROUP BY Queries: 8-9 marks (Important)**

---

## Nested Queries (Subqueries)

### **Single-Value Subqueries:**
```sql
-- Students older than average age
SELECT Name, Age FROM STUDENT
WHERE Age > (SELECT AVG(Age) FROM STUDENT);

-- Most expensive product
SELECT * FROM PRODUCT
WHERE Price = (SELECT MAX(Price) FROM PRODUCT);

-- Students in Computer Science
SELECT Name FROM STUDENT
WHERE Course_ID = (
    SELECT Course_ID FROM COURSE 
    WHERE Course_Name = 'Computer Science'
);
```

### **Multiple-Value Subqueries:**
```sql
-- Students in CS or IT courses
SELECT Name FROM STUDENT
WHERE Course_ID IN (
    SELECT Course_ID FROM COURSE 
    WHERE Course_Name IN ('Computer Science', 'Information Technology')
);

-- Students with above-average marks in any course
SELECT Name FROM STUDENT
WHERE Marks > ANY (
    SELECT AVG(Marks) FROM STUDENT GROUP BY Course_ID
);

-- Students with highest marks across all courses
SELECT Name FROM STUDENT
WHERE Marks >= ALL (
    SELECT AVG(Marks) FROM STUDENT GROUP BY Course_ID
);
```

### **Correlated Subqueries:**
```sql
-- Students with highest marks in their course
SELECT s1.Name, s1.Course_ID, s1.Marks
FROM STUDENT s1
WHERE s1.Marks = (
    SELECT MAX(s2.Marks) 
    FROM STUDENT s2 
    WHERE s2.Course_ID = s1.Course_ID
);

-- Departments with more students than average
SELECT Dept_Name FROM DEPARTMENT d
WHERE (
    SELECT COUNT(*) FROM STUDENT s 
    WHERE s.Dept_ID = d.Dept_ID
) > (
    SELECT AVG(student_count) FROM (
        SELECT COUNT(*) as student_count 
        FROM STUDENT GROUP BY Dept_ID
    ) AS avg_table
);
```

### **EXISTS Subqueries:**
```sql
-- Students who have enrolled in courses
SELECT Name FROM STUDENT s
WHERE EXISTS (
    SELECT 1 FROM ENROLLMENT e 
    WHERE e.Student_ID = s.Student_ID
);

-- Courses with no enrollments
SELECT Course_Name FROM COURSE c
WHERE NOT EXISTS (
    SELECT 1 FROM ENROLLMENT e 
    WHERE e.Course_ID = c.Course_ID
);
```

**Nested Queries: 9-10 marks (Very Important)**

---

## Integrity Constraints in SQL

### **Column-Level Constraints:**
```sql
CREATE TABLE STUDENT (
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Age INT CHECK (Age > 0 AND Age < 100),
    Email VARCHAR(100) UNIQUE,
    GPA DECIMAL(3,2) CHECK (GPA >= 0.0 AND GPA <= 4.0),
    Gender CHAR(1) CHECK (Gender IN ('M', 'F', 'O')),
    Enrollment_Date DATE DEFAULT CURRENT_DATE
);
```

### **Table-Level Constraints:**
```sql
CREATE TABLE ENROLLMENT (
    Student_ID INT,
    Course_ID INT,
    Grade CHAR(2),
    Semester VARCHAR(10),
    
    PRIMARY KEY (Student_ID, Course_ID, Semester),
    FOREIGN KEY (Student_ID) REFERENCES STUDENT(Student_ID) ON DELETE CASCADE,
    FOREIGN KEY (Course_ID) REFERENCES COURSE(Course_ID) ON UPDATE CASCADE,
    CHECK (Grade IN ('A+', 'A', 'B+', 'B', 'C+', 'C', 'D', 'F'))
);
```

### **Referential Integrity Actions:**
```sql
-- CASCADE: Automatically update/delete related records
FOREIGN KEY (Course_ID) REFERENCES COURSE(Course_ID) ON DELETE CASCADE

-- SET NULL: Set foreign key to NULL when referenced record is deleted
FOREIGN KEY (Manager_ID) REFERENCES EMPLOYEE(Employee_ID) ON DELETE SET NULL

-- RESTRICT: Prevent deletion if referenced records exist
FOREIGN KEY (Dept_ID) REFERENCES DEPARTMENT(Dept_ID) ON DELETE RESTRICT
```

**Integrity Constraints: 6-7 marks**

---

## Database Programming with JDBC

### **JDBC Basics:**
**JDBC (Java Database Connectivity):** API for connecting Java applications to databases.

### **Basic JDBC Steps:**
```java
// 1. Load driver (automatic in newer versions)
Class.forName("com.mysql.cj.jdbc.Driver");

// 2. Establish connection
String url = "jdbc:mysql://localhost:3306/school_db";
String username = "root";
String password = "password";
Connection conn = DriverManager.getConnection(url, username, password);

// 3. Create statement
Statement stmt = conn.createStatement();

// 4. Execute query
String sql = "SELECT * FROM STUDENT WHERE Age > 18";
ResultSet rs = stmt.executeQuery(sql);

// 5. Process results
while(rs.next()) {
    int id = rs.getInt("Student_ID");
    String name = rs.getString("Name");
    int age = rs.getInt("Age");
    System.out.println(id + " - " + name + " (" + age + ")");
}

// 6. Close connections
rs.close();
stmt.close();
conn.close();
```

### **PreparedStatement (Recommended):**
```java
String sql = "SELECT * FROM STUDENT WHERE Age > ? AND City = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setInt(1, 18);
pstmt.setString(2, "Mumbai");
ResultSet rs = pstmt.executeQuery();

// For updates
String updateSQL = "UPDATE STUDENT SET Age = ? WHERE Student_ID = ?";
PreparedStatement updateStmt = conn.prepareStatement(updateSQL);
updateStmt.setInt(1, 21);
updateStmt.setInt(2, 101);
int rowsUpdated = updateStmt.executeUpdate();
```

**JDBC: 5-6 marks**

---

## Security and Authorization

### **User Management:**
```sql
-- Create user
CREATE USER 'teacher'@'localhost' IDENTIFIED BY 'password123';
CREATE USER 'student'@'%' IDENTIFIED BY 'student_pass';

-- Drop user
DROP USER 'old_user'@'localhost';
```

### **GRANT Statement:**
```sql
-- Table-level permissions
GRANT SELECT, INSERT ON STUDENT TO 'teacher'@'localhost';
GRANT ALL PRIVILEGES ON COURSE TO 'admin'@'localhost';

-- Column-level permissions
GRANT SELECT (Name, Age) ON STUDENT TO 'readonly_user'@'localhost';

-- Database-level permissions
GRANT CREATE, DROP ON school_db.* TO 'db_manager'@'localhost';

-- Global permissions
GRANT ALL PRIVILEGES ON *.* TO 'super_admin'@'localhost' WITH GRANT OPTION;
```

### **REVOKE Statement:**
```sql
-- Remove specific permissions
REVOKE INSERT, UPDATE ON STUDENT FROM 'teacher'@'localhost';

-- Remove all permissions
REVOKE ALL PRIVILEGES ON COURSE FROM 'admin'@'localhost';
```

### **Roles (Advanced):**
```sql
-- Create role
CREATE ROLE 'student_role';
GRANT SELECT ON STUDENT TO 'student_role';
GRANT 'student_role' TO 'john'@'localhost';
```

**Security: 6-7 marks**

---

## Indexing

### **Index Concepts:**
**Index:** A **data structure** that improves query performance by providing **fast access paths** to table data.

**Analogy:** Like a book's index - helps find information quickly without reading entire book.

### **Types of Indexes:**

#### **1. Primary Index**
- **Automatically created** with PRIMARY KEY
- **Unique and ordered** on primary key column
- **One per table**

```sql
CREATE TABLE STUDENT (
    Student_ID INT PRIMARY KEY,  -- Primary index automatically created
    Name VARCHAR(50)
);
```

#### **2. Secondary Index**
- **Manually created** on non-primary key columns
- **Can be unique or non-unique**
- **Multiple indexes allowed**

```sql
-- Simple index
CREATE INDEX idx_student_name ON STUDENT(Name);

-- Composite index
CREATE INDEX idx_student_course_city ON STUDENT(Course_ID, City);

-- Unique index
CREATE UNIQUE INDEX idx_student_email ON STUDENT(Email);
```

#### **3. Ordered Indices**
- **B-tree indexes** (most common)
- **Maintain sorted order** of indexed columns
- **Efficient for range queries**

```sql
-- Good for range queries
CREATE INDEX idx_student_age ON STUDENT(Age);

-- Query benefits from index
SELECT * FROM STUDENT WHERE Age BETWEEN 18 AND 25;
SELECT * FROM STUDENT WHERE Age > 20 ORDER BY Age;
```

### **Index Definition in SQL:**
```sql
-- Create index
CREATE INDEX index_name ON table_name(column1, column2, ...);

-- Create unique index
CREATE UNIQUE INDEX index_name ON table_name(column);

-- Create index with specific order
CREATE INDEX idx_student_grade_desc ON STUDENT(Grade DESC);

-- Drop index
DROP INDEX index_name ON table_name;

-- Show indexes
SHOW INDEX FROM table_name;
```

### **When to Use Indexes:**

#### **Good Candidates:**
- **Frequently searched columns** (WHERE clauses)
- **JOIN columns** (foreign keys)
- **ORDER BY columns**
- **Large tables** (> 1000 records)

#### **Avoid Indexing:**
- **Small tables** (< 100 records)
- **Frequently updated columns**
- **Columns with few unique values**
- **Wide columns** (large text fields)

### **Index Performance Impact:**

#### **Benefits:**
- **Faster SELECT** queries
- **Efficient sorting** (ORDER BY)
- **Quick joins** on indexed columns
- **Faster GROUP BY** operations

#### **Costs:**
- **Additional storage** space (10-15% of table size)
- **Slower INSERT/UPDATE/DELETE** (index maintenance)
- **Memory usage** for index buffers

**Indexing: 8-9 marks (Important)**

---

## Important Points for Exams

### **Must Remember:**
1. **SQL command categories** - DDL, DML, DCL, TCL
2. **Constraint types** and their syntax
3. **Aggregate functions** with GROUP BY and HAVING
4. **NULL value handling** with special operators
5. **Subquery types** - correlated, EXISTS, IN, ANY, ALL
6. **Join types** and their differences
7. **Index benefits and costs**
8. **GRANT/REVOKE** syntax for security

### **Common Exam Questions:**
- **Write SQL queries** for given scenarios **(10-12 marks)**
- **Design indexes** for query optimization **(6-7 marks)**
- **Explain aggregate functions** with examples **(5-6 marks)**
- **Compare different join types** **(5-6 marks)**
- **Handle NULL values** in calculations **(4-5 marks)**

### **Key SQL Keywords:**
- **SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY**
- **INSERT, UPDATE, DELETE**
- **CREATE, ALTER, DROP**
- **GRANT, REVOKE**
- **INDEX, PRIMARY KEY, FOREIGN KEY**

**Total Module Weightage: 22-25 marks**
