# Module 5: Relational Database Design

## Design Guidelines for Relational Schema

### **Fundamental Design Principles:**

#### **1. Clear Semantics of Relation Schemas**
- **Each relation should represent one concept clearly**
- **Avoid mixing unrelated concepts in single relation**
- **Relation name should indicate its purpose**

**Good Design:**
```sql
STUDENT(Student_ID, Name, Age, Address)
COURSE(Course_ID, Course_Name, Credits, Department)
ENROLLMENT(Student_ID, Course_ID, Grade, Semester)
```

**Poor Design:**
```sql
STUDENT_COURSE(Student_ID, Name, Age, Course_ID, Course_Name, Grade)
-- Mixes student info, course info, and enrollment
```

#### **2. Minimize Redundant Information**
- **Avoid storing same information in multiple places**
- **Reduces storage space and update anomalies**
- **Maintain single source of truth**

#### **3. Minimize NULL Values**
- **NULL values waste storage space**
- **Create confusion in query results**
- **Complicate aggregate calculations**

#### **4. Avoid Spurious Tuples**
- **Ensure joins produce meaningful results**
- **Avoid relations that create invalid combinations**
- **Maintain referential integrity**

### **Design Quality Measures:**

#### **Storage Efficiency:**
- **Minimize redundant data storage**
- **Optimize space utilization**
- **Consider compression possibilities**

#### **Update Efficiency:**
- **Minimize update operations required**
- **Reduce chances of inconsistency**
- **Simplify maintenance procedures**

#### **Query Performance:**
- **Optimize for common query patterns**
- **Balance normalization with performance**
- **Consider indexing requirements**

**Marks: 5-6 marks**

---

## Functional Dependencies

### **Definition:**
A **functional dependency** X → Y exists if whenever two tuples agree on the value of X, they must agree on the value of Y.

**Interpretation:** X **functionally determines** Y, or Y is **functionally dependent** on X.

### **Notation and Examples:**

#### **Basic Examples:**
```sql
Student_ID → Student_Name    (Student ID determines name)
SSN → Birth_Date            (Social Security Number determines birth date)
Course_ID → Course_Name     (Course ID determines course name)
Employee_ID → Salary        (Employee ID determines salary)
```

#### **Composite Key Dependencies:**
```sql
{Student_ID, Course_ID} → Grade
{Order_ID, Product_ID} → Quantity
{Employee_ID, Project_ID} → Hours_Worked
```

### **Types of Functional Dependencies:**

#### **1. Full Functional Dependency**
**Definition:** Y is **fully functionally dependent** on X if:
- X → Y (Y depends on X)
- Y does not depend on any proper subset of X

**Example:**
```sql
{Student_ID, Course_ID} → Grade
-- Grade depends on BOTH Student_ID AND Course_ID
-- Cannot determine grade from Student_ID alone or Course_ID alone
```

#### **2. Partial Functional Dependency**
**Definition:** Y is **partially functionally dependent** on X if:
- X → Y (Y depends on X)  
- Y depends on some proper subset of X

**Example:**
```sql
{Student_ID, Course_ID} → Student_Name
-- Student_Name depends only on Student_ID
-- Course_ID is not needed to determine Student_Name
```

#### **3. Transitive Functional Dependency**
**Definition:** If X → Y and Y → Z, then X → Z (transitively)

**Example:**
```sql
Student_ID → Department_ID → Department_Head
-- Student_ID determines Department_ID
-- Department_ID determines Department_Head  
-- Therefore: Student_ID → Department_Head (transitively)
```

### **Properties of Functional Dependencies:**

#### **Armstrong's Axioms:**
1. **Reflexivity:** If Y ⊆ X, then X → Y
2. **Augmentation:** If X → Y, then XZ → YZ  
3. **Transitivity:** If X → Y and Y → Z, then X → Z

#### **Derived Rules:**
1. **Union:** If X → Y and X → Z, then X → YZ
2. **Decomposition:** If X → YZ, then X → Y and X → Z
3. **Pseudo-transitivity:** If X → Y and WY → Z, then WX → Z

**Functional Dependencies: 7-8 marks**

---

## Database Tables and Normalization

### **The Need for Normalization**

#### **Problems with Unnormalized Tables:**

##### **1. Update Anomalies**
**Problem:** Same information stored in multiple places requires multiple updates.

**Example:**
```sql
STUDENT_COURSE(Student_ID, Name, Course_ID, Course_Name, Instructor, Grade)
-- If instructor changes for a course, must update all student records
```

##### **2. Insert Anomalies**  
**Problem:** Cannot insert certain information without other unrelated data.

**Example:**
```sql
-- Cannot add new course without having a student enrolled
-- Cannot add student without course enrollment
```

##### **3. Delete Anomalies**
**Problem:** Deleting a record may cause loss of other important information.

**Example:**
```sql
-- Deleting last student in course loses course information
-- Deleting course enrollment may lose student information
```

##### **4. Storage Redundancy**
**Problem:** Same data repeated multiple times wastes storage space.

**Example:**
```sql
Student_ID | Name  | Course_ID | Course_Name | Instructor
101        | John  | CS101     | Database    | Dr. Smith
101        | John  | CS102     | Networks    | Dr. Jones  
102        | Mary  | CS101     | Database    | Dr. Smith
-- John's name repeated, Database course info repeated
```

### **Benefits of Normalization:**

#### **1. Eliminates Redundancy**
- **Single storage** of each fact
- **Reduced storage** requirements
- **Consistent information** across database

#### **2. Prevents Anomalies**
- **No update anomalies** - change once, reflects everywhere
- **No insert anomalies** - can add information independently  
- **No delete anomalies** - no unintended information loss

#### **3. Ensures Data Integrity**
- **Consistent data** across all tables
- **Referential integrity** maintained
- **Business rules** enforced through structure

#### **4. Improves Maintenance**
- **Easier updates** and modifications
- **Simpler validation** rules
- **Better error detection**

**Need for Normalization: 6-7 marks**

---

## The Normalization Process

### **Overview:**
Normalization is a **step-by-step process** of organizing data to **minimize redundancy** and **dependency** by dividing large tables into smaller, related tables.

### **Normal Form Hierarchy:**
```
Unnormalized → 1NF → 2NF → 3NF → BCNF → 4NF → 5NF
```

### **General Process:**
1. **Start with unnormalized relation**
2. **Apply 1NF rules** (eliminate repeating groups)
3. **Apply 2NF rules** (eliminate partial dependencies)  
4. **Apply 3NF rules** (eliminate transitive dependencies)
5. **Apply BCNF rules** (eliminate remaining anomalies)

---

## Definition of Normal Forms

### **First Normal Form (1NF)**

#### **Definition:**
A relation is in **1NF** if:
- **Domain of each attribute contains atomic values**
- **No repeating groups or multi-valued attributes**
- **Primary key is defined**

#### **Rules:**
1. **Eliminate repeating groups**
2. **Create separate row for each group item**
3. **Ensure atomic values** in each cell

#### **Example - Before 1NF:**
```sql
STUDENT Table:
Student_ID | Name  | Courses
101        | John  | Database, Networks, OS
102        | Mary  | Database, Programming
103        | David | Networks, Security
```

#### **Example - After 1NF:**
```sql
STUDENT_COURSE Table:
Student_ID | Name  | Course
101        | John  | Database
101        | John  | Networks  
101        | John  | OS
102        | Mary  | Database
102        | Mary  | Programming
103        | David | Networks
103        | David | Security

Primary Key: {Student_ID, Course}
```

### **Second Normal Form (2NF)**

#### **Definition:**
A relation is in **2NF** if:
- **It is in 1NF**
- **No partial functional dependencies** exist on primary key

#### **Rules:**
1. **Must be in 1NF first**
2. **Every non-key attribute must be fully functionally dependent on primary key**
3. **Eliminate partial dependencies by decomposition**

#### **Example - Before 2NF (1NF with problems):**
```sql
STUDENT_COURSE Table:
Student_ID | Course_ID | Student_Name | Course_Name | Credits | Grade
101        | CS101     | John         | Database    | 3       | A
101        | CS102     | John         | Networks    | 4       | B
102        | CS101     | Mary         | Database    | 3       | A+

Primary Key: {Student_ID, Course_ID}

Partial Dependencies:
Student_ID → Student_Name     (partial dependency)
Course_ID → Course_Name       (partial dependency)  
Course_ID → Credits           (partial dependency)
```

#### **Example - After 2NF:**
```sql
STUDENT Table:
Student_ID | Student_Name
101        | John
102        | Mary

COURSE Table:  
Course_ID | Course_Name | Credits
CS101     | Database    | 3
CS102     | Networks    | 4

ENROLLMENT Table:
Student_ID | Course_ID | Grade
101        | CS101     | A
101        | CS102     | B  
102        | CS101     | A+
```

### **Third Normal Form (3NF)**

#### **Definition:**
A relation is in **3NF** if:
- **It is in 2NF**
- **No transitive functional dependencies** exist

#### **Rules:**
1. **Must be in 2NF first**
2. **No non-key attribute should be transitively dependent on primary key**
3. **Eliminate transitive dependencies by decomposition**

#### **Example - Before 3NF (2NF with problems):**
```sql
STUDENT Table:
Student_ID | Name | Dept_ID | Dept_Name    | Dept_Head
101        | John | CS      | Computer Sci | Dr. Smith
102        | Mary | IT      | Info Tech    | Dr. Jones
103        | Bob  | CS      | Computer Sci | Dr. Smith

Primary Key: Student_ID

Transitive Dependencies:
Student_ID → Dept_ID → Dept_Name     (transitive)
Student_ID → Dept_ID → Dept_Head     (transitive)
```

#### **Example - After 3NF:**
```sql
STUDENT Table:
Student_ID | Name | Dept_ID
101        | John | CS
102        | Mary | IT
103        | Bob  | CS

DEPARTMENT Table:
Dept_ID | Dept_Name    | Dept_Head
CS      | Computer Sci | Dr. Smith  
IT      | Info Tech    | Dr. Jones
```

### **Boyce-Codd Normal Form (BCNF)**

#### **Definition:**
A relation is in **BCNF** if:
- **It is in 3NF**
- **Every determinant is a candidate key**

#### **Rules:**
1. **Must be in 3NF first**
2. **For every functional dependency X → Y, X must be a superkey**
3. **Eliminates anomalies that 3NF might miss**

#### **Example - 3NF but not BCNF:**
```sql
COURSE_INSTRUCTOR Table:
Course_ID | Instructor_ID | Room
CS101     | I001          | R101
CS101     | I002          | R102  
CS102     | I001          | R101
CS102     | I003          | R103

Primary Key: {Course_ID, Instructor_ID}

Functional Dependencies:
{Course_ID, Instructor_ID} → Room     (✓ valid)
Instructor_ID → Room                  (✗ violates BCNF)
-- Instructor_ID is determinant but not candidate key
```

#### **Example - After BCNF:**
```sql
COURSE_INSTRUCTOR Table:
Course_ID | Instructor_ID
CS101     | I001
CS101     | I002
CS102     | I001  
CS102     | I003

INSTRUCTOR_ROOM Table:
Instructor_ID | Room
I001          | R101
I002          | R102
I003          | R103
```

### **Normal Form Comparison:**

| **Normal Form** | **Eliminates** | **Key Rule** |
|---|---|---|
| **1NF** | Repeating groups | Atomic values |
| **2NF** | Partial dependencies | Full dependency on key |
| **3NF** | Transitive dependencies | No indirect dependencies |
| **BCNF** | Determinant anomalies | All determinants are keys |

**Normal Forms: 12-15 marks (Very Important)**

---

## Improving the Design

### **Design Evaluation Criteria:**

#### **1. Lossless Join Property**
- **Decomposition should preserve all information**
- **Natural join of decomposed relations should recreate original**
- **No spurious tuples generated**

#### **2. Dependency Preservation**
- **All functional dependencies should be preserved**
- **Either directly enforced or derivable from constraints**
- **Business rules maintained after decomposition**

#### **3. Separation of Concerns**
- **Each relation represents single concept**
- **Related attributes grouped together**
- **Unrelated concepts separated**

### **Decomposition Guidelines:**

#### **Good Decomposition:**
- **Lossless join decomposition**
- **Dependency preserving decomposition**  
- **Achieves desired normal form**
- **Maintains semantic meaning**

#### **Poor Decomposition:**
- **Lossy join** (information lost)
- **Dependencies not preserved**
- **Over-normalization** (too many small tables)
- **Under-normalization** (anomalies remain)

### **Denormalization Considerations:**

#### **When to Denormalize:**
- **Performance critical applications**
- **Read-heavy workloads** 
- **Data warehousing scenarios**
- **Reporting requirements**

#### **Denormalization Techniques:**
- **Controlled redundancy** for performance
- **Materialized views** for complex queries
- **Summary tables** for aggregations
- **Flattened structures** for reporting

**Design Improvement: 6-7 marks**

---

## Important Points for Exams

### **Must Remember:**
1. **Design guidelines** - clear semantics, minimal redundancy, avoid NULLs
2. **Functional dependency types** - full, partial, transitive
3. **Normalization benefits** - eliminates anomalies, reduces redundancy
4. **Normal form definitions** and **step-by-step conversion**
5. **1NF → 2NF → 3NF → BCNF progression**
6. **Real-world application** of normalization principles

### **Common Exam Questions:**
- **Normalize given table to 3NF/BCNF** **(10-12 marks)**
- **Identify functional dependencies** **(5-6 marks)**
- **Explain normalization benefits** **(6-7 marks)**
- **Compare different normal forms** **(5-6 marks)**
- **Design database schema** for given scenario **(8-10 marks)**

### **Key Terms:**
- **Functional Dependency, Partial Dependency, Transitive Dependency**
- **1NF, 2NF, 3NF, BCNF**
- **Update Anomaly, Insert Anomaly, Delete Anomaly**
- **Lossless Join, Dependency Preservation**
- **Normalization, Denormalization, Decomposition**

**Total Module Weightage: 18-20 marks**
