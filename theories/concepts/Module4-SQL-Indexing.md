# Module 4: SQL & Indexing - Database Ki Asli Power! 💪

## SQL Kya Hai Bhai? 🤔

**Simple words mein:** SQL (Structured Query Language) database se **baat karne ka tareeka** hai! Think of it as **database ka WhatsApp** - jo chahiye wo message kar do!

**Real example:** Tu database se puch sakta hai "Mujhe saare Delhi ke students de do" - aur SQL ye kaam kar dega!

---

## SQL Overview 📊

### **SQL Ki Superpowers:**
- **Data retrieve** karna (SELECT)
- **Data insert/update/delete** karna (DML)
- **Table structure** banane/modify karne (DDL)
- **Access control** karna (DCL)
- **Complex queries** fire karna

### **SQL Categories:**
```
DDL (Data Definition) → CREATE, ALTER, DROP
DML (Data Manipulation) → INSERT, UPDATE, DELETE, SELECT  
DCL (Data Control) → GRANT, REVOKE
TCL (Transaction Control) → COMMIT, ROLLBACK
```

---

## Data Definition Commands (DDL) 🏗️

### **CREATE TABLE - Table Banao**
```sql
CREATE TABLE Student (
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Age INT CHECK (Age > 0),
    Email VARCHAR(100) UNIQUE,
    Course_ID INT,
    FOREIGN KEY (Course_ID) REFERENCES Course(Course_ID)
);
```

### **ALTER TABLE - Table Modify Karo**
```sql
-- Column add karo
ALTER TABLE Student ADD Phone VARCHAR(15);

-- Column modify karo  
ALTER TABLE Student MODIFY Name VARCHAR(100);

-- Column drop karo
ALTER TABLE Student DROP COLUMN Phone;
```

### **DROP TABLE - Table Delete Karo**
```sql
DROP TABLE Student;  -- Pura table gone!
```

### **Real Instagram Example:**
```sql
CREATE TABLE User (
    User_ID INT PRIMARY KEY,
    Username VARCHAR(30) UNIQUE NOT NULL,
    Email VARCHAR(100) UNIQUE,
    Profile_Picture VARCHAR(255),
    Followers_Count INT DEFAULT 0,
    Created_Date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE Post (
    Post_ID INT PRIMARY KEY,
    User_ID INT,
    Caption TEXT,
    Image_URL VARCHAR(255),
    Likes_Count INT DEFAULT 0,
    Posted_Date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (User_ID) REFERENCES User(User_ID)
);
```

---

## Data Manipulation Commands (DML) 🔄

### **INSERT - Data Dalo**
```sql
-- Single record
INSERT INTO Student VALUES (101, 'Rahul', 20, 'rahul@gmail.com', 1);

-- Multiple records
INSERT INTO Student (Student_ID, Name, Age) VALUES 
(102, 'Priya', 19),
(103, 'Amit', 21);

-- Select se insert
INSERT INTO Pass_Students 
SELECT * FROM Student WHERE Grade = 'A';
```

### **UPDATE - Data Modify Karo**
```sql
-- Single record update
UPDATE Student 
SET Age = 21 
WHERE Student_ID = 101;

-- Multiple columns update
UPDATE Student 
SET Age = Age + 1, Course_ID = 2 
WHERE City = 'Delhi';

-- Conditional update
UPDATE Student 
SET Grade = 'A+' 
WHERE Marks > 90;
```

### **DELETE - Data Hatao**
```sql
-- Specific records delete
DELETE FROM Student 
WHERE Age < 18;

-- All records delete (table structure remains)
DELETE FROM Student;

-- Conditional delete with join
DELETE s FROM Student s
JOIN Course c ON s.Course_ID = c.Course_ID
WHERE c.Course_Name = 'Discontinued';
```

### **SELECT - Data Nikalo (Main Power!)**
```sql
-- Basic selection
SELECT Name, Age FROM Student;

-- All columns
SELECT * FROM Student;

-- With conditions
SELECT * FROM Student 
WHERE Age > 20 AND City = 'Mumbai';

-- Sorted results
SELECT * FROM Student 
ORDER BY Age DESC, Name ASC;

-- Limited results
SELECT * FROM Student 
LIMIT 10;
```

---

## Set Operations - Collections Ki Power! 🔗

### **UNION - Combine Karo**
```sql
-- All CS and IT students
SELECT Name FROM Student WHERE Course = 'CS'
UNION
SELECT Name FROM Student WHERE Course = 'IT';

-- UNION ALL (duplicates allowed)
SELECT Name FROM Student WHERE City = 'Delhi'
UNION ALL
SELECT Name FROM Student WHERE Age > 20;
```

### **INTERSECT - Common Nikalo**
```sql
-- Students who are both CS and from Delhi
SELECT Student_ID FROM Student WHERE Course = 'CS'
INTERSECT
SELECT Student_ID FROM Student WHERE City = 'Delhi';
```

### **EXCEPT/MINUS - Difference Nikalo**
```sql
-- All students except those who passed
SELECT Student_ID FROM Student
EXCEPT
SELECT Student_ID FROM Pass_Students;
```

---

## Aggregate Functions - Math Ki Power! 🧮

### **Basic Aggregate Functions:**
```sql
-- Count records
SELECT COUNT(*) AS Total_Students FROM Student;
SELECT COUNT(DISTINCT City) AS Unique_Cities FROM Student;

-- Mathematical operations
SELECT 
    AVG(Age) AS Average_Age,
    MAX(Marks) AS Highest_Marks,
    MIN(Marks) AS Lowest_Marks,
    SUM(Fees) AS Total_Fees
FROM Student;

-- String aggregation
SELECT GROUP_CONCAT(Name) AS All_Names FROM Student;
```

### **Real E-commerce Example:**
```sql
-- Product statistics
SELECT 
    Category,
    COUNT(*) AS Product_Count,
    AVG(Price) AS Average_Price,
    MAX(Price) AS Most_Expensive,
    MIN(Price) AS Cheapest,
    SUM(Stock_Quantity) AS Total_Stock
FROM Product
GROUP BY Category
HAVING COUNT(*) > 5;
```

---

## NULL Values - Missing Data Handle Karo! ❓

### **NULL Kya Hai?**
- **Unknown/Missing** value
- **Not zero** or empty string
- **Special treatment** required

### **NULL Operations:**
```sql
-- NULL check karo
SELECT * FROM Student WHERE Phone IS NULL;
SELECT * FROM Student WHERE Phone IS NOT NULL;

-- NULL handle karo
SELECT Name, COALESCE(Phone, 'No Phone') AS Contact FROM Student;
SELECT Name, ISNULL(Email, 'No Email') AS Email_Status FROM Student;

-- NULL in comparisons
SELECT * FROM Student WHERE Age = NULL;  -- Wrong! Returns nothing
SELECT * FROM Student WHERE Age IS NULL; -- Correct!

-- NULL in calculations
SELECT Name, Marks, (Marks + NULL) AS Total FROM Student; -- Result is NULL
```

---

## Complex Queries with GROUP BY 📊

### **GROUP BY - Category-wise Analysis**
```sql
-- Students per course
SELECT Course_ID, COUNT(*) AS Student_Count
FROM Student
GROUP BY Course_ID;

-- Average age by city
SELECT City, AVG(Age) AS Average_Age
FROM Student
GROUP BY City
HAVING AVG(Age) > 20;

-- Multiple grouping
SELECT Course_ID, City, COUNT(*) AS Count
FROM Student
GROUP BY Course_ID, City
ORDER BY Course_ID, Count DESC;
```

### **HAVING vs WHERE:**
```sql
-- WHERE filters before grouping
SELECT Course_ID, COUNT(*) AS Count
FROM Student
WHERE Age > 18  -- Filter individual records first
GROUP BY Course_ID
HAVING COUNT(*) > 10;  -- Filter groups after grouping
```

---

## Nested Queries - Query Inside Query! 🔄

### **Subquery Types:**

#### **Single Value Subquery:**
```sql
-- Students older than average age
SELECT Name FROM Student
WHERE Age > (SELECT AVG(Age) FROM Student);

-- Most expensive product
SELECT * FROM Product
WHERE Price = (SELECT MAX(Price) FROM Product);
```

#### **Multiple Value Subquery:**
```sql
-- Students in CS or IT courses
SELECT Name FROM Student
WHERE Course_ID IN (
    SELECT Course_ID FROM Course 
    WHERE Course_Name IN ('CS', 'IT')
);

-- Students with above average marks
SELECT Name FROM Student
WHERE Marks > ALL (
    SELECT AVG(Marks) FROM Student GROUP BY Course_ID
);
```

#### **Correlated Subquery:**
```sql
-- Students with highest marks in their course
SELECT s1.Name, s1.Course_ID, s1.Marks
FROM Student s1
WHERE s1.Marks = (
    SELECT MAX(s2.Marks) 
    FROM Student s2 
    WHERE s2.Course_ID = s1.Course_ID
);
```

### **EXISTS Subquery:**
```sql
-- Students who have enrolled in courses
SELECT Name FROM Student s
WHERE EXISTS (
    SELECT 1 FROM Enrollment e 
    WHERE e.Student_ID = s.Student_ID
);

-- Courses with no students
SELECT Course_Name FROM Course c
WHERE NOT EXISTS (
    SELECT 1 FROM Student s 
    WHERE s.Course_ID = c.Course_ID
);
```

---

## Joins - Tables Ko Connect Karo! 🔗

### **INNER JOIN - Common Data**
```sql
-- Students with course details
SELECT s.Name, c.Course_Name
FROM Student s
INNER JOIN Course c ON s.Course_ID = c.Course_ID;
```

### **LEFT JOIN - All Left Side Data**
```sql
-- All students (even without courses)
SELECT s.Name, c.Course_Name
FROM Student s
LEFT JOIN Course c ON s.Course_ID = c.Course_ID;
```

### **RIGHT JOIN - All Right Side Data**
```sql
-- All courses (even without students)
SELECT s.Name, c.Course_Name
FROM Student s
RIGHT JOIN Course c ON s.Course_ID = c.Course_ID;
```

### **FULL OUTER JOIN - All Data**
```sql
-- All students and all courses
SELECT s.Name, c.Course_Name
FROM Student s
FULL OUTER JOIN Course c ON s.Course_ID = c.Course_ID;
```

---

## Security & Authorization 🔒

### **GRANT - Permission Do**
```sql
-- Table access permission
GRANT SELECT, INSERT ON Student TO 'teacher_user';

-- All permissions
GRANT ALL PRIVILEGES ON Student TO 'admin_user';

-- Column level permission
GRANT SELECT (Name, Age) ON Student TO 'readonly_user';

-- Database level permission
GRANT CREATE, DROP ON DATABASE school TO 'db_admin';
```

### **REVOKE - Permission Wapas Lo**
```sql
-- Specific permissions revoke
REVOKE INSERT, UPDATE ON Student FROM 'teacher_user';

-- All permissions revoke
REVOKE ALL PRIVILEGES ON Student FROM 'admin_user';
```

---

## Indexing - Speed Ka Raja! ⚡

### **Index Kya Hai?**
**Database ka phonebook!** Jaise phonebook mein naam alphabetical order mein hote hai, waise hi index data ko **sorted order** mein rakhta hai for **faster search**!

### **Index Types:**

#### **Primary Index**
```sql
-- Automatically created with primary key
CREATE TABLE Student (
    Student_ID INT PRIMARY KEY,  -- Primary index auto-created
    Name VARCHAR(50)
);
```

#### **Secondary Index**
```sql
-- Manual index creation
CREATE INDEX idx_student_name ON Student(Name);
CREATE INDEX idx_student_city ON Student(City);

-- Composite index
CREATE INDEX idx_course_student ON Enrollment(Course_ID, Student_ID);

-- Unique index
CREATE UNIQUE INDEX idx_student_email ON Student(Email);
```

### **Index Benefits & Drawbacks:**

#### **Benefits:**
- **Faster SELECT** queries
- **Quick sorting** and searching
- **Efficient JOIN** operations

#### **Drawbacks:**
- **Extra storage** space needed
- **Slower INSERT/UPDATE/DELETE** (index maintenance)
- **Index maintenance** overhead

### **When to Use Index:**
```sql
-- Good for indexing
CREATE INDEX idx_student_age ON Student(Age);  -- Frequently searched
CREATE INDEX idx_order_date ON Orders(Order_Date);  -- Range queries

-- Avoid indexing
-- Small tables (< 1000 records)
-- Columns with frequent updates
-- Columns with few unique values
```

---

## JDBC - Java Se Database Connect! ☕

### **Basic JDBC Example:**
```java
// Database connection
String url = "jdbc:mysql://localhost:3306/school";
String username = "root";
String password = "password";

Connection conn = DriverManager.getConnection(url, username, password);

// Execute query
String sql = "SELECT * FROM Student WHERE Age > ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 18);
ResultSet rs = stmt.executeQuery();

// Process results
while(rs.next()) {
    System.out.println(rs.getString("Name") + " - " + rs.getInt("Age"));
}

// Close connections
rs.close();
stmt.close();
conn.close();
```

---

## Performance Tips 🚀

### **Query Optimization:**
```sql
-- Use indexes on WHERE columns
SELECT * FROM Student WHERE Student_ID = 101;  -- Fast (if indexed)

-- Avoid SELECT *
SELECT Name, Age FROM Student;  -- Better than SELECT *

-- Use LIMIT for large results
SELECT * FROM Student ORDER BY Age LIMIT 100;

-- Use EXISTS instead of IN for large subqueries
SELECT Name FROM Student s
WHERE EXISTS (SELECT 1 FROM Course c WHERE c.Course_ID = s.Course_ID);
```

---

## Real Project Example 🛒

### **E-commerce Database:**
```sql
-- Create tables
CREATE TABLE Customer (
    Customer_ID INT PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100) UNIQUE,
    City VARCHAR(50),
    INDEX idx_customer_city (City)
);

CREATE TABLE Product (
    Product_ID INT PRIMARY KEY,
    Name VARCHAR(100),
    Price DECIMAL(10,2),
    Category VARCHAR(50),
    Stock INT,
    INDEX idx_product_category (Category),
    INDEX idx_product_price (Price)
);

CREATE TABLE Order_Item (
    Order_ID INT,
    Product_ID INT,
    Quantity INT,
    Price DECIMAL(10,2),
    PRIMARY KEY (Order_ID, Product_ID),
    FOREIGN KEY (Product_ID) REFERENCES Product(Product_ID)
);

-- Complex business queries
-- Top 5 customers by total spending
SELECT c.Name, SUM(oi.Quantity * oi.Price) AS Total_Spent
FROM Customer c
JOIN Orders o ON c.Customer_ID = o.Customer_ID
JOIN Order_Item oi ON o.Order_ID = oi.Order_ID
GROUP BY c.Customer_ID, c.Name
ORDER BY Total_Spent DESC
LIMIT 5;

-- Products running low in stock
SELECT Name, Stock FROM Product
WHERE Stock < 10
ORDER BY Stock ASC;
```

---

## Interview Questions 💡

**Q: Difference between WHERE and HAVING?**
**A:** WHERE filters individual records, HAVING filters groups after GROUP BY

**Q: When to use Index?**
**A:** Frequently searched columns, large tables, WHERE/JOIN conditions

**Q: What is NULL in SQL?**
**A:** Unknown/missing value, not zero or empty, needs special handling with IS NULL

**Q: INNER vs LEFT JOIN difference?**
**A:** INNER returns only matching records, LEFT returns all left side + matching right

---

## Key Takeaways ✨

1. **SQL** = database communication language
2. **DDL** creates structure, **DML** manipulates data
3. **Aggregate functions** provide mathematical operations
4. **GROUP BY** enables category-wise analysis  
5. **Nested queries** allow complex data retrieval
6. **Joins** connect related tables
7. **Indexes** speed up queries but cost storage
8. **Security** controls who can access what
9. **JDBC** connects Java applications to database

**Next:** Database normalization aur design optimization! 🎯
