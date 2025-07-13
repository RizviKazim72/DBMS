# Module 6: Transaction Management - Quick Revision

## 🔄 Transaction Concept (5-6 marks)

### **Definition:** Logical unit of database work
### **Operations:** Read(X), Write(X), Commit, Abort
### **Example:**
```sql
BEGIN TRANSACTION;
READ(A); A = A - 500; WRITE(A);
READ(B); B = B + 500; WRITE(B);
COMMIT;
```

---

## 📊 Transaction States (6-7 marks)

### **State Flow:**
```
ACTIVE → PARTIALLY_COMMITTED → COMMITTED
  ↓              ↓
FAILED ────→ ABORTED
```

### **Quick Definitions:**
- **ACTIVE:** Transaction executing
- **PARTIALLY_COMMITTED:** Last operation done, waiting for commit
- **COMMITTED:** Changes permanent
- **FAILED:** Error occurred
- **ABORTED:** Transaction cancelled, changes undone

---

## 🎯 ACID Properties (8-10 marks) ⭐ VERY IMPORTANT

### **A - Atomicity**
- ✅ **All or Nothing** principle
- ✅ **No partial execution**
- **Example:** Bank transfer completes fully or not at all

### **C - Consistency**  
- ✅ **Valid state always**
- ✅ **Constraints preserved**
- **Example:** Account balance ≥ 0 always maintained

### **I - Isolation**
- ✅ **No interference** between concurrent transactions
- ✅ **Intermediate results hidden**
- **Levels:** Read Uncommitted < Read Committed < Repeatable Read < Serializable

### **D - Durability**
- ✅ **Committed changes survive** system crashes
- ✅ **Non-volatile storage** required
- **Implementation:** Write-ahead logging (WAL)

---

## 🎮 Transaction Control Commands (5-6 marks)

### **Basic Commands:**
```sql
BEGIN TRANSACTION;    -- Start transaction
COMMIT;              -- Save changes permanently  
ROLLBACK;            -- Undo all changes
SAVEPOINT sp1;       -- Create checkpoint
ROLLBACK TO sp1;     -- Partial rollback
```

---

## 🚦 Concurrency Problems (7-8 marks)

### **4 Main Problems:**

#### **1. Dirty Read**
```
T1: UPDATE A = 500; (uncommitted)
T2: READ A = 500;   (reads dirty data)
T1: ROLLBACK;       (T2 saw wrong value!)
```

#### **2. Lost Update**
```
T1: READ A = 100;
T2: READ A = 100;
T1: A = 150; WRITE A;
T2: A = 130; WRITE A;  (T1's update lost!)
```

#### **3. Unrepeatable Read**
```
T1: READ A = 100;
T2: UPDATE A = 200; COMMIT;
T1: READ A = 200;  (different value!)
```

#### **4. Phantom Read**
```
T1: COUNT(*) = 5;
T2: INSERT new row; COMMIT;
T1: COUNT(*) = 6;  (phantom row!)
```

---

## 🔀 Serializability (8-9 marks) ⭐ IMPORTANT

### **Conflict Serializability:**
- **Step 1:** Find conflicting operations (R-W, W-R, W-W)
- **Step 2:** Create precedence graph
- **Step 3:** Check for cycles
- **Result:** No cycles = Conflict serializable

### **Conflict Types:**
- **Read-Write (R-W):** T1 reads, T2 writes same item
- **Write-Read (W-R):** T1 writes, T2 reads same item  
- **Write-Write (W-W):** Both write same item

### **Quick Test:**
```
Schedule: R1(A), W2(A), R2(B), W1(B)
Conflicts: R1(A)→W2(A), R2(B)→W1(B)
Graph: T1→T2→T1 (Cycle!)
Result: NOT conflict serializable
```

---

## 🔒 Lock-based Protocols (10-12 marks) ⭐ HIGH IMPORTANCE

### **Lock Types:**
- **Shared (S):** Multiple transactions can read
- **Exclusive (X):** Only one transaction can access

### **Compatibility Matrix:**
```
       S   X
   S   ✓   ✗
   X   ✗   ✗
```

### **Two-Phase Locking (2PL):**
- **Growing Phase:** Acquire locks only
- **Shrinking Phase:** Release locks only
- **Rule:** Once lock released, cannot acquire new locks

### **2PL Variations:**
- **Basic 2PL:** Deadlocks possible
- **Conservative 2PL:** Acquire all locks before start
- **Strict 2PL:** Hold X-locks until commit/abort
- **Rigorous 2PL:** Hold all locks until commit/abort

---

## ⚰️ Deadlock Handling (8-10 marks)

### **Deadlock Definition:** Circular wait condition
```
T1: LOCK-X(A) → wait for B
T2: LOCK-X(B) → wait for A
Deadlock! 🔄
```

### **Handling Methods:**

#### **1. Prevention:**
- **Wait-Die:** Older waits, younger dies
- **Wound-Wait:** Older wounds younger, younger waits

#### **2. Detection:**
- **Wait-for graph** method
- **Find cycles** in graph
- **Abort victim** transaction

#### **3. Avoidance:**
- **Banker's algorithm**
- **Check safe state** before granting locks

---

## ⏰ Timestamp Protocols (6-7 marks)

### **Basic Rules:**
- **Each transaction** gets unique timestamp
- **Read rule:** TS(Ti) ≥ W-TS(X) → Allow
- **Write rule:** TS(Ti) ≥ R-TS(X) AND TS(Ti) ≥ W-TS(X) → Allow

### **Benefits:**
- ✅ **No deadlocks**
- ✅ **No waiting**
- ✅ **Guaranteed serializability**

### **Drawbacks:**
- ❌ **High abort rate**
- ❌ **Starvation possible**

---

## 🔧 Recovery System (12-15 marks) ⭐ VERY IMPORTANT

### **Failure Types:**
1. **Transaction failure:** Logic errors, constraint violations
2. **System crash:** Power failure, OS crash
3. **Disk failure:** Hardware malfunction

### **Log-based Recovery:**

#### **Log Record Types:**
```
<Ti, start>           -- Transaction started
<Ti, X, V1, V2>      -- Updated X from V1 to V2  
<Ti, commit>         -- Transaction committed
<Ti, abort>          -- Transaction aborted
<checkpoint L>       -- Checkpoint with active list L
```

#### **WAL Protocol:**
1. **Log record to stable storage** before data
2. **Commit only after** commit record in log
3. **Ensures atomicity** and durability

#### **Recovery Algorithm:**
1. **Analysis Phase:** Scan log, find active transactions
2. **Redo Phase:** Forward scan, redo committed transactions
3. **Undo Phase:** Backward scan, undo uncommitted transactions

---

## 🎯 Exam Strategy

### **High-Probability Questions:**
1. **Explain ACID properties** (8-10 marks) ⭐
2. **Test schedule for serializability** (8-10 marks) ⭐
3. **Compare deadlock handling methods** (6-8 marks)
4. **Design recovery algorithm** (10-12 marks) ⭐
5. **2PL protocol variations** (6-8 marks)

### **Time Management:**
- **ACID explanation:** 12-15 minutes
- **Serializability test:** 15-20 minutes
- **Recovery algorithm:** 20-25 minutes
- **Deadlock methods:** 10-12 minutes

### **Common Mistakes to Avoid:**
- ❌ **Confusing conflict types**
- ❌ **Wrong precedence graph**
- ❌ **Missing WAL protocol steps**
- ❌ **Incorrect 2PL phase identification**

---

## 🔥 Quick Formulas & Patterns

### **Conflict Check:**
```
Same data item + Different transactions + At least one write = CONFLICT
```

### **2PL Verification:**
```
Growing phase: Only acquire locks
Shrinking phase: Only release locks
Lock point: Maximum locks held
```

### **Recovery Steps:**
```
Analysis → Redo → Undo
Forward scan → Forward scan → Backward scan
Active transactions → Committed work → Uncommitted work
```

---

## 💡 Memory Tricks

### **ACID:**
- **A**ll or nothing (Atomicity)
- **C**onsistent state (Consistency)  
- **I**solated execution (Isolation)
- **D**urable storage (Durability)

### **Transaction States:**
- **A**ctive → **P**artially committed → **C**ommitted
- **F**ailed → **A**borted

### **Lock Compatibility:**
- **S**hared with **S**hared = ✓
- **X**clusive with anything = ✗

### **Recovery Phases:**
- **A**nalysis **R**edo **U**ndo = **ARU**

---

## ⚡ Last-Minute Review

### **Must Remember:**
1. **ACID properties** with examples
2. **Transaction state transitions**
3. **Serializability testing method**
4. **2PL protocol rules**
5. **Deadlock handling approaches**
6. **WAL protocol steps**
7. **Recovery algorithm phases**

### **Self-learning Integration:**
- **Module 5 deadlock scenarios:** Order processing, inventory updates, payment conflicts
- **Design normalized tables** with proper locking order
- **Prevent deadlocks** through table access sequence

### **Total Weightage:** 20-25 marks (Highest!)

### **Success Mantra:**
**Master serializability testing and recovery algorithms!**
**Practice precedence graphs and log-based recovery daily!**
