# Module 3 - Relational Model Viva Preparation 🎤

## Basic Questions (Foundation) 🌟

**Q1: What is Relational Model?**
**A:** A logical data model that organizes data in tables (relations) with rows (tuples) and columns (attributes). Based on mathematical concept of relations.

**Q2: Define basic terms: Relation, Tuple, Attribute.**
**A:** 
- **Relation:** Table structure with rows and columns
- **Tuple:** Single row/record in a table  
- **Attribute:** Column/field representing a property

**Q3: What is difference between Degree and Cardinality?**
**A:**
- **Degree:** Number of columns/attributes in a relation
- **Cardinality:** Number of rows/tuples in a relation

**Q4: What are properties of relations?**
**A:**
- No duplicate tuples (each row unique)
- Tuples are unordered  
- Attributes are atomic (single-valued)
- Attribute names are unique
- Attributes are unordered

---

## Constraint-Based Questions 🔒

**Q5: What are different types of constraints in relational model?**
**A:** Four main types:
- **Domain Constraint:** Valid values for attributes
- **Key Constraint:** Uniqueness requirement
- **Entity Integrity:** Primary key cannot be NULL
- **Referential Integrity:** Foreign key must reference existing primary key

**Q6: Explain Entity Integrity with example.**
**A:** Primary key cannot be NULL or empty. 
**Example:** Student_ID in STUDENT table cannot be NULL because every student must be uniquely identifiable.

**Q7: What is Referential Integrity? Give example.**
**A:** Foreign key must reference an existing primary key value.
**Example:** Course_ID in STUDENT table must exist in COURSE table's Course_ID column.

**Q8: Can Foreign Key be NULL? Why?**
**A:** Yes, Foreign Key can be NULL to represent optional relationships.
**Example:** Employee may not have a manager (Manager_ID can be NULL).

---

## Key-Related Questions 🔑

**Q9: Explain different types of keys.**
**A:**
- **Primary Key:** Main unique identifier (Student_ID)
- **Foreign Key:** Reference to another table's primary key
- **Secondary Key:** Non-primary key used for indexing (Email)
- **Composite Key:** Multiple attributes forming primary key
- **Candidate Key:** Potential primary keys

**Q10: How do you choose Primary Key?**
**A:** Primary Key should be:
- **Unique** for every record
- **Non-null** (cannot be empty)
- **Minimal** (smallest number of attributes)
- **Stable** (value doesn't change often)

**Q11: Difference between Primary Key and Secondary Key?**
**A:**
- **Primary Key:** Main identifier, cannot be NULL, one per table
- **Secondary Key:** Alternative access path, can be NULL, multiple allowed

**Q12: What is Composite Key? When is it used?**
**A:** Primary key made of multiple attributes. Used when single attribute cannot uniquely identify records.
**Example:** ENROLLMENT(Student_ID, Course_ID) - both together form primary key.

---

## ER to Relational Mapping 🔄

**Q13: How do you map Strong Entity to Relational Model?**
**A:** Create a table with:
- Same name as entity
- All simple attributes become columns
- Primary key remains the same
- Add domain constraints

**Q14: How do you handle Weak Entity in relational mapping?**
**A:** 
- Create table for weak entity
- Include owner's primary key as foreign key
- Combine partial key + foreign key = composite primary key
**Example:** ORDER_ITEM(Order_ID, Item_Number) where (Order_ID, Item_Number) is composite primary key.

**Q15: How do you map 1:M relationship?**
**A:** Add foreign key in the "many" side table referencing primary key of "one" side.
**Example:** DEPARTMENT(1) → EMPLOYEE(M)
Add Dept_ID as foreign key in EMPLOYEE table.

**Q16: How do you handle M:N relationship?**
**A:** Create separate junction/bridge table with:
- Foreign keys from both entities
- Primary key = combination of foreign keys  
- Any relationship attributes
**Example:** STUDENT ↔ COURSE creates ENROLLMENT(Student_ID, Course_ID, Grade)

**Q17: Map 1:1 relationship - what are the options?**
**A:** Three options:
1. **Merge both entities** into single table
2. **Add foreign key** in either table
3. **Create separate table** for relationship (rare)

---

## Relational Algebra Questions ⚡

**Q18: What is Relational Algebra?**
**A:** Mathematical query language with operations to retrieve data from relational databases. It's procedural and forms the foundation for SQL.

**Q19: Explain Selection operation with example.**
**A:** Selection (σ) filters rows based on condition.
**Syntax:** σ<condition>(Relation)
**Example:** σ<Age>20>(STUDENT) - selects students above 20 years.

**Q20: What is Projection operation?**
**A:** Projection (π) selects specific columns and removes duplicates.
**Syntax:** π<attribute_list>(Relation)  
**Example:** π<Name,Age>(STUDENT) - shows only Name and Age columns.

**Q21: Difference between Selection and Projection?**
**A:**
- **Selection (σ):** Filters rows (horizontal filtering)
- **Projection (π):** Filters columns (vertical filtering)

**Q22: What are different types of Join operations?**
**A:**
- **Natural Join (⋈):** Auto-joins on common attributes
- **Theta Join:** Join with any condition
- **Equi Join:** Join with equality condition  
- **Outer Join:** Includes unmatched tuples

**Q23: When do you use Union vs Join?**
**A:**
- **Union:** Combines tables with **same structure** (compatible)
- **Join:** Combines tables with **related data** (different structures)

---

## Query Writing Questions 📝

**Q24: Write relational algebra to find students from Computer Science department.**
**A:** 
```
π<Name>(σ<Dept_Name='Computer Science'>(STUDENT ⋈ DEPARTMENT))
```

**Q25: Find students who got 'A' grade in any course.**
**A:**
```
π<Name>(σ<Grade='A'>(STUDENT ⋈ ENROLLMENT))
```

**Q26: How do you find students who haven't enrolled in any course?**
**A:**
```
π<Name>(STUDENT) - π<Name>(STUDENT ⋈ ENROLLMENT)
```

**Q27: Write query to find courses taken by student 'John'.**
**A:**
```
π<Course_Name>(σ<Name='John'>(STUDENT ⋈ ENROLLMENT ⋈ COURSE))
```

---

## Advanced Concepts 🚀

**Q28: What is Cartesian Product? When to avoid it?**
**A:** Cartesian Product (×) creates all possible combinations of tuples from two relations. 
**Avoid because:** Produces huge result set (very expensive).
**Use instead:** Proper join operations with conditions.

**Q29: How do you optimize relational algebra queries?**
**A:** Optimization techniques:
- **Apply selection early** (filter rows first)
- **Apply projection early** (reduce columns)
- **Use joins instead** of Cartesian product
- **Combine operations** when possible

**Q30: What is difference between Union and Union-compatible?**
**A:**
- **Union:** Operation to combine relations
- **Union-compatible:** Relations must have same number of attributes with compatible domains

---

## Practical Scenarios 🎯

**Q31: Design relational schema for Library Management System.**
**A:**
```sql
BOOK(Book_ID, Title, Author, Category, ISBN)
MEMBER(Member_ID, Name, Address, Phone, Join_Date)
BORROW(Member_ID, Book_ID, Issue_Date, Return_Date)

Foreign Keys:
- BORROW.Member_ID → MEMBER.Member_ID
- BORROW.Book_ID → BOOK.Book_ID
```

**Q32: How would you handle employee hierarchy in relational model?**
**A:** Two approaches:
1. **Recursive relationship:** EMPLOYEE(Emp_ID, Name, Manager_ID) where Manager_ID → Emp_ID
2. **Separate hierarchy table:** EMP_HIERARCHY(Emp_ID, Manager_ID, Level)

**Q33: Model a university system with students, courses, and enrollments.**
**A:**
```sql
STUDENT(Student_ID, Name, Age, Department)
COURSE(Course_ID, Course_Name, Credits, Department)  
ENROLLMENT(Student_ID, Course_ID, Grade, Semester)
INSTRUCTOR(Instructor_ID, Name, Department)
TEACHES(Instructor_ID, Course_ID, Semester)
```

---

## Problem-Solving Questions 🔧

**Q34: A table has no primary key. What problems can occur?**
**A:** Problems:
- **Duplicate records** possible
- **Cannot uniquely identify** rows
- **Referential integrity** cannot be maintained
- **Performance issues** in joining
- **Update anomalies** may occur

**Q35: How do you handle multi-valued attributes in relational model?**
**A:** Create separate table:
**Example:** Employee with multiple phone numbers
```sql
EMPLOYEE(Emp_ID, Name, Address)
EMP_PHONE(Emp_ID, Phone_Number)
Primary Key: (Emp_ID, Phone_Number)
```

**Q36: What happens if you violate referential integrity?**
**A:** Database management system will:
- **Reject the operation** (INSERT/UPDATE)
- **Display error message**
- **Maintain data consistency**
**Example:** Cannot add student with non-existent Course_ID.

---

## Quick Fire Round ⚡

**Q37: Can a table have multiple primary keys?**
**A:** No, only one primary key per table (but can be composite).

**Q38: Can foreign key be part of primary key?**
**A:** Yes, especially in junction tables for M:N relationships.

**Q39: Symbol for Selection operation?**
**A:** σ (sigma)

**Q40: Symbol for Projection operation?**  
**A:** π (pi)

**Q41: Symbol for Natural Join?**
**A:** ⋈ (bowtie)

**Q42: What does ρ represent?**
**A:** Rename operation (rho)

**Q43: Can attribute have NULL value?**
**A:** Yes, except for primary key attributes.

**Q44: What is BCNF?**
**A:** Boyce-Codd Normal Form (advanced normalization - Module 5 topic).

---

## Pro Tips for Viva Success 💡

### **Preparation Strategy:**
1. **Practice drawing** relational schemas quickly
2. **Memorize symbols** for relational algebra operations
3. **Prepare real examples** for each mapping rule
4. **Practice query writing** on paper

### **During Viva:**
1. **Define terms clearly** before explaining concepts
2. **Use examples** to illustrate every concept
3. **Draw schemas** when explaining mapping
4. **Show step-by-step** approach for queries

### **Common Mistakes to Avoid:**
- Don't confuse degree with cardinality
- Don't forget foreign key constraints
- Don't use Cartesian product unnecessarily
- Don't mix up selection and projection

### **Golden Rules:**
- **Always define** before explaining
- **Use consistent notation** for operations
- **Justify design decisions** in mapping
- **Check constraints** in every schema

**Practice Scenarios:** E-commerce, Banking, Hospital, Social Media

**Remember:** **Clear definitions** + **Good examples** + **Systematic approach** = **Outstanding Viva!** 🌟
