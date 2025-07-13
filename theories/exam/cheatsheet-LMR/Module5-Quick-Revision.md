# Module 5: Database Design - Quick Revision

## 🎯 Design Guidelines (5-6 marks)

### **4 Key Principles:**
1. **Clear Semantics** - One concept per relation
2. **Minimal Redundancy** - Avoid duplicate data  
3. **Minimal NULLs** - Reduce empty values
4. **No Spurious Tuples** - Meaningful joins only

### **Quality Measures:**
- **Storage Efficiency** (space optimization)
- **Update Efficiency** (minimal operations)
- **Query Performance** (fast retrieval)

---

## 🔗 Functional Dependencies (7-8 marks)

### **Definition:** X → Y means X determines Y

### **Types:**
- **Full FD:** Y depends on complete X
- **Partial FD:** Y depends on subset of X  
- **Transitive FD:** X → Y → Z implies X → Z

### **Armstrong's Axioms:**
1. **Reflexivity:** Y ⊆ X ⟹ X → Y
2. **Augmentation:** X → Y ⟹ XZ → YZ
3. **Transitivity:** X → Y, Y → Z ⟹ X → Z

---

## 📊 Normalization Problems (6-7 marks)

### **Update Anomalies:**
- Same data in multiple places
- Multiple updates required
- **Example:** Change instructor name everywhere

### **Insert Anomalies:**
- Cannot add data without unrelated info
- **Example:** Cannot add course without student

### **Delete Anomalies:**  
- Lose important info when deleting
- **Example:** Delete last student loses course info

### **Storage Redundancy:**
- Repeated data wastes space
- **Example:** Student name in every enrollment

---

## 🎯 Normal Forms (12-15 marks) ⭐ VERY IMPORTANT

### **1NF Rules:**
- ✅ **Atomic values** only
- ✅ **No repeating groups**
- ✅ **Primary key defined**

**Example:**
```
Before: Courses = "DB, Networks, OS"
After:  Separate row for each course
```

### **2NF Rules:**
- ✅ **Must be in 1NF**
- ✅ **No partial dependencies**
- ✅ **Full dependency on primary key**

**Problem:** Student_ID → Student_Name (partial dependency)
**Solution:** Create separate STUDENT table

### **3NF Rules:**
- ✅ **Must be in 2NF**  
- ✅ **No transitive dependencies**
- ✅ **Direct dependencies only**

**Problem:** Student_ID → Dept_ID → Dept_Head (transitive)
**Solution:** Create separate DEPARTMENT table

### **BCNF Rules:**
- ✅ **Must be in 3NF**
- ✅ **Every determinant is candidate key**
- ✅ **Eliminates remaining anomalies**

**Problem:** Instructor_ID → Room (determinant not key)
**Solution:** Separate INSTRUCTOR_ROOM table

---

## 🚀 Quick Conversion Process

### **Normalization Steps:**
```
Unnormalized → 1NF → 2NF → 3NF → BCNF
```

### **Step-by-Step:**
1. **1NF:** Remove repeating groups
2. **2NF:** Remove partial dependencies  
3. **3NF:** Remove transitive dependencies
4. **BCNF:** Make all determinants keys

---

## 🎯 Design Improvement (6-7 marks)

### **Good Decomposition:**
- ✅ **Lossless join** (no info lost)
- ✅ **Dependency preservation** (rules maintained)
- ✅ **Achieves normal form**
- ✅ **Semantic meaning preserved**

### **Poor Decomposition:**
- ❌ **Lossy join** (info lost)
- ❌ **Dependencies not preserved**
- ❌ **Over/under normalization**

---

## 🔥 Exam Strategy

### **High-Probability Questions:**
1. **Normalize table to 3NF/BCNF** (10-12 marks) ⭐
2. **Identify functional dependencies** (5-6 marks)
3. **Explain normalization benefits** (6-7 marks)
4. **Compare normal forms** (5-6 marks)
5. **Design schema for scenario** (8-10 marks)

### **Time Management:**
- **Normalization problem:** 15-20 minutes
- **FD identification:** 8-10 minutes  
- **Concept explanation:** 10-12 minutes
- **Design guidelines:** 8-10 minutes

### **Common Mistakes to Avoid:**
- ❌ **Forgetting primary keys**
- ❌ **Missing transitive dependencies**
- ❌ **Incorrect FD notation**
- ❌ **Incomplete decomposition**

---

## 📝 Key Formulas & Patterns

### **FD Notation:**
```
X → Y    (X determines Y)
A → B, B → C  ⟹  A → C  (transitivity)
{A,B} → C  (composite determinant)
```

### **Normal Form Checklist:**
- **1NF:** Atomic + No repeating groups
- **2NF:** 1NF + No partial dependencies  
- **3NF:** 2NF + No transitive dependencies
- **BCNF:** 3NF + All determinants are keys

### **Quick Recognition:**
- **Partial:** Non-key depends on part of key
- **Transitive:** A → B → C chain exists
- **BCNF violation:** Determinant is not superkey

---

## 💡 Memory Tricks

### **Normal Forms:**
- **1NF:** **A**tomic values
- **2NF:** **P**artial dependencies gone
- **3NF:** **T**ransitive dependencies gone  
- **BCNF:** **B**est form, determinants are keys

### **Design Guidelines:**
- **C**lear semantics
- **M**inimal redundancy
- **M**inimal NULLs
- **N**o spurious tuples

### **Dependencies:**
- **F**ull = **F**ull key needed
- **P**artial = **P**art of key enough
- **T**ransitive = **T**hrough another attribute

---

## ⚡ Last-Minute Review

### **Must Remember:**
1. **4 Design guidelines**
2. **3 Types of FDs**
3. **4 Normal forms + rules**
4. **3 Anomaly types**
5. **Armstrong's 3 axioms**

### **Total Weightage:** 18-20 marks

### **Success Mantra:**
**Practice normalization problems daily!**
**Draw tables, identify FDs, apply rules step-by-step**
