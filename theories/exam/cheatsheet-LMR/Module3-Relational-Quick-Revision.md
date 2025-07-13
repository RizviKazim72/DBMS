# Module 3 - Relational Model Quick Revision ⚡

## Core Definitions 🎯

- **Relation:** Table with rows and columns
- **Tuple:** Single row/record  
- **Attribute:** Column/field
- **Domain:** Valid values for attribute
- **Degree:** Number of columns
- **Cardinality:** Number of rows

---

## Relational Model Constraints 🔒

### **4 Main Types:**

#### **1. Domain Constraint**
- Valid values for each attribute
- **Example:** Age (0-120), Gender (M/F)

#### **2. Key Constraint**  
- Primary key must be unique
- **Example:** Student_ID cannot duplicate

#### **3. Entity Integrity**
- Primary key cannot be NULL
- **Example:** Student_ID ≠ NULL

#### **4. Referential Integrity**
- Foreign key must reference existing primary key
- **Example:** Course_ID must exist in COURSE table

**Memory Trick:** **D-K-E-R** = **Domain-Key-Entity-Referential**

---

## Key Types Quick Guide 🔑

| **Key Type** | **Purpose** | **Properties** |
|---|---|---|
| **Primary** | Main identifier | Unique + Non-null |
| **Foreign** | Link tables | Can be null, references PK |
| **Secondary** | Fast access | Non-primary, indexed |
| **Composite** | Multiple attributes | Combined uniqueness |

### **Key Selection Formula:**
**Unique** + **Minimal** + **Stable** = **Good Primary Key**

---

## ER to Relational Mapping 🔄

### **Quick Rules:**

#### **Strong Entity → Table**
- Direct conversion, keep primary key

#### **Weak Entity → Table + Composite Key**
- Owner PK + Partial Key = New PK

#### **1:1 Relationship**
- **Merge tables** OR **FK in either side**

#### **1:M Relationship**  
- **FK in "many" side** table

#### **M:N Relationship**
- **Create junction table** with both FKs

#### **Multi-valued Attribute**
- **Separate table** with owner PK + attribute

### **Memory Formula:**
**Strong=Table, Weak=Composite, 1:1=Merge, 1:M=FK-Many, M:N=Junction**

---

## Relational Algebra Operations ⚡

### **Unary Operations (Single Table):**

#### **Selection (σ) - Row Filter**
```
σ<condition>(Table)
Example: σ<Age>20>(STUDENT)
```

#### **Projection (π) - Column Filter**
```
π<attributes>(Table)  
Example: π<Name,Age>(STUDENT)
```

#### **Rename (ρ) - Change Names**
```
ρ<new_name>(Table)
Example: ρ<Students>(STUDENT)
```

### **Binary Operations (Two Tables):**

#### **Set Operations:**
- **Union (∪):** Combine all
- **Intersection (∩):** Common records  
- **Difference (-):** Subtract
- **Cartesian (×):** All combinations

#### **Join Operations:**
- **Natural Join (⋈):** Auto-join on common attributes
- **Theta Join:** Join with condition
- **Equi Join:** Join with equality

### **Operation Priority:**
**Selection > Projection > Join > Set Operations**

---

## Query Writing Strategy 📝

### **5-Step Process:**
1. **Identify** required tables
2. **Apply selections** early (filter rows)
3. **Apply projections** early (filter columns)
4. **Join** tables as needed
5. **Apply** set operations last

### **Example Pattern:**
```
"Find names of CS students above 20"

Step 1: Need STUDENT, DEPARTMENT tables
Step 2: σ<Age>20>(STUDENT) 
Step 3: σ<Dept_Name='CS'>(DEPARTMENT)
Step 4: Join filtered tables
Step 5: π<Name>(result)

Final: π<Name>(σ<Age>20>(STUDENT) ⋈ σ<Dept_Name='CS'>(DEPARTMENT))
```

---

## Common Query Patterns 🎯

### **Find Pattern:**
```
π<select_attributes>(σ<conditions>(Table1 ⋈ Table2))
```

### **Count Pattern:**
```
γ<group_by, COUNT(*)>(σ<conditions>(Table))
```

### **Difference Pattern:**
```
π<attributes>(Table1) - π<attributes>(Table2)
```

### **Multiple Conditions:**
```
σ<condition1 AND condition2 OR condition3>(Table)
```

---

## Constraint Examples 📊

### **Sample Table: STUDENT**
```
STUDENT(Student_ID, Name, Age, Email, Course_ID)
```

### **Constraints Applied:**
- **Domain:** Age (16-60), Email (valid format)
- **Key:** Student_ID (unique, non-null)
- **Entity Integrity:** Student_ID ≠ NULL
- **Referential:** Course_ID → COURSE(Course_ID)

---

## Mapping Examples Quick Reference 🔧

### **University System Mapping:**

#### **ER Model:**
```
STUDENT ──(enrolls)── COURSE
DEPARTMENT ──(offers)── COURSE  
STUDENT ──(belongs)── DEPARTMENT
```

#### **Relational Schema:**
```sql
STUDENT(Student_ID, Name, Age, Dept_ID)
DEPARTMENT(Dept_ID, Dept_Name, HOD)
COURSE(Course_ID, Course_Name, Dept_ID)
ENROLLMENT(Student_ID, Course_ID, Grade)

Foreign Keys:
- STUDENT.Dept_ID → DEPARTMENT.Dept_ID
- COURSE.Dept_ID → DEPARTMENT.Dept_ID  
- ENROLLMENT.Student_ID → STUDENT.Student_ID
- ENROLLMENT.Course_ID → COURSE.Course_ID
```

---

## Exam Strategy ⏰

### **ER to Relational Questions (10-12 marks):**

#### **Time Allocation:**
- **Analyze ER:** 3 minutes
- **Apply mapping rules:** 8 minutes
- **Write schema:** 6 minutes
- **Add constraints:** 3 minutes
- **Total:** 20 minutes

#### **Step-by-step:**
1. **List all entities** (strong/weak)
2. **List all relationships** (1:1, 1:M, M:N)
3. **Apply mapping rules** systematically
4. **Define primary/foreign keys**
5. **Add constraints**

### **Relational Algebra Questions (8-10 marks):**

#### **Time Allocation:**
- **Understand query:** 2 minutes
- **Plan approach:** 3 minutes
- **Write expression:** 10 minutes
- **Total:** 15 minutes

#### **Strategy:**
1. **Read query carefully**
2. **Identify required tables**
3. **Break into sub-problems**
4. **Apply operations step-by-step**
5. **Optimize expression**

---

## Last Minute Formulas 💡

### **Constraint Check:**
**Domain → Key → Entity → Referential**

### **Mapping Priority:**
**Entities first → Relationships → Attributes → Constraints**

### **Query Construction:**
**Selection first → Projection → Joins → Set Operations**

### **Key Identification:**
**Unique + Non-null + Minimal = Primary Key**

### **Join Selection:**
**Natural Join > Equi Join > Theta Join > Cartesian Product**

---

## Common Mistakes to Avoid ❌

### **Mapping Errors:**
- ❌ Forgetting foreign keys in 1:M relationships
- ❌ Not creating junction table for M:N
- ❌ Missing composite keys for weak entities

### **Query Errors:**
- ❌ Using Cartesian product instead of joins
- ❌ Not applying selection early
- ❌ Forgetting to remove duplicates in projection

### **Constraint Errors:**
- ❌ Allowing NULL in primary keys
- ❌ Foreign key not referencing existing primary key
- ❌ Domain constraints too restrictive/loose

**Practice Time:** 30 minutes for complete module revision
