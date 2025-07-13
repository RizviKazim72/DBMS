# Module 3: Relational Model & Relational Algebra

## Introduction to Relational Model

**Relational Model:** A **logical data model** that represents data as a collection of **relations (tables)** where data is organized in **rows and columns**.

### **Key Components:**
- **Relation (Table):** Two-dimensional structure with rows and columns
- **Tuple (Row):** Single record containing related data
- **Attribute (Column):** Named property or characteristic
- **Domain:** Set of possible values for an attribute

### **Terminology:**

| **Informal Terms** | **Formal Terms** |
|---|---|
| Table | Relation |
| Row | Tuple |
| Column | Attribute |
| Number of rows | Cardinality |
| Number of columns | Degree |

### **Properties of Relations:**
1. **No duplicate tuples** - each row is unique
2. **Tuples are unordered** - no specific sequence
3. **Attributes are atomic** - single-valued
4. **Attribute names are unique** within relation
5. **Attributes are unordered** - column sequence doesn't matter

**Marks: 5-6 marks**

---

## Relational Model Constraints

### **Domain Constraints**
- **Definition:** Specifies **valid values** for each attribute
- **Examples:** 
  - Age: Positive integers (0-120)
  - Gender: {'M', 'F', 'Other'}
  - Email: Valid email format

### **Key Constraints**
- **Definition:** **Unique identification** requirement for tuples
- **Superkey:** Set of attributes that uniquely identifies tuples
- **Candidate Key:** Minimal superkey (no redundant attributes)
- **Primary Key:** Selected candidate key for unique identification

### **Entity Integrity Constraint**
- **Definition:** **Primary key cannot be NULL**
- **Reason:** Every entity must be uniquely identifiable
- **Example:** Student_ID cannot be empty in STUDENT relation

### **Referential Integrity Constraint**
- **Definition:** **Foreign key must reference existing primary key**
- **Purpose:** Maintains consistency across related tables
- **Example:** Course_ID in STUDENT must exist in COURSE table

### **Constraint Example:**
```sql
STUDENT(Student_ID, Name, Age, Course_ID)
- Student_ID: Primary Key (Entity Integrity - not NULL)
- Age: Domain Constraint (1-100)
- Course_ID: Foreign Key → COURSE(Course_ID) (Referential Integrity)
```

**Marks: 7-8 marks (Important topic)**

---

## Concept of Keys

### **Primary Key**
- **Definition:** **Main identifier** for each tuple in relation
- **Properties:**
  - **Unique** - no duplicates allowed
  - **Non-null** - cannot be empty
  - **Minimal** - smallest set of attributes needed
  - **Stable** - value should not change frequently

### **Secondary Key (Alternate Key)**
- **Definition:** **Non-primary candidate keys** used for efficient access
- **Purpose:** **Fast retrieval** and **alternative identification**
- **Examples:** Email, Phone number, Social Security Number
- **Usage:** Create indexes for faster searching

### **Foreign Key**
- **Definition:** **Attribute referencing primary key** of another relation
- **Purpose:** **Establish relationships** between tables
- **Properties:**
  - **Can be NULL** (optional relationship)
  - **Must match existing primary key** when not NULL
  - **Multiple foreign keys** allowed in same table

### **Composite Key**
- **Definition:** **Primary key consisting of multiple attributes**
- **When used:** When single attribute cannot uniquely identify
- **Example:** ENROLLMENT(Student_ID, Course_ID, Semester)

### **Key Hierarchy:**
```
Superkey
    ↓
Candidate Key (minimal superkey)
    ↓
Primary Key (selected candidate key)
```

### **Practical Example:**
```sql
EMPLOYEE Table:
- Employee_ID: Primary Key
- Email: Secondary Key (alternate access)
- Department_ID: Foreign Key → DEPARTMENT(Dept_ID)
- SSN: Candidate Key (could be primary key)

ENROLLMENT Table:
- (Student_ID, Course_ID): Composite Primary Key
- Student_ID: Foreign Key → STUDENT(Student_ID)
- Course_ID: Foreign Key → COURSE(Course_ID)
```

**Keys Concept: 8-10 marks (Very Important)**

---

## Mapping ER and EER Model to Relational Model

### **Mapping Rules:**

#### **1. Strong Entity Mapping**
- **Rule:** Create **separate table** for each strong entity
- **Primary Key:** Same as entity's primary key
- **Attributes:** All simple attributes become columns

**Example:**
```
ER: STUDENT(Student_ID, Name, Age, Address)
Relational: STUDENT(Student_ID, Name, Age, Address)
```

#### **2. Weak Entity Mapping**
- **Rule:** Create table with **composite primary key**
- **Primary Key:** Partial key + Foreign key from owner entity
- **Foreign Key:** Reference to strong entity

**Example:**
```
ER: ORDER → ORDER_ITEM (weak entity)
Relational: 
ORDER(Order_ID, Date, Customer_Name)
ORDER_ITEM(Order_ID, Item_Number, Product_Name, Quantity)
Primary Key: (Order_ID, Item_Number)
Foreign Key: Order_ID → ORDER(Order_ID)
```

#### **3. 1:1 Relationship Mapping**
**Three Options:**
- **Merge tables:** Combine both entities into single table
- **Foreign key approach:** Add foreign key in either table
- **Cross-reference:** Create separate relationship table

**Example:**
```
PERSON ──1:1── PASSPORT
Option 1: PERSON(Person_ID, Name, Passport_Number, Issue_Date)
Option 2: PERSON(Person_ID, Name) + PASSPORT(Passport_Number, Person_ID, Issue_Date)
```

#### **4. 1:M Relationship Mapping**
- **Rule:** Add **foreign key in "many" side** table
- **Foreign Key:** References primary key of "one" side

**Example:**
```
DEPARTMENT ──1:M── EMPLOYEE
DEPARTMENT(Dept_ID, Dept_Name, Location)
EMPLOYEE(Emp_ID, Name, Salary, Dept_ID)
Foreign Key: Dept_ID → DEPARTMENT(Dept_ID)
```

#### **5. M:N Relationship Mapping**
- **Rule:** Create **separate junction table**
- **Primary Key:** Combination of foreign keys
- **Additional Attributes:** Relationship attributes included

**Example:**
```
STUDENT ──M:N── COURSE (with Grade)
STUDENT(Student_ID, Name, Age)
COURSE(Course_ID, Course_Name, Credits)
ENROLLMENT(Student_ID, Course_ID, Grade, Semester)
Primary Key: (Student_ID, Course_ID)
Foreign Keys: Student_ID → STUDENT, Course_ID → COURSE
```

#### **6. Multi-valued Attribute Mapping**
- **Rule:** Create **separate table** for multi-valued attribute
- **Primary Key:** Owner's primary key + attribute value

**Example:**
```
ER: EMPLOYEE(Emp_ID, Name, Phone_Numbers)
Relational:
EMPLOYEE(Emp_ID, Name)
EMPLOYEE_PHONE(Emp_ID, Phone_Number)
Primary Key: (Emp_ID, Phone_Number)
```

#### **7. Composite Attribute Mapping**
- **Option 1:** **Flatten** into separate columns
- **Option 2:** **Single column** with formatted value

**Example:**
```
ER: Address(Street, City, State, PIN)
Option 1: Street, City, State, PIN (separate columns)
Option 2: Address (single formatted column)
```

### **EER Model Mapping:**

#### **Generalization/Specialization:**
**Four Approaches:**
1. **Create tables for superclass and subclasses**
2. **Create tables only for subclasses** 
3. **Create single table with type indicator**
4. **Create table for superclass with optional attributes**

**Example:**
```
PERSON → {STUDENT, EMPLOYEE}
Approach 1:
PERSON(Person_ID, Name, Age)
STUDENT(Person_ID, Student_Number, Major)
EMPLOYEE(Person_ID, Employee_Number, Salary)
```

**Mapping Rules: 10-12 marks (Very Important)**

---

## Introduction to Relational Algebra

### **Definition:**
**Relational Algebra** is a **procedural query language** that uses **mathematical operations** to retrieve data from relational databases.

### **Characteristics:**
- **Procedural** - specify how to get result
- **Closed** - operations on relations produce relations
- **Foundation** for SQL and other query languages
- **Theoretical basis** for query optimization

### **Types of Operations:**

#### **Unary Operations** (Single Relation):
1. **Selection (σ)** - Select rows
2. **Projection (π)** - Select columns  
3. **Rename (ρ)** - Rename relation/attributes

#### **Binary Operations** (Two Relations):
1. **Union (∪)** - Combine relations
2. **Intersection (∩)** - Common tuples
3. **Difference (-)** - Subtract relations
4. **Cartesian Product (×)** - All combinations
5. **Join Operations** - Various join types

**Marks: 6-7 marks**

---

## Unary Relational Operations

### **1. Selection (σ)**
**Purpose:** **Select specific rows** based on condition

**Syntax:** σ<condition>(Relation)

**Examples:**
```
σ<Age>20>(STUDENT) - Students above 20
σ<City='Delhi'>(STUDENT) - Students from Delhi  
σ<Age>18 AND Gender='F'>(STUDENT) - Female students above 18
```

**Conditions:**
- **Comparison:** =, ≠, <, ≤, >, ≥
- **Logical:** AND, OR, NOT
- **Pattern:** LIKE (for string matching)

### **2. Projection (π)**
**Purpose:** **Select specific columns** and remove duplicates

**Syntax:** π<attribute_list>(Relation)

**Examples:**
```
π<Name,Age>(STUDENT) - Only Name and Age columns
π<Student_ID>(STUDENT) - Only Student IDs
π<Name>(σ<City='Mumbai'>(STUDENT)) - Names of Mumbai students
```

**Properties:**
- **Removes duplicates** automatically
- **Reduces relation size**
- **Can be combined** with other operations

### **3. Rename (ρ)**
**Purpose:** **Rename relations and attributes**

**Syntax:** 
- ρ<new_name>(Relation) - Rename relation
- ρ<new_name(attr_list)>(Relation) - Rename attributes

**Examples:**
```
ρ<Students>(STUDENT) - Rename table to Students
ρ<S(ID,Name,Age)>(STUDENT) - Rename table and attributes
```

**Unary Operations: 6-7 marks**

---

## Set Theory Operations & Binary Operations

### **Set Theory Operations**
**Requirement:** Relations must be **union compatible** (same structure)

#### **1. Union (∪)**
**Purpose:** **Combine all tuples** from both relations

**Example:**
```
STUDENT_CS ∪ STUDENT_IT = All students from CS and IT
```

#### **2. Intersection (∩)**
**Purpose:** **Common tuples** in both relations

**Example:**
```
STUDENT_CS ∩ STUDENT_SPORTS = Students in both CS and Sports
```

#### **3. Difference (-)**
**Purpose:** **Tuples in first but not in second** relation

**Example:**
```
ALL_STUDENTS - PASSED_STUDENTS = Failed students
```

### **Cartesian Product (×)**
**Purpose:** **All possible combinations** of tuples

**Example:**
```
STUDENT × COURSE = Every student paired with every course
If STUDENT has 100 rows, COURSE has 50 rows
Result: 100 × 50 = 5000 rows
```

**Warning:** **Very expensive operation** - use joins instead

### **Join Operations**

#### **1. Natural Join (⋈)**
**Purpose:** **Join on common attributes** automatically

**Example:**
```
STUDENT ⋈ ENROLLMENT
Joins on common attribute Student_ID
```

#### **2. Theta Join**
**Purpose:** **Join with any condition**

**Syntax:** R ⋈<condition> S

**Example:**
```
STUDENT ⋈<Student.Age > Course.Min_Age> COURSE
```

#### **3. Equi Join**
**Purpose:** **Join with equality condition**

**Example:**
```
STUDENT ⋈<Student.Dept_ID = Department.Dept_ID> DEPARTMENT
```

#### **4. Outer Joins**
- **Left Outer Join:** Include all tuples from left relation
- **Right Outer Join:** Include all tuples from right relation  
- **Full Outer Join:** Include all tuples from both relations

**Binary Operations: 8-10 marks**

---

## Relational Algebra Queries

### **Complex Query Examples:**

#### **Sample Schema:**
```sql
STUDENT(Student_ID, Name, Age, City, Dept_ID)
DEPARTMENT(Dept_ID, Dept_Name, HOD)
COURSE(Course_ID, Course_Name, Credits, Dept_ID)
ENROLLMENT(Student_ID, Course_ID, Grade, Semester)
```

#### **Query Examples:**

**1. Find names of students from Computer Science department:**
```
π<Name>(σ<Dept_Name='Computer Science'>(STUDENT ⋈ DEPARTMENT))
```

**2. Find students who got 'A' grade in any course:**
```
π<Name>(σ<Grade='A'>(STUDENT ⋈ ENROLLMENT))
```

**3. Find courses taken by student 'John':**
```
π<Course_Name>(σ<Name='John'>(STUDENT ⋈ ENROLLMENT ⋈ COURSE))
```

**4. Find students who haven't enrolled in any course:**
```
π<Name>(STUDENT) - π<Name>(STUDENT ⋈ ENROLLMENT)
```

**5. Find departments with more than 50 students:**
```
π<Dept_Name>(σ<Count>50>(DEPARTMENT ⋈ (γ<Dept_ID, COUNT(*)>(STUDENT))))
```

### **Query Optimization Principles:**
1. **Apply selection early** - reduce tuples before join
2. **Apply projection early** - reduce attributes
3. **Use natural joins** instead of Cartesian product
4. **Combine operations** when possible

**Query Examples: 8-10 marks**

---

## Important Points for Exams

### **Must Remember:**
1. **Relational model terminology** - relation, tuple, attribute
2. **Constraint types** - domain, key, entity integrity, referential integrity
3. **Key concepts** - primary, foreign, secondary, composite keys
4. **ER to Relational mapping rules** for all relationship types
5. **Relational algebra operations** - unary and binary
6. **Query writing** using relational algebra expressions

### **Common Exam Questions:**
- **Map ER diagram to relational schema** **(10-12 marks)**
- **Write relational algebra queries** **(8-10 marks)**
- **Explain constraint types** with examples **(6-7 marks)**
- **Define key types** and their purposes **(5-6 marks)**

### **Key Terms:**
- **Relation, Tuple, Attribute, Domain**
- **Primary Key, Foreign Key, Referential Integrity**
- **Selection, Projection, Join, Union**
- **Entity Integrity, Cardinality, Degree**

**Total Module Weightage: 20-25 marks**
