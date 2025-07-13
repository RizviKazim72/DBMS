# Module 3: Relational Model & Relational Algebra 🗂️

## Relational Model Kya Hai Bhai? 🤔

**Simple words mein:** Relational Model matlab data ko **tables** (relations) mein store karna - **rows aur columns** ke format mein!

Think of it as **Excel sheet on steroids** - but with strict rules aur powerful operations!

## Real-World Example 🌟
Imagine kar tu **WhatsApp contacts** dekh raha hai:
- **Table name:** Contacts
- **Columns:** Name, Phone, Last_Seen, Profile_Picture
- **Rows:** Har contact ka data
- **Primary Key:** Phone number (unique identifier)

---

## Relational Model Basics 📊

### **Key Terms:**
- **Relation (Table):** 2D structure with rows & columns
- **Tuple (Row):** Single record/entry
- **Attribute (Column):** Property/field  
- **Domain:** Valid values for an attribute
- **Degree:** Number of columns
- **Cardinality:** Number of rows

### **Example Table - Students:**
```
| Student_ID | Name      | Age | Course   | City     |
|------------|-----------|-----|----------|----------|
| 101        | Rahul     | 20  | CS       | Delhi    |
| 102        | Priya     | 19  | IT       | Mumbai   |
| 103        | Amit      | 21  | CS       | Pune     |
```

**Degree = 5** (5 columns), **Cardinality = 3** (3 rows)

---

## Relational Model Constraints 🔒

### **1. Domain Constraint**
- Har attribute valid values mein hona chahiye
- **Example:** Age sirf positive numbers, Gender sirf M/F

### **2. Key Constraint**
- Primary key unique aur non-null hona chahiye
- **Example:** Student_ID duplicate nahi ho sakta

### **3. Entity Integrity Constraint**
- Primary key kabhi NULL nahi ho sakta
- **Example:** Student_ID cannot be empty

### **4. Referential Integrity Constraint**
- Foreign key valid reference point karna chahiye
- **Example:** Student ka Course_ID valid course ko point kare

### **Real Example:**
```
STUDENT table:
- Student_ID (Primary Key) - cannot be NULL or duplicate
- Course_ID (Foreign Key) - must exist in COURSE table

COURSE table:
- Course_ID (Primary Key)
- Course_Name
```

---

## Keys in Detail 🔑

### **Primary Key**
- **Uniquely identify** karta hai each row
- **Cannot be NULL** or duplicate
- **Example:** Aadhar number, Student ID, Email

### **Foreign Key**
- **Reference** to another table's primary key
- **Links** two tables together
- **Can be NULL** (optional relationship)
- **Example:** Student table mein Course_ID pointing to Course table

### **Secondary Key (Alternate Key)**
- **Non-primary key** used for searching/indexing
- **Example:** Email, Phone number for quick lookup

### **Composite Key**
- **Multiple attributes** combined as primary key
- **Example:** {Course_ID + Student_ID} for enrollment table

### **Instagram Example:**
```
USER table:
- User_ID (Primary Key)
- Username (Secondary Key)
- Email (Secondary Key)

POST table:
- Post_ID (Primary Key)  
- User_ID (Foreign Key → USER.User_ID)
- Caption
- Timestamp
```

---

## ER to Relational Mapping 🔄

### **Mapping Rules:**

#### **1. Strong Entity → Table**
- Entity becomes table
- Attributes become columns
- Primary key remains same

#### **2. Weak Entity → Table with Composite Key**
- Include owner's primary key as foreign key
- Combine with partial key to form composite primary key

#### **3. 1:1 Relationship**
- **Option 1:** Merge both tables
- **Option 2:** Add foreign key in either table

#### **4. 1:M Relationship**
- Add foreign key in "many" side table

#### **5. M:N Relationship**
- Create separate junction table
- Include foreign keys from both entities
- Primary key = combination of both foreign keys

### **Practical Example - University System:**

**ER Model:**
```
STUDENT ──(enrolls)── COURSE
   │                     │
Student_ID          Course_ID
Name               Course_Name
Age                Credits
```

**Relational Schema:**
```sql
STUDENT(Student_ID, Name, Age)
COURSE(Course_ID, Course_Name, Credits)
ENROLLMENT(Student_ID, Course_ID, Grade, Date)
-- Foreign Keys: Student_ID → STUDENT, Course_ID → COURSE
```

---

## Relational Algebra - Operations Ki Power! ⚡

### **Relational Algebra Kya Hai?**
Mathematical operations to **retrieve data** from relational database. Think of it as **SQL ka mathematical foundation**!

### **Types of Operations:**

#### **Unary Operations (Single Table):**

##### **1. Selection (σ) - Row Filter**
- **Syntax:** σ<condition>(Table)
- **Example:** σ<Age>18>(STUDENT) // Students above 18

##### **2. Projection (π) - Column Filter** 
- **Syntax:** π<attributes>(Table)
- **Example:** π<Name,Age>(STUDENT) // Only Name and Age columns

##### **3. Rename (ρ) - Change Names**
- **Syntax:** ρ<new_name>(Table)
- **Example:** ρ<Students>(STUDENT) // Rename table to Students

#### **Binary Operations (Two Tables):**

##### **Set Operations:**

##### **1. Union (∪) - Combine All**
- **Condition:** Same structure (compatible tables)
- **Example:** STUDENT_CS ∪ STUDENT_IT // All CS and IT students

##### **2. Intersection (∩) - Common Records**
- **Example:** STUDENT_CS ∩ STUDENT_SPORTS // Students in both CS and Sports

##### **3. Difference (-) - Subtract**
- **Example:** STUDENT_ALL - STUDENT_PASSED // Failed students

##### **4. Cartesian Product (×) - All Combinations**
- **Example:** STUDENT × COURSE // Every student with every course

##### **Join Operations:**

##### **1. Natural Join (⋈) - Smart Combine**
- Automatically joins on common attributes
- **Example:** STUDENT ⋈ ENROLLMENT // Join on Student_ID

##### **2. Theta Join - Conditional Join**
- **Example:** STUDENT ⋈<Age>Course.MinAge> COURSE

##### **3. Equi Join - Equality Condition**
- **Example:** STUDENT ⋈<Student.ID=Enrollment.Student_ID> ENROLLMENT

---

## Practical Query Examples 📝

### **Sample Tables:**
```
STUDENT(Student_ID, Name, Age, City, Course_ID)
COURSE(Course_ID, Course_Name, Credits, Department)
ENROLLMENT(Student_ID, Course_ID, Grade, Semester)
```

### **Common Queries:**

#### **1. Find all students from Delhi:**
```
σ<City='Delhi'>(STUDENT)
```

#### **2. Get only names and ages of students:**
```
π<Name,Age>(STUDENT)
```

#### **3. Find CS students above 20:**
```
σ<Age>20 AND Course_ID='CS'>(STUDENT)
```

#### **4. Get student details with their courses:**
```
STUDENT ⋈ COURSE
```

#### **5. Find students who got 'A' grade:**
```
π<Name>(σ<Grade='A'>(STUDENT ⋈ ENROLLMENT))
```

---

## Real-World Application 🌍

### **E-commerce Query Examples:**

#### **Tables:**
```
CUSTOMER(Cust_ID, Name, City, Phone)
PRODUCT(Prod_ID, Name, Price, Category) 
ORDER(Order_ID, Cust_ID, Date, Total)
ORDER_ITEM(Order_ID, Prod_ID, Quantity, Price)
```

#### **Business Queries:**

##### **1. Find customers from Mumbai:**
```
σ<City='Mumbai'>(CUSTOMER)
```

##### **2. Products under ₹1000:**
```
σ<Price<1000>(PRODUCT)
```

##### **3. Customer orders with total amount:**
```
π<Name,Total>(CUSTOMER ⋈ ORDER)
```

##### **4. Electronics products ordered by customers:**
```
π<Customer.Name, Product.Name>(
  σ<Category='Electronics'>(
    CUSTOMER ⋈ ORDER ⋈ ORDER_ITEM ⋈ PRODUCT
  )
)
```

---

## Query Optimization Tips 🚀

### **Performance Tricks:**
1. **Selection first:** Apply σ before joins
2. **Projection early:** Use π to reduce data size
3. **Avoid Cartesian Product:** Always use proper joins
4. **Index usage:** Secondary keys help in fast retrieval

### **Example Optimization:**
```
// Slow:
π<Name>(σ<City='Delhi'>(STUDENT ⋈ COURSE))

// Fast:
π<Name>(σ<City='Delhi'>(STUDENT) ⋈ COURSE)
```

---

## Interview Questions 💡

**Q: Difference between Relational Algebra and SQL?**
**A:** Relational Algebra is mathematical foundation, SQL is practical implementation

**Q: Why use Foreign Keys?**
**A:** Maintain referential integrity, establish relationships, prevent orphan records

**Q: What's the most expensive operation?**
**A:** Cartesian Product - generates huge result set

**Q: How to optimize join operations?**
**A:** Use indexes on join columns, apply selections before joins

---

## Key Takeaways ✨

1. **Relational Model** = data in tables with rows & columns
2. **Constraints** maintain data integrity
3. **Keys** provide unique identification & relationships  
4. **ER Mapping** converts conceptual design to implementation
5. **Relational Algebra** = mathematical foundation for queries
6. **Operations** = Unary (selection, projection) + Binary (joins, set ops)
7. **Query optimization** = selection first, then joins

**Next:** SQL implementation aur advanced querying! 🎯
