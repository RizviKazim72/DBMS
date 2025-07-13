# Module 4 - SQL & Indexing Quick Revision 🚀

## SQL Categories (DDML-DC-TC) 📊

| **Category** | **Commands** | **Purpose** |
|---|---|---|
| **DDL** | CREATE, ALTER, DROP | Structure Definition |
| **DML** | SELECT, INSERT, UPDATE, DELETE | Data Manipulation |
| **DCL** | GRANT, REVOKE | Access Control |
| **TCL** | COMMIT, ROLLBACK | Transaction Control |

**Memory Trick:** **D-D-D-T** = **DDL-DML-DCL-TCL**

---

## Essential SQL Commands ⚡

### **CREATE TABLE Formula:**
```sql
CREATE TABLE table_name (
    column datatype constraints,
    PRIMARY KEY (column),
    FOREIGN KEY (column) REFERENCES other_table(column)
);
```

### **DML Operations:**
```sql
-- Insert: Add data
INSERT INTO table VALUES (val1, val2, ...);

-- Update: Modify data  
UPDATE table SET col1=val1 WHERE condition;

-- Delete: Remove data
DELETE FROM table WHERE condition;

-- Select: Retrieve data
SELECT columns FROM table WHERE condition;
```

---

## Aggregate Functions Quick Reference 🧮

| **Function** | **Purpose** | **Example** |
|---|---|---|
| `COUNT()` | Count records | `COUNT(*)`, `COUNT(DISTINCT col)` |
| `SUM()` | Sum values | `SUM(salary)` |
| `AVG()` | Average | `AVG(age)` |
| `MAX()` | Maximum | `MAX(price)` |
| `MIN()` | Minimum | `MIN(date)` |

### **GROUP BY Pattern:**
```sql
SELECT column, AGG_FUNCTION(column)
FROM table
WHERE condition          -- Filter BEFORE grouping
GROUP BY column
HAVING AGG_FUNCTION > value  -- Filter AFTER grouping
ORDER BY column;
```

**Key Rule:** **WHERE** → **GROUP BY** → **HAVING** → **ORDER BY**

---

## NULL Handling Cheat Sheet ❓

### **NULL Check Operations:**
```sql
-- Check for NULL
WHERE column IS NULL
WHERE column IS NOT NULL

-- Handle NULL in output
COALESCE(column, 'default_value')
ISNULL(column, 'replacement')

-- NULL in calculations
column + NULL = NULL  -- Any operation with NULL = NULL
```

### **NULL in Aggregates:**
- `COUNT(*)` - includes NULLs
- `COUNT(column)` - excludes NULLs
- `SUM(), AVG(), MAX(), MIN()` - ignore NULLs

---

## Join Types Quick Guide 🔗

| **Join Type** | **Result** | **Use Case** |
|---|---|---|
| `INNER JOIN` | Only matching records | Common data |
| `LEFT JOIN` | All left + matching right | All from left table |
| `RIGHT JOIN` | All right + matching left | All from right table |
| `FULL OUTER JOIN` | All from both tables | Complete data |

### **Join Pattern:**
```sql
SELECT columns
FROM table1 t1
[INNER/LEFT/RIGHT/FULL] JOIN table2 t2 
ON t1.column = t2.column
WHERE conditions;
```

---

## Subquery Patterns 🔄

### **Single Value Subquery:**
```sql
SELECT * FROM table
WHERE column = (SELECT AGG_FUNC(column) FROM table2);
```

### **Multiple Value Subquery:**
```sql
SELECT * FROM table
WHERE column IN (SELECT column FROM table2);
```

### **EXISTS Pattern:**
```sql
SELECT * FROM table1 t1
WHERE EXISTS (
    SELECT 1 FROM table2 t2 
    WHERE t2.fk = t1.pk
);
```

### **Correlated Subquery:**
```sql
SELECT * FROM table1 t1
WHERE t1.column = (
    SELECT MAX(t2.column) 
    FROM table2 t2 
    WHERE t2.fk = t1.pk
);
```

---

## Set Operations Quick Reference 📚

### **Requirements:**
- **Union Compatible** tables (same structure)

### **Operations:**
```sql
query1 UNION query2        -- Remove duplicates
query1 UNION ALL query2    -- Keep duplicates  
query1 INTERSECT query2    -- Common records
query1 EXCEPT query2       -- First minus second
```

---

## Constraints Checklist 🔒

### **Column Constraints:**
```sql
column_name datatype 
    PRIMARY KEY          -- Unique identifier
    NOT NULL            -- Cannot be empty
    UNIQUE              -- No duplicates
    CHECK (condition)   -- Custom validation
    DEFAULT value       -- Default value
```

### **Table Constraints:**
```sql
PRIMARY KEY (col1, col2)                    -- Composite key
FOREIGN KEY (col) REFERENCES table(col)     -- Reference integrity
CHECK (condition)                           -- Table-level validation
```

### **Referential Actions:**
- `ON DELETE CASCADE` - Delete related records
- `ON DELETE SET NULL` - Set FK to NULL
- `ON UPDATE CASCADE` - Update FK values

---

## Indexing Strategy 📈

### **When to Create Index:**
✅ **Frequently searched columns** (WHERE clauses)
✅ **Foreign key columns** (JOIN operations)  
✅ **ORDER BY columns**
✅ **Large tables** (>1000 records)

### **When to Avoid Index:**
❌ **Small tables** (<100 records)
❌ **Frequently updated columns**
❌ **Low cardinality** columns (few unique values)
❌ **Wide text columns**

### **Index Types:**
```sql
-- Primary (automatic)
CREATE TABLE t (id INT PRIMARY KEY);

-- Secondary
CREATE INDEX idx_name ON table(column);

-- Composite  
CREATE INDEX idx_multi ON table(col1, col2);

-- Unique
CREATE UNIQUE INDEX idx_unique ON table(column);
```

---

## Security Commands 🔐

### **User Management:**
```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
DROP USER 'username'@'host';
```

### **Permission Patterns:**
```sql
-- Grant permissions
GRANT SELECT, INSERT ON table TO 'user'@'host';
GRANT ALL PRIVILEGES ON database.* TO 'user'@'host';

-- Revoke permissions
REVOKE INSERT, UPDATE ON table FROM 'user'@'host';
REVOKE ALL PRIVILEGES ON database.* FROM 'user'@'host';
```

---

## JDBC Connection Pattern ☕

### **Basic JDBC Steps:**
```java
// 1. Connection
String url = "jdbc:mysql://localhost:3306/db";
Connection conn = DriverManager.getConnection(url, user, pass);

// 2. Prepared Statement (recommended)
String sql = "SELECT * FROM table WHERE col = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setString(1, value);

// 3. Execute
ResultSet rs = stmt.executeQuery();

// 4. Process results
while(rs.next()) {
    String data = rs.getString("column");
}

// 5. Close
rs.close(); stmt.close(); conn.close();
```

---

## Query Optimization Tips ⚡

### **Performance Best Practices:**
1. **Use indexes** on WHERE/JOIN columns
2. **Avoid SELECT *** - specify needed columns
3. **Use LIMIT** for large result sets
4. **Apply WHERE** before GROUP BY
5. **Use EXISTS** instead of IN for large subqueries
6. **Use PreparedStatement** to prevent SQL injection

### **Index Performance:**
- **Speeds up:** SELECT, JOIN, ORDER BY, GROUP BY
- **Slows down:** INSERT, UPDATE, DELETE
- **Storage cost:** 10-15% of table size

---

## Common Query Patterns 🎯

### **Top N Records:**
```sql
SELECT * FROM table ORDER BY column DESC LIMIT n;
```

### **Duplicate Finding:**
```sql
SELECT column, COUNT(*) FROM table 
GROUP BY column HAVING COUNT(*) > 1;
```

### **Rank/Dense Rank:**
```sql
SELECT *, ROW_NUMBER() OVER (ORDER BY column) as rank
FROM table;
```

### **Date Queries:**
```sql
-- Today's records
WHERE DATE(datetime_col) = CURDATE()

-- Last 30 days
WHERE datetime_col >= DATE_SUB(CURDATE(), INTERVAL 30 DAY)

-- Month-wise grouping
GROUP BY YEAR(date_col), MONTH(date_col)
```

---

## Exam Strategy & Time Management ⏰

### **Query Writing Questions (10-12 marks):**

#### **Time Allocation:**
- **Read & understand:** 3 minutes
- **Plan approach:** 2 minutes  
- **Write queries:** 10 minutes
- **Review & test:** 5 minutes
- **Total:** 20 minutes

#### **Step-by-step Approach:**
1. **Identify tables** needed
2. **Determine joins** required
3. **Apply WHERE** conditions
4. **Add GROUP BY** if needed
5. **Include ORDER BY** for sorting
6. **Test logic** mentally

### **Common Question Types:**
- **CRUD operations** (4-5 marks each)
- **JOIN queries** (6-7 marks)
- **Subqueries** (7-8 marks)
- **Aggregate with GROUP BY** (5-6 marks)
- **Index creation** (3-4 marks)

---

## Last Minute Formulas 💡

### **Query Order:**
**SELECT** → **FROM** → **WHERE** → **GROUP BY** → **HAVING** → **ORDER BY** → **LIMIT**

### **Join Memory:**
**INNER** = Common, **LEFT** = Left+Common, **RIGHT** = Right+Common, **FULL** = All

### **Aggregate Pattern:**
**GROUP BY** column → **HAVING** on aggregate → **ORDER BY** result

### **NULL Rule:**
**Anything with NULL = NULL** (except IS NULL/IS NOT NULL)

### **Index Decision:**
**Read-heavy + Large table = Index**, **Write-heavy + Small table = No Index**

---

## Practice Scenarios 🏆

### **E-commerce System:**
```sql
-- Tables: Customer, Product, Order, Order_Item
-- Practice: Sales reports, top customers, product analytics

-- Example: Top 5 customers by spending
SELECT c.name, SUM(oi.quantity * oi.price) as total
FROM Customer c 
JOIN Order o ON c.id = o.customer_id
JOIN Order_Item oi ON o.id = oi.order_id
GROUP BY c.id, c.name
ORDER BY total DESC LIMIT 5;
```

### **University System:**
```sql
-- Tables: Student, Course, Enrollment, Faculty
-- Practice: Grade reports, enrollment statistics, faculty workload

-- Example: Students with GPA > 3.5
SELECT s.name, AVG(e.grade_points) as gpa
FROM Student s
JOIN Enrollment e ON s.id = e.student_id
GROUP BY s.id, s.name
HAVING AVG(e.grade_points) > 3.5;
```

**Practice Time:** 45 minutes for complete module
