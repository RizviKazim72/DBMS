# Module 5: Database Design - Viva Questions & Answers

## 🎯 Design Guidelines Questions

### **Q1: What are the main design guidelines for relational databases?**
**A:** The four main guidelines are:
1. **Clear Semantics** - Each relation should represent one clear concept
2. **Minimal Redundancy** - Avoid storing same information multiple times
3. **Minimal NULL Values** - Reduce empty/unknown values in attributes
4. **No Spurious Tuples** - Ensure joins produce meaningful results only

### **Q2: Why should we avoid NULL values in database design?**
**A:** NULL values should be avoided because:
- They **waste storage space**
- Create **confusion in query results** (NULL ≠ 0 ≠ empty string)
- **Complicate aggregate functions** (COUNT, SUM, AVG)
- Make **three-valued logic** (True/False/Unknown) complex

### **Q3: What is the difference between logical and physical database design?**
**A:** 
- **Logical Design:** Focuses on **what data** to store and **relationships** between data (ER modeling, normalization)
- **Physical Design:** Focuses on **how to store** data efficiently (indexing, partitioning, storage allocation)

---

## 🔗 Functional Dependencies Questions

### **Q4: Define functional dependency with example.**
**A:** A functional dependency X → Y means:
- **Definition:** If two tuples have same value for X, they must have same value for Y
- **Example:** Student_ID → Student_Name (Every student ID uniquely determines student name)
- **Notation:** X is called **determinant**, Y is called **dependent**

### **Q5: Explain full vs partial functional dependency.**
**A:**
- **Full FD:** Attribute depends on **complete composite key**
  - Example: {Student_ID, Course_ID} → Grade
- **Partial FD:** Attribute depends on **part of composite key** only
  - Example: {Student_ID, Course_ID} → Student_Name (depends only on Student_ID)

### **Q6: What is transitive functional dependency?**
**A:** When dependency exists through another attribute:
- **Definition:** If A → B and B → C, then A → C (transitively)
- **Example:** Student_ID → Department_ID → Department_Head
- **Problem:** Creates redundancy and update anomalies

### **Q7: State Armstrong's axioms.**
**A:** The three fundamental rules:
1. **Reflexivity:** If Y ⊆ X, then X → Y
2. **Augmentation:** If X → Y, then XZ → YZ
3. **Transitivity:** If X → Y and Y → Z, then X → Z

### **Q8: How do you find closure of functional dependencies?**
**A:** Use Armstrong's axioms repeatedly:
1. Start with given FD set
2. Apply reflexivity, augmentation, transitivity
3. Continue until no new FDs can be derived
4. Result is closure F+

---

## 📊 Normalization Problems Questions

### **Q9: What are update anomalies? Give example.**
**A:** **Update anomalies** occur when same data is stored multiple times:
- **Problem:** Changing one instance requires updating all copies
- **Example:** In unnormalized table, changing instructor name requires updating every student record for that course
- **Risk:** Inconsistent data if some updates are missed

### **Q10: Explain insert anomalies with example.**
**A:** **Insert anomalies** occur when you cannot add certain information independently:
- **Problem:** Cannot insert one type of data without unrelated data
- **Example:** Cannot add new course information without enrolling at least one student
- **Solution:** Separate tables for independent entities

### **Q11: What are delete anomalies?**
**A:** **Delete anomalies** occur when deleting a record causes loss of other important information:
- **Problem:** Unintended information loss
- **Example:** Deleting last student enrollment also deletes course information
- **Solution:** Separate course information from enrollment data

### **Q12: Why is normalization important?**
**A:** Normalization is important because it:
- **Eliminates redundancy** and saves storage space
- **Prevents update, insert, delete anomalies**
- **Ensures data consistency** across database
- **Simplifies maintenance** and reduces errors
- **Enforces business rules** through structure

---

## 🎯 Normal Forms Questions

### **Q13: What is First Normal Form (1NF)?**
**A:** **1NF requirements:**
- **Atomic values** - No multi-valued attributes
- **No repeating groups** - Each cell contains single value
- **Primary key defined** - Unique identifier exists
- **Example:** Convert "Courses: DB,Networks,OS" to separate rows

### **Q14: Explain Second Normal Form (2NF) with example.**
**A:** **2NF requirements:**
- Must be in **1NF first**
- **No partial dependencies** on primary key
- Every non-key attribute **fully depends** on primary key

**Example:**
```sql
Before 2NF: {Student_ID, Course_ID} → Student_Name (partial)
After 2NF: Student_ID → Student_Name (separate table)
```

### **Q15: What is Third Normal Form (3NF)?**
**A:** **3NF requirements:**
- Must be in **2NF first**
- **No transitive dependencies** exist
- Non-key attributes depend **directly** on primary key only

**Example:**
```sql
Before 3NF: Student_ID → Dept_ID → Dept_Head (transitive)
After 3NF: Separate DEPARTMENT table with Dept_ID → Dept_Head
```

### **Q16: When is a table in BCNF?**
**A:** **BCNF requirements:**
- Must be in **3NF first**
- **Every determinant must be candidate key**
- Eliminates anomalies that 3NF might miss
- **Stricter than 3NF** - resolves remaining dependencies

### **Q17: Can a table be in 3NF but not BCNF? Give example.**
**A:** **Yes, possible when determinant is not candidate key:**
```sql
COURSE_INSTRUCTOR(Course_ID, Instructor_ID, Room)
Primary Key: {Course_ID, Instructor_ID}
FD: Instructor_ID → Room (violates BCNF)
Solution: Separate INSTRUCTOR_ROOM table
```

### **Q18: What is the difference between 3NF and BCNF?**
**A:**
- **3NF:** No transitive dependencies, allows some anomalies
- **BCNF:** Every determinant is candidate key, eliminates all anomalies
- **BCNF is stricter** than 3NF
- **BCNF always achieves better design** but may not preserve dependencies

---

## 🚀 Normalization Process Questions

### **Q19: What is the step-by-step normalization process?**
**A:** **Normalization steps:**
1. **Start with unnormalized relation**
2. **Apply 1NF:** Remove repeating groups, ensure atomic values
3. **Apply 2NF:** Remove partial dependencies
4. **Apply 3NF:** Remove transitive dependencies
5. **Apply BCNF:** Make all determinants candidate keys

### **Q20: How do you normalize a table to 2NF?**
**A:** **2NF Conversion steps:**
1. **Identify partial dependencies** (non-key attributes depending on part of composite key)
2. **Create separate table** for each partial dependency
3. **Remove partially dependent attributes** from original table
4. **Establish foreign key relationships**

### **Q21: Give example of converting 1NF to 2NF.**
**A:** **Example conversion:**
```sql
1NF Table: ENROLLMENT(Student_ID, Course_ID, Student_Name, Grade)
Partial FD: Student_ID → Student_Name

2NF Result:
STUDENT(Student_ID, Student_Name)
ENROLLMENT(Student_ID, Course_ID, Grade)
```

---

## 🎯 Design Improvement Questions

### **Q22: What is lossless join decomposition?**
**A:** **Lossless join decomposition:**
- **Definition:** Decomposition where original relation can be recreated by natural join
- **Property:** No information is lost during decomposition
- **Test:** R1 ⋈ R2 = R (original relation)
- **Importance:** Essential for maintaining data integrity

### **Q23: What is dependency preservation?**
**A:** **Dependency preservation:**
- **Definition:** All functional dependencies can be checked within individual decomposed relations
- **Benefit:** No need to join tables to verify constraints
- **Example:** If Student_ID → Student_Name exists, it should be checkable in STUDENT table alone

### **Q24: When should you consider denormalization?**
**A:** **Denormalization scenarios:**
- **Performance critical** applications
- **Read-heavy workloads** (more queries than updates)
- **Data warehousing** for analytical processing
- **Reporting systems** requiring fast aggregations
- **Trade-off:** Controlled redundancy for better performance

### **Q25: What are the disadvantages of over-normalization?**
**A:** **Over-normalization problems:**
- **Too many joins** required for simple queries
- **Reduced query performance** due to complex joins
- **Increased complexity** in application development
- **Higher storage overhead** due to foreign keys
- **Solution:** Balance normalization with performance needs

---

## 🔥 Advanced Questions

### **Q26: What is multivalued dependency?**
**A:** **Multivalued dependency (MVD):**
- **Definition:** X →→ Y means X determines set of Y values independently
- **Example:** Employee →→ Skills (employee can have multiple skills)
- **Related to 4NF:** Eliminates multivalued dependencies
- **Notation:** Double arrow (→→) distinguishes from functional dependency

### **Q27: How do you handle many-to-many relationships in normalization?**
**A:** **M:N relationship handling:**
1. **Create junction/bridge table** with foreign keys from both entities
2. **Composite primary key** from both foreign keys
3. **Additional attributes** specific to relationship go in junction table
4. **Example:** STUDENT_COURSE(Student_ID, Course_ID, Grade, Semester)

### **Q28: What is the difference between candidate key and primary key?**
**A:**
- **Candidate Key:** Any minimal set of attributes that uniquely identifies tuple
- **Primary Key:** One chosen candidate key for the relation
- **Properties:** Both are unique and minimal
- **Difference:** Multiple candidate keys possible, only one primary key

### **Q29: Can functional dependencies exist without normalization?**
**A:** **Yes, FDs exist independently:**
- **FDs are inherent** in data relationships
- **Normalization uses FDs** to organize data better
- **FDs exist in unnormalized** tables too
- **Purpose:** Normalization eliminates problematic FDs (partial, transitive)

### **Q30: What is the role of foreign keys in normalization?**
**A:** **Foreign keys in normalization:**
- **Maintain relationships** after decomposition
- **Ensure referential integrity** between tables
- **Link decomposed tables** back to original structure
- **Enable lossless joins** through proper connections
- **Enforce business rules** through constraints

---

## 💡 Quick Tips for Viva

### **Key Points to Remember:**
1. **Always give examples** with your definitions
2. **Draw tables** to illustrate concepts
3. **Mention benefits and problems** for each topic
4. **Connect concepts** - how FDs lead to normalization
5. **Real-world applications** - why companies use normalization

### **Common Follow-up Questions:**
- "Give a real-world example"
- "What problems does this solve?"
- "How would you implement this?"
- "What are the trade-offs?"
- "When would you not use this approach?"

### **Confidence Boosters:**
- **Practice drawing ER diagrams** quickly
- **Memorize normal form definitions**
- **Know Armstrong's axioms** by heart
- **Be ready with examples** for each concept
- **Understand practical applications**
