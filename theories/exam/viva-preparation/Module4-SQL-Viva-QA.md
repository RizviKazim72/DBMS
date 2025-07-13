# Module 4 - SQL & Indexing Viva Preparation 🎤

## Basic SQL Questions (Foundation) 🌟

**Q1: What is SQL? What does it stand for?**
**A:** SQL stands for Structured Query Language. It's a standardized programming language for managing and manipulating relational databases.

**Q2: What are the different categories of SQL commands?**
**A:** Four main categories:
- **DDL (Data Definition Language):** CREATE, ALTER, DROP
- **DML (Data Manipulation Language):** SELECT, INSERT, UPDATE, DELETE
- **DCL (Data Control Language):** GRANT, REVOKE
- **TCL (Transaction Control Language):** COMMIT, ROLLBACK, SAVEPOINT

**Q3: Difference between DDL and DML?**
**A:**
- **DDL:** Defines database structure (tables, indexes, constraints)
- **DML:** Manipulates data within existing structures

**Q4: Is SQL case sensitive?**
**A:** SQL keywords are **not case sensitive** (SELECT = select), but data values **can be case sensitive** depending on database configuration.

---

## Data Definition Language (DDL) 🏗️

**Q5: How do you create a table with constraints?**
**A:**
```sql
CREATE TABLE STUDENT (
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Age INT CHECK (Age > 0),
    Email VARCHAR(100) UNIQUE,
    Course_ID INT,
    FOREIGN KEY (Course_ID) REFERENCES COURSE(Course_ID)
);
```

**Q6: What's the difference between DROP and DELETE?**
**A:**
- **DROP:** Removes entire table structure and data permanently
- **DELETE:** Removes only data, table structure remains

**Q7: How do you add a column to existing table?**
**A:**
```sql
ALTER TABLE STUDENT ADD Phone VARCHAR(15);
```

**Q8: What are the common data types in SQL?**
**A:**
- **Numeric:** INT, DECIMAL, FLOAT
- **String:** VARCHAR, CHAR, TEXT
- **Date/Time:** DATE, TIME, TIMESTAMP
- **Boolean:** BOOLEAN

---

## Data Manipulation Language (DML) 💾

**Q9: Write INSERT statement with multiple records.**
**A:**
```sql
INSERT INTO STUDENT (Student_ID, Name, Age) VALUES
(101, 'John', 20),
(102, 'Mary', 19),
(103, 'David', 21);
```

**Q10: How do you update multiple columns?**
**A:**
```sql
UPDATE STUDENT 
SET Age = 21, Email = 'newemail@domain.com' 
WHERE Student_ID = 101;
```

**Q11: Difference between WHERE and HAVING?**
**A:**
- **WHERE:** Filters individual records **before** grouping
- **HAVING:** Filters groups **after** GROUP BY operation

**Q12: What is the order of execution in SELECT statement?**
**A:** FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT

---

## Aggregate Functions & GROUP BY 🧮

**Q13: What are aggregate functions? Give examples.**
**A:** Functions that perform calculations on multiple rows:
- **COUNT():** Count records
- **SUM():** Sum of values
- **AVG():** Average value
- **MAX():** Maximum value
- **MIN():** Minimum value

**Q14: Difference between COUNT(*) and COUNT(column)?**
**A:**
- **COUNT(*):** Counts all rows including NULLs
- **COUNT(column):** Counts non-NULL values in specified column

**Q15: Write query to find average age by course.**
**A:**
```sql
SELECT Course_ID, AVG(Age) AS Average_Age
FROM STUDENT
GROUP BY Course_ID
HAVING AVG(Age) > 20;
```

**Q16: Can you use aggregate functions in WHERE clause?**
**A:** No, use HAVING clause instead. Aggregate functions work on groups, which are formed after WHERE filtering.

---

## NULL Value Handling ❓

**Q17: What is NULL in SQL?**
**A:** NULL represents **unknown or missing data**. It's not zero, empty string, or space - it's the absence of any value.

**Q18: How do you check for NULL values?**
**A:**
```sql
-- Correct way
SELECT * FROM STUDENT WHERE Phone IS NULL;
SELECT * FROM STUDENT WHERE Email IS NOT NULL;

-- Wrong way (doesn't work)
SELECT * FROM STUDENT WHERE Phone = NULL;
```

**Q19: What happens when you perform arithmetic with NULL?**
**A:** Any arithmetic operation with NULL results in NULL.
**Example:** 10 + NULL = NULL, 20 * NULL = NULL

**Q20: How do you handle NULL in output?**
**A:**
```sql
SELECT Name, COALESCE(Phone, 'No Phone') AS Contact
FROM STUDENT;
```

---

## Join Operations 🔗

**Q21: Explain different types of JOINs.**
**A:**
- **INNER JOIN:** Returns only matching records from both tables
- **LEFT JOIN:** Returns all records from left table + matching from right
- **RIGHT JOIN:** Returns all records from right table + matching from left  
- **FULL OUTER JOIN:** Returns all records from both tables

**Q22: Write INNER JOIN query for students and courses.**
**A:**
```sql
SELECT s.Name, c.Course_Name
FROM STUDENT s
INNER JOIN COURSE c ON s.Course_ID = c.Course_ID;
```

**Q23: When would you use LEFT JOIN?**
**A:** When you want **all records from left table** even if there's no matching record in right table.
**Example:** All students even if they haven't enrolled in any course.

**Q24: Difference between INNER JOIN and WHERE with multiple tables?**
**A:** Both can produce same results, but JOIN is more readable and follows SQL standards. WHERE with multiple tables creates Cartesian product internally.

---

## Subqueries (Nested Queries) 🔄

**Q25: What is a subquery? Give example.**
**A:** A query inside another query.
```sql
-- Students older than average age
SELECT Name FROM STUDENT
WHERE Age > (SELECT AVG(Age) FROM STUDENT);
```

**Q26: Difference between correlated and non-correlated subquery?**
**A:**
- **Non-correlated:** Inner query executes once, independent of outer query
- **Correlated:** Inner query executes for each row of outer query, depends on outer query values

**Q27: Write correlated subquery example.**
**A:**
```sql
-- Students with highest marks in their course
SELECT s1.Name, s1.Marks
FROM STUDENT s1
WHERE s1.Marks = (
    SELECT MAX(s2.Marks) 
    FROM STUDENT s2 
    WHERE s2.Course_ID = s1.Course_ID
);
```

**Q28: When do you use EXISTS vs IN?**
**A:**
- **EXISTS:** Better for large datasets, returns TRUE/FALSE
- **IN:** Better for small lists, checks membership

---

## Set Operations 📚

**Q29: What are set operations in SQL?**
**A:** Operations to combine results from multiple queries:
- **UNION:** Combines results, removes duplicates
- **UNION ALL:** Combines results, keeps duplicates
- **INTERSECT:** Returns common records
- **EXCEPT/MINUS:** Returns records in first but not in second

**Q30: What is union compatibility?**
**A:** Tables must have **same number of columns** with **compatible data types** to use set operations.

**Q31: Difference between UNION and UNION ALL?**
**A:**
- **UNION:** Removes duplicate records (slower)
- **UNION ALL:** Keeps all records including duplicates (faster)

---

## Constraints & Integrity 🔒

**Q32: What are integrity constraints?**
**A:** Rules that ensure data accuracy and consistency:
- **Primary Key:** Unique identifier, cannot be NULL
- **Foreign Key:** References primary key in another table
- **Unique:** No duplicate values allowed
- **Check:** Custom validation rules
- **Not NULL:** Cannot be empty

**Q33: What is referential integrity?**
**A:** Ensures foreign key values must reference existing primary key values. Maintains consistency between related tables.

**Q34: What are CASCADE operations?**
**A:**
- **ON DELETE CASCADE:** Automatically delete related records
- **ON UPDATE CASCADE:** Automatically update foreign key values
- **ON DELETE SET NULL:** Set foreign key to NULL when referenced record deleted

**Q35: Difference between PRIMARY KEY and UNIQUE constraint?**
**A:**
- **Primary Key:** Only one per table, cannot be NULL, creates clustered index
- **Unique:** Multiple allowed per table, can be NULL (once), creates non-clustered index

---

## Indexing 📈

**Q36: What is an index? Why is it important?**
**A:** Index is a data structure that improves query performance by providing fast access paths to table data. Like a book's index - helps find information quickly.

**Q37: Types of indexes?**
**A:**
- **Primary Index:** Automatically created with primary key
- **Secondary Index:** Manually created on other columns
- **Unique Index:** Ensures uniqueness
- **Composite Index:** Multiple columns combined

**Q38: When should you create an index?**
**A:** Create index when:
- Column frequently used in WHERE clauses
- Column used in JOIN operations
- Column used in ORDER BY
- Large tables (>1000 records)

**Q39: What are the disadvantages of indexes?**
**A:**
- **Additional storage space** required
- **Slower INSERT/UPDATE/DELETE** operations
- **Index maintenance** overhead

**Q40: How do you create an index?**
**A:**
```sql
-- Simple index
CREATE INDEX idx_student_name ON STUDENT(Name);

-- Composite index
CREATE INDEX idx_student_course ON STUDENT(Course_ID, City);

-- Unique index
CREATE UNIQUE INDEX idx_student_email ON STUDENT(Email);
```

---

## Security & Authorization 🔐

**Q41: How do you manage user permissions in SQL?**
**A:** Using GRANT and REVOKE commands:
```sql
-- Grant permissions
GRANT SELECT, INSERT ON STUDENT TO 'teacher'@'localhost';

-- Revoke permissions
REVOKE UPDATE, DELETE ON STUDENT FROM 'teacher'@'localhost';
```

**Q42: What is the principle of least privilege?**
**A:** Give users only the **minimum permissions** necessary to perform their job functions. Reduces security risks.

**Q43: Difference between authentication and authorization?**
**A:**
- **Authentication:** Verifying user identity (login)
- **Authorization:** Determining what authenticated user can access (permissions)

---

## JDBC & Database Programming ☕

**Q44: What is JDBC?**
**A:** Java Database Connectivity - API that allows Java applications to interact with databases.

**Q45: What are the steps to connect to database using JDBC?**
**A:**
1. Load database driver
2. Establish connection
3. Create statement
4. Execute query
5. Process results  
6. Close connections

**Q46: Difference between Statement and PreparedStatement?**
**A:**
- **Statement:** SQL compiled each time, vulnerable to SQL injection
- **PreparedStatement:** Pre-compiled, secure, better performance for repeated queries

**Q47: How do you prevent SQL injection?**
**A:** Use **PreparedStatement** with parameter placeholders (?):
```java
String sql = "SELECT * FROM STUDENT WHERE Age > ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 18);
```

---

## Performance & Optimization ⚡

**Q48: How do you optimize SQL queries?**
**A:**
- **Use indexes** on frequently searched columns
- **Avoid SELECT *** - specify needed columns
- **Use LIMIT** for large result sets
- **Apply WHERE conditions** before GROUP BY
- **Use EXISTS** instead of IN for large subqueries

**Q49: What factors affect query performance?**
**A:**
- **Table size** (number of records)
- **Index availability** on searched columns
- **Query complexity** (joins, subqueries)
- **Data distribution** (how evenly data is spread)
- **Database server resources** (CPU, memory)

**Q50: How do you find slow queries?**
**A:**
- Use **EXPLAIN** to analyze query execution plan
- Check **query execution time**
- Monitor **database logs**
- Use **database profiling tools**

---

## Advanced Topics 🚀

**Q51: What is a recursive query?**
**A:** Query that references itself, typically used for hierarchical data:
```sql
WITH RECURSIVE employee_hierarchy AS (
    SELECT employee_id, name, manager_id, 1 as level
    FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.employee_id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM employee_hierarchy;
```

**Q52: What are window functions?**
**A:** Functions that perform calculations across related rows:
```sql
SELECT Name, Marks, 
       ROW_NUMBER() OVER (ORDER BY Marks DESC) as rank
FROM STUDENT;
```

**Q53: What is a stored procedure?**
**A:** Pre-compiled SQL code stored in database that can be executed multiple times with different parameters.

---

## Practical Scenarios 🎯

**Q54: Design tables for a library management system.**
**A:**
```sql
CREATE TABLE BOOK (
    Book_ID INT PRIMARY KEY,
    Title VARCHAR(200),
    Author VARCHAR(100),
    ISBN VARCHAR(20) UNIQUE
);

CREATE TABLE MEMBER (
    Member_ID INT PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100) UNIQUE,
    Join_Date DATE
);

CREATE TABLE BORROW (
    Member_ID INT,
    Book_ID INT,
    Issue_Date DATE,
    Return_Date DATE,
    PRIMARY KEY (Member_ID, Book_ID, Issue_Date),
    FOREIGN KEY (Member_ID) REFERENCES MEMBER(Member_ID),
    FOREIGN KEY (Book_ID) REFERENCES BOOK(Book_ID)
);
```

**Q55: Find books that are currently borrowed.**
**A:**
```sql
SELECT b.Title, m.Name, br.Issue_Date
FROM BOOK b
JOIN BORROW br ON b.Book_ID = br.Book_ID  
JOIN MEMBER m ON br.Member_ID = m.Member_ID
WHERE br.Return_Date IS NULL;
```

---

## Pro Tips for Viva Success 💡

### **Preparation Strategy:**
1. **Practice writing queries** on paper without IDE
2. **Understand query execution** order and logic
3. **Memorize common functions** and their syntax
4. **Prepare real-world examples** for each concept

### **During Viva:**
1. **Think aloud** while writing queries
2. **Explain your approach** step by step
3. **Handle edge cases** (NULL values, empty results)
4. **Optimize queries** and explain reasoning

### **Common Mistakes to Avoid:**
- Don't confuse WHERE with HAVING
- Don't forget to handle NULL values
- Don't use aggregate functions in WHERE clause
- Don't ignore referential integrity constraints

### **Golden Rules:**
- **Test your logic** before writing complex queries
- **Start simple** then add complexity
- **Use meaningful aliases** for readability
- **Always consider performance** implications

**Practice Datasets:** Library, E-commerce, University, Hospital, Banking

**Remember:** **Clear logic** + **Correct syntax** + **Performance awareness** = **Excellent Viva!** 🌟
