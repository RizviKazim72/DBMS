# Module 2 - ER Model Viva Preparation 🎤

## Basic Questions (Warm-up) 🌟

**Q1: What is ER Model?**
**A:** Entity-Relationship Model is a conceptual data model that describes database structure using entities, attributes, and relationships between entities.

**Q2: What is the difference between Entity and Entity Set?**
**A:** Entity is a single instance (like student "John"), while Entity Set is collection of all entities of same type (like all students in database).

**Q3: What are the main components of ER Model?**
**A:** Three main components:
- **Entities** (real-world objects)
- **Attributes** (properties of entities)  
- **Relationships** (associations between entities)

---

## Attribute-Based Questions 🏷️

**Q4: What are different types of attributes?**
**A:** Four types:
- **Simple:** Cannot be divided (Age, Gender)
- **Composite:** Can be divided into parts (Address = Street + City)
- **Multi-valued:** Multiple values possible (Phone numbers)
- **Derived:** Calculated from other attributes (Age from DOB)

**Q5: Give example of composite attribute?**
**A:** Address attribute can be divided into:
- Street, City, State, PIN Code
- Another example: Full_Name = First_Name + Last_Name

**Q6: What is derived attribute? Give example.**
**A:** Attribute calculated from other stored attributes. 
**Example:** Age derived from Date_of_Birth, Total_Marks from individual subject marks.

---

## Key-Related Questions 🔑

**Q7: What are different types of keys?**
**A:** Main types:
- **Primary Key:** Uniquely identifies entity (Student_ID)
- **Candidate Key:** Potential primary keys (Email, Phone)
- **Super Key:** Primary key + additional attributes
- **Partial Key:** Used in weak entities (Item_Number in Order_Item)

**Q8: How do you choose Primary Key?**
**A:** Primary Key should be:
- **Unique** for each entity
- **Non-null** (cannot be empty)
- **Minimal** (smallest set of attributes)
- **Stable** (doesn't change frequently)

**Q9: Can we have multiple Primary Keys?**
**A:** No, only **one Primary Key** per entity. But we can have multiple **Candidate Keys** which are potential primary keys.

---

## Relationship Questions 🔗

**Q10: What are different types of relationships by cardinality?**
**A:** Three types:
- **1:1 (One-to-One):** Person has Passport
- **1:M (One-to-Many):** Department has Employees  
- **M:N (Many-to-Many):** Students enroll in Courses

**Q11: How do you implement M:N relationship in database?**
**A:** Create a **junction/bridge table** with foreign keys from both entities.
**Example:** Student_Course table with Student_ID + Course_ID

**Q12: What is participation constraint?**
**A:** Defines whether entity participation is mandatory or optional:
- **Total Participation:** Every entity must participate (double line)
- **Partial Participation:** Some entities may not participate (single line)

---

## Weak Entity Questions 👶

**Q13: What is weak entity? Give example.**
**A:** Entity that cannot exist independently and depends on strong entity.
**Example:** Order_Item depends on Order. If Order is deleted, Order_Items are automatically removed.

**Q14: How do you identify weak entity?**
**A:** Characteristics of weak entity:
- **Cannot exist** without strong entity
- **No primary key** of its own
- **Partial key** + Strong entity key = Composite primary key
- Connected via **identifying relationship**

**Q15: What is identifying relationship?**
**A:** Special relationship that connects weak entity with its owner (strong entity). Represented by double diamond in ER diagram.

---

## Advanced Concepts 🚀

**Q16: Difference between Generalization and Specialization?**
**A:** 
- **Generalization:** Bottom-up approach, combining similar entities into superclass
- **Specialization:** Top-down approach, dividing general entity into subclasses
**Example:** Car+Bike → Vehicle (Generalization), Employee → Manager+Developer (Specialization)

**Q17: What is aggregation in ER Model?**
**A:** Treating a relationship as an entity to participate in other relationships.
**Example:** Student-Course enrollment relationship treated as entity to assign Instructor.

**Q18: What is EER Model?**
**A:** Extended Entity-Relationship Model adds features like:
- **Inheritance** (subclass inherits from superclass)
- **Category** (union types)
- **Enhanced constraints** (disjoint/overlap, total/partial)

---

## Design-Based Questions 🛠️

**Q19: How do you design ER diagram for Library Management System?**
**A:** Key entities and relationships:
- **Entities:** Book, Member, Author, Category
- **Relationships:** 
  - Member **borrows** Book (1:M)
  - Author **writes** Book (1:M) 
  - Book **belongs to** Category (M:1)

**Q20: Design ER for Hospital Management System.**
**A:** Core components:
- **Entities:** Patient, Doctor, Appointment, Department
- **Relationships:**
  - Patient **schedules** Appointment (1:M)
  - Doctor **attends** Appointment (1:M)
  - Doctor **works in** Department (M:1)

**Q21: How would you handle employee hierarchy in ER diagram?**
**A:** Use **recursive relationship**:
- Employee **supervises** Employee (1:M)
- Or use **specialization**: Employee → {Manager, Developer, Analyst}

---

## Practical Scenarios 🎯

**Q22: A student can take multiple courses, and each course can have multiple students. How do you model this?**
**A:** **Many-to-Many relationship** between Student and Course entities. Implement using junction table Student_Course with attributes like Grade, Enrollment_Date.

**Q23: In e-commerce, how do you model orders and products?**
**A:** 
- **Customer** places **Order** (1:M)
- **Order** contains **Product** (M:N) - needs Order_Item junction table
- Order_Item has attributes: Quantity, Unit_Price

**Q24: How do you handle address that can be for both customers and suppliers?**
**A:** Two approaches:
1. **Separate address attributes** in Customer and Supplier entities
2. **Address as separate entity** with relationships to both Customer and Supplier

---

## Problem-Solving Questions 🔧

**Q25: Your ER diagram has too many M:N relationships. What's the issue?**
**A:** Possible problems:
- **Under-designed** - missing intermediate entities
- **Need junction tables** for implementation
- **Consider breaking down** complex relationships
- **Add attributes** to relationship (might need aggregation)

**Q26: How do you decide between making something an entity or attribute?**
**A:** Make it **entity** if:
- Has **multiple properties** of its own
- **Participates in relationships** with other entities
- **Exists independently** in business context
Make it **attribute** if it's a simple property describing an entity.

**Q27: When do you use composite vs multi-valued attributes?**
**A:**
- **Composite:** When attribute has logical parts (Address = Street + City)
- **Multi-valued:** When multiple values of same type exist (Multiple phone numbers)

---

## Quick Fire Round ⚡

**Q28: ER diagram symbol for entity?**
**A:** Rectangle

**Q29: ER diagram symbol for relationship?**
**A:** Diamond

**Q30: ER diagram symbol for attribute?**
**A:** Ellipse

**Q31: How to show primary key in ER diagram?**
**A:** Underline the attribute

**Q32: Symbol for weak entity?**
**A:** Double rectangle

**Q33: Symbol for derived attribute?**
**A:** Dashed ellipse

**Q34: What does double diamond represent?**
**A:** Identifying relationship (for weak entities)

**Q35: Full form of EER?**
**A:** Extended Entity-Relationship

---

## Pro Tips for Viva Success 💡

### **Preparation Strategy:**
1. **Practice drawing** ER diagrams quickly
2. **Memorize symbols** and their meanings
3. **Prepare real examples** for each concept
4. **Practice explaining** design decisions

### **During Viva:**
1. **Think aloud** while drawing diagrams
2. **Explain your reasoning** for design choices
3. **Start with simple** then add complexity
4. **Ask clarifying questions** if scenario unclear

### **Common Mistakes to Avoid:**
- Don't confuse entity with attribute
- Don't forget to specify cardinalities
- Don't make everything an entity
- Don't ignore weak entity dependencies

### **Golden Rules:**
- **Entity = Noun, Relationship = Verb**
- **Draw clean, labeled diagrams**
- **Justify every design decision**
- **Think from user perspective**

**Practice Scenarios:** Library, Hospital, E-commerce, Banking, University

**Remember:** **Clear thinking** + **Good examples** + **Clean diagrams** = **Excellent Viva!** 🌟
