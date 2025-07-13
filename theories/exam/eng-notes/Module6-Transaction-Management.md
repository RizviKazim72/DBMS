# Module 6: Transaction Management, Concurrency and Recovery

## Transaction Concept

### **Definition:**
A **transaction** is a sequence of database operations that forms a **logical unit of work**. It represents a single, indivisible unit of database processing.

### **Characteristics:**
- **Atomic unit** of database processing
- **All operations succeed** or **all fail**
- **Maintains database consistency**
- **Isolated from other transactions**

### **Transaction Operations:**
- **Read(X):** Transfer data item X from database to local buffer
- **Write(X):** Transfer data item X from local buffer to database
- **Commit:** Successfully complete transaction
- **Abort:** Terminate transaction unsuccessfully

### **Example - Bank Transfer:**
```sql
BEGIN TRANSACTION;
    -- Read account balances
    READ(Account_A);
    READ(Account_B);
    
    -- Perform transfer
    Account_A = Account_A - 500;
    Account_B = Account_B + 500;
    
    -- Write updated balances
    WRITE(Account_A);
    WRITE(Account_B);
COMMIT;
```

**Transaction Concept: 5-6 marks**

---

## Transaction State Diagram

### **Transaction States:**

#### **1. Active State**
- **Initial state** when transaction begins execution
- **Operations are being performed**
- **Can proceed to partially committed or failed state**

#### **2. Partially Committed State**
- **Final operation has been executed**
- **Changes are in main memory buffers**
- **Not yet written to disk permanently**

#### **3. Committed State**
- **Transaction has completed successfully**
- **All changes are permanently saved to database**
- **Cannot be rolled back**

#### **4. Failed State**
- **Transaction cannot proceed** due to error
- **System must abort the transaction**
- **Requires rollback of changes**

#### **5. Aborted State**
- **Transaction has been terminated**
- **All changes have been rolled back**
- **Database restored to pre-transaction state**

### **State Transition Diagram:**
```
    ACTIVE
    /    \
   /      \
  ↓        ↓
PARTIALLY  FAILED
COMMITTED    |
   |         |
   ↓         ↓
COMMITTED  ABORTED
```

### **State Transitions:**
- **Active → Partially Committed:** Last operation executed
- **Partially Committed → Committed:** Changes written to disk
- **Active/Partially Committed → Failed:** Error occurs
- **Failed → Aborted:** Rollback completed

**State Diagram: 6-7 marks**

---

## ACID Properties

### **A - Atomicity**
**Definition:** Transaction is treated as **single, indivisible unit**.

**Requirements:**
- **All operations complete** or **none complete**
- **No partial execution** allowed
- **System ensures atomicity** through abort/commit mechanisms

**Implementation:**
- **Log-based recovery** techniques
- **Shadow paging** mechanisms
- **Rollback operations** for failed transactions

### **C - Consistency**
**Definition:** Transaction takes database from **one consistent state to another**.

**Requirements:**
- **Integrity constraints preserved**
- **Business rules maintained**
- **Valid data relationships**

**Examples:**
- **Referential integrity:** Foreign key constraints
- **Domain constraints:** Data type validations
- **Business rules:** Account balance ≥ 0

### **I - Isolation**
**Definition:** **Concurrent transactions do not interfere** with each other.

**Requirements:**
- **Intermediate results hidden** from other transactions
- **Final results visible** only after commit
- **Concurrent execution equivalent** to serial execution

**Isolation Levels:**
1. **Read Uncommitted:** Lowest isolation
2. **Read Committed:** Prevents dirty reads
3. **Repeatable Read:** Prevents dirty and non-repeatable reads
4. **Serializable:** Highest isolation, prevents all anomalies

### **D - Durability**
**Definition:** **Committed changes persist** even after system failures.

**Requirements:**
- **Changes survive system crashes**
- **Data stored in non-volatile storage**
- **Recovery mechanisms available**

**Implementation:**
- **Write-ahead logging (WAL)**
- **Database buffers flushing**
- **Backup and recovery procedures**

**ACID Properties: 8-10 marks (Very Important)**

---

## Transaction Control Commands

### **SQL Transaction Control Statements:**

#### **1. BEGIN TRANSACTION / START TRANSACTION**
```sql
BEGIN TRANSACTION;
-- or
START TRANSACTION;
```
**Purpose:** Explicitly start a new transaction
**Effect:** Marks the beginning of transaction boundary

#### **2. COMMIT**
```sql
COMMIT;
-- or
COMMIT WORK;
```
**Purpose:** Permanently save all changes made in transaction
**Effect:** Transaction reaches committed state

#### **3. ROLLBACK**
```sql
ROLLBACK;
-- or
ROLLBACK WORK;
```
**Purpose:** Undo all changes made in current transaction
**Effect:** Transaction reaches aborted state

#### **4. SAVEPOINT**
```sql
SAVEPOINT savepoint_name;
```
**Purpose:** Create intermediate checkpoint in transaction
**Benefits:** Partial rollback possible

#### **5. ROLLBACK TO SAVEPOINT**
```sql
ROLLBACK TO savepoint_name;
```
**Purpose:** Rollback to specific savepoint
**Effect:** Undo changes after savepoint only

### **Example Usage:**
```sql
BEGIN TRANSACTION;
    
    INSERT INTO Orders VALUES (1, 'Customer1', '2024-01-01');
    SAVEPOINT order_inserted;
    
    INSERT INTO OrderItems VALUES (1, 1, 'Product1', 2);
    SAVEPOINT items_inserted;
    
    -- If this fails, rollback to items_inserted
    UPDATE Inventory SET stock = stock - 2 WHERE product_id = 1;
    
    -- If error occurs
    ROLLBACK TO items_inserted;
    
    -- Or commit all changes
    COMMIT;
```

**Transaction Control: 5-6 marks**

---

## Concurrent Executions

### **Need for Concurrency:**
- **Improved throughput** and resource utilization
- **Reduced response time** for short transactions
- **Better system efficiency**

### **Concurrency Problems:**

#### **1. Dirty Read Problem**
**Definition:** Transaction reads **uncommitted data** from another transaction.

**Example:**
```
T1: UPDATE Account SET balance = 500 WHERE id = 1;  (uncommitted)
T2: SELECT balance FROM Account WHERE id = 1;       (reads 500)
T1: ROLLBACK;                                       (T2 read wrong data)
```

#### **2. Lost Update Problem**
**Definition:** **Updates from one transaction are lost** due to another transaction.

**Example:**
```
T1: READ(X = 100)
T2: READ(X = 100)
T1: X = X + 50; WRITE(X = 150)
T2: X = X + 30; WRITE(X = 130)    (T1's update lost)
```

#### **3. Unrepeatable Read Problem**
**Definition:** Transaction gets **different values** when reading same data multiple times.

**Example:**
```
T1: SELECT balance FROM Account WHERE id = 1;  (reads 1000)
T2: UPDATE Account SET balance = 2000 WHERE id = 1; COMMIT;
T1: SELECT balance FROM Account WHERE id = 1;  (reads 2000)
```

#### **4. Phantom Read Problem**
**Definition:** Transaction gets **different number of rows** in repeated queries.

**Example:**
```
T1: SELECT COUNT(*) FROM Orders WHERE date = '2024-01-01';  (count = 5)
T2: INSERT INTO Orders VALUES (..., '2024-01-01', ...); COMMIT;
T1: SELECT COUNT(*) FROM Orders WHERE date = '2024-01-01';  (count = 6)
```

### **Schedule Types:**
- **Serial Schedule:** Transactions execute one after another
- **Concurrent Schedule:** Operations of multiple transactions interleaved
- **Serializable Schedule:** Concurrent schedule equivalent to some serial schedule

**Concurrent Executions: 7-8 marks**

---

## Serializability

### **Definition:**
A schedule is **serializable** if it is equivalent to some **serial schedule** of the same transactions.

### **Types of Serializability:**

#### **1. Conflict Serializability**

**Conflicting Operations:** Two operations that:
- **Access same data item**
- **At least one is write operation**
- **Belong to different transactions**

**Conflict Types:**
- **Read-Write conflict (R-W)**
- **Write-Read conflict (W-R)**
- **Write-Write conflict (W-W)**

**Testing Conflict Serializability:**
1. **Create precedence graph**
2. **Add edge** from Ti to Tj if Ti has conflicting operation before Tj
3. **If graph is acyclic** → Conflict serializable

**Example:**
```
Schedule S: R1(A), W2(A), R2(B), W1(B)

Conflicts:
R1(A) → W2(A)  (T1 → T2)
W2(A) → W1(B)  (Need to check if affects same data)
R2(B) → W1(B)  (T2 → T1)

Creates cycle: T1 → T2 → T1
Not conflict serializable!
```

#### **2. View Serializability**

**View Equivalent Schedules** must satisfy:
1. **Same initial reads:** If Ti reads initial value of X in S1, then same in S2
2. **Same final writes:** If Ti performs final write of X in S1, then same in S2
3. **Same intermediate reads:** If Ti reads value written by Tj in S1, then same in S2

**Testing View Serializability:**
- **More complex** than conflict serializability
- **NP-complete problem**
- **Includes all conflict serializable schedules**

**View Serializability: 8-9 marks (Important)**

---

## Lock-based Protocols

### **Locking Mechanism:**
**Lock** is a synchronization mechanism to control concurrent access to data items.

### **Lock Types:**

#### **1. Shared Lock (S-lock)**
- **Multiple transactions** can hold shared locks on same item
- **Allows concurrent reading**
- **Prevents writing** while held

#### **2. Exclusive Lock (X-lock)**
- **Only one transaction** can hold exclusive lock
- **Prevents both reading and writing** by others
- **Required for write operations**

### **Lock Compatibility Matrix:**
```
Requested Lock
         S    X
Held  S  ✓    ✗
Lock  X  ✗    ✗
```

### **Two-Phase Locking (2PL) Protocol:**

#### **Phases:**
1. **Growing Phase:** Transaction can **acquire locks** but cannot release
2. **Shrinking Phase:** Transaction can **release locks** but cannot acquire

#### **Rules:**
- **Lock point:** Point where transaction holds maximum locks
- **Once lock is released,** no new locks can be acquired
- **Guarantees conflict serializability**

### **Variations of 2PL:**

#### **1. Basic 2PL**
- **Deadlocks possible**
- **Cascading rollbacks possible**

#### **2. Conservative 2PL (Static)**
- **Acquire all locks** before transaction begins
- **Prevents deadlocks**
- **Reduces concurrency**

#### **3. Strict 2PL**
- **Hold exclusive locks** until commit/abort
- **Prevents cascading rollbacks**
- **Most commonly used**

#### **4. Rigorous 2PL**
- **Hold all locks** until commit/abort
- **Simplifies recovery**
- **Reduces concurrency further**

**Lock-based Protocols: 10-12 marks (High importance)**

---

## Deadlock Handling

### **Deadlock Definition:**
**Two or more transactions** waiting for each other to release locks, creating a **circular wait condition**.

### **Deadlock Example:**
```
T1: LOCK-X(A); ... wait for LOCK-X(B)
T2: LOCK-X(B); ... wait for LOCK-X(A)

Both transactions wait indefinitely!
```

### **Deadlock Handling Strategies:**

#### **1. Deadlock Prevention**

**Wait-Die Scheme:**
- **Older transaction waits** for younger
- **Younger transaction dies** (aborts) if requests lock held by older
- **Non-preemptive**

**Wound-Wait Scheme:**
- **Older transaction wounds** (aborts) younger
- **Younger transaction waits** for older
- **Preemptive**

#### **2. Deadlock Detection**

**Wait-for Graph Method:**
1. **Create wait-for graph**
2. **Nodes represent transactions**
3. **Edge Ti → Tj** means Ti waits for Tj
4. **Cycle in graph** indicates deadlock

**Detection Algorithm:**
- **Periodically check** for cycles
- **When deadlock detected,** select victim
- **Abort victim transaction**

**Victim Selection Criteria:**
- **Minimum cost** to abort
- **Least work done**
- **Fewest locks held**

#### **3. Deadlock Avoidance**

**Banker's Algorithm for Databases:**
- **Check if request** leads to safe state
- **Grant lock only** if safe state maintained
- **Requires advance knowledge** of resource requirements

### **Timeout-based Detection:**
- **Set timeout period** for lock waits
- **Abort transaction** if timeout exceeded
- **Simple but may abort** non-deadlocked transactions

**Deadlock Handling: 8-10 marks**

---

## Timestamp-based Protocols

### **Timestamp Ordering:**
Each transaction assigned **unique timestamp** representing its start time.

### **Timestamp Assignment:**
- **System clock value**
- **Logical counter value**
- **Hybrid approaches**

### **Data Item Timestamps:**
- **Read timestamp (R-TS):** Largest timestamp of successfully read
- **Write timestamp (W-TS):** Largest timestamp of successfully written

### **Basic Timestamp Protocol Rules:**

#### **Read Operation:**
```
If TS(Ti) < W-TS(X):
    REJECT and ABORT Ti
Else:
    EXECUTE read
    R-TS(X) = max(R-TS(X), TS(Ti))
```

#### **Write Operation:**
```
If TS(Ti) < R-TS(X) OR TS(Ti) < W-TS(X):
    REJECT and ABORT Ti
Else:
    EXECUTE write
    W-TS(X) = TS(Ti)
```

### **Thomas' Write Rule:**
**Optimization** for write operations:
```
If TS(Ti) < W-TS(X):
    IGNORE write (but don't abort)
```

### **Advantages:**
- **No deadlocks** possible
- **No waiting** required
- **Serializable schedules** guaranteed

### **Disadvantages:**
- **High abort rate** possible
- **Starvation** of long transactions
- **Overhead** of timestamp management

**Timestamp Protocols: 6-7 marks**

---

## Recovery System

### **Types of Failures:**

#### **1. Transaction Failure**
- **Logical errors:** Incorrect input, constraint violations
- **System errors:** Deadlock detection, resource unavailability

#### **2. System Crash**
- **Power failure**
- **Operating system crash**
- **Database system software failure**

#### **3. Disk Failure**
- **Head crash**
- **Disk corruption**
- **Controller failure**

### **Recovery Concepts:**

#### **Volatile Storage**
- **Main memory, cache**
- **Lost on system crash**
- **Fast access**

#### **Non-volatile Storage**
- **Disk storage**
- **Survives system crash**
- **Slower access**

#### **Stable Storage**
- **Theoretical concept**
- **Never lost**
- **Implemented through replication**

**Recovery Concepts: 5-6 marks**

---

## Log-based Recovery

### **Transaction Log:**
**Sequential file** recording all database operations for recovery purposes.

### **Log Record Types:**

#### **1. Transaction Records**
```
<Ti, start>          - Transaction Ti started
<Ti, X, V1, V2>      - Ti updated X from V1 to V2
<Ti, commit>         - Transaction Ti committed
<Ti, abort>          - Transaction Ti aborted
```

#### **2. Checkpoint Records**
```
<checkpoint L>       - L is list of active transactions
```

### **Write-Ahead Logging (WAL) Protocol:**
1. **Log record must reach stable storage** before corresponding data
2. **Transaction commits** only after commit log record in stable storage
3. **Ensures atomicity and durability**

### **Recovery Algorithm:**

#### **Undo Operation:**
```
For each log record <Ti, X, V1, V2> in reverse order:
    If Ti is in undo-list:
        X = V1  (restore old value)
        Write <Ti, X, V1> to log
```

#### **Redo Operation:**
```
For each log record <Ti, X, V1, V2> in forward order:
    If Ti is in redo-list:
        X = V2  (restore new value)
        Write <Ti, X, V2> to log
```

### **Recovery Phases:**

#### **1. Analysis Phase**
- **Scan log forward** from last checkpoint
- **Determine active transactions** at crash
- **Build undo and redo lists**

#### **2. Redo Phase**
- **Scan log forward** from oldest update
- **Redo all operations** of committed transactions
- **Ensure durability**

#### **3. Undo Phase**
- **Scan log backward**
- **Undo operations** of uncommitted transactions
- **Ensure atomicity**

### **Checkpoint Technique:**
**Periodic operation** to reduce recovery time:
1. **Write all modified buffers** to disk
2. **Write checkpoint record** to log
3. **Flush log to stable storage**

**Log-based Recovery: 12-15 marks (Very Important)**

---

## Important Points for Exams

### **Must Remember:**
1. **ACID properties** and their significance
2. **Transaction state diagram** and transitions
3. **Concurrency problems** and their solutions
4. **Serializability types** and testing methods
5. **2PL protocol** and its variations
6. **Deadlock handling** strategies
7. **Recovery algorithms** and log-based techniques

### **Common Exam Questions:**
- **Explain ACID properties** with examples **(8-10 marks)**
- **Draw transaction state diagram** **(5-6 marks)**
- **Test schedule for serializability** **(8-10 marks)**
- **Compare deadlock handling methods** **(6-8 marks)**
- **Design recovery algorithm** **(10-12 marks)**

### **Key Terms:**
- **Transaction, ACID, Serializability**
- **Lock, Deadlock, Timestamp**
- **Recovery, Log, Checkpoint**
- **Concurrency, Isolation levels**
- **2PL, Wait-for graph, WAL**

**Total Module Weightage: 20-25 marks (Highest weightage)**
