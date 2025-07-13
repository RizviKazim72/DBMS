# Module 6: Transaction Management - Viva Questions & Answers

## 🔄 Transaction Concept Questions

### **Q1: What is a transaction in database systems?**
**A:** A **transaction** is a logical unit of database processing that consists of one or more database operations. Key characteristics:
- **Atomic unit** - All operations succeed or all fail
- **Consistent** - Maintains database integrity
- **Isolated** - Independent of other transactions
- **Durable** - Committed changes are permanent

### **Q2: What are the basic operations in a transaction?**
**A:** The basic operations are:
- **Read(X):** Transfer data item X from database to local buffer
- **Write(X):** Transfer data item X from local buffer to database
- **Commit:** Successfully complete the transaction
- **Abort:** Terminate the transaction unsuccessfully

### **Q3: Why are transactions important in database systems?**
**A:** Transactions are important because they:
- **Ensure data consistency** during concurrent access
- **Provide recovery mechanisms** from system failures
- **Maintain data integrity** through ACID properties
- **Enable reliable multi-user** database operations
- **Support business logic** as atomic units

---

## 📊 Transaction State Questions

### **Q4: Draw and explain the transaction state diagram.**
**A:** The transaction state diagram shows five states:
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

- **ACTIVE:** Transaction is executing
- **PARTIALLY_COMMITTED:** Final operation executed, waiting for commit
- **COMMITTED:** Transaction completed successfully, changes permanent
- **FAILED:** Error occurred, transaction must be aborted
- **ABORTED:** Transaction terminated, all changes rolled back

### **Q5: What is the difference between partially committed and committed states?**
**A:** 
- **Partially Committed:** All operations completed but **changes still in memory buffers**, not yet written to disk permanently
- **Committed:** Changes have been **written to stable storage** and are permanent, transaction successfully completed

### **Q6: Can a transaction move from committed state to aborted state?**
**A:** **No**, once a transaction reaches the **committed state**, it cannot be aborted. Committed changes are **permanent and durable**. The transaction can only move from committed state by starting a new transaction.

---

## 🎯 ACID Properties Questions

### **Q7: Explain ACID properties in detail.**
**A:** **ACID** ensures reliable transaction processing:

**Atomicity:** 
- **All operations succeed or all fail**
- No partial execution allowed
- Example: Bank transfer either completes fully or not at all

**Consistency:**
- **Database remains in valid state** before and after transaction
- All integrity constraints preserved
- Example: Account balance constraints maintained

**Isolation:**
- **Concurrent transactions don't interfere** with each other
- Intermediate results hidden from other transactions
- Example: One user's deposit doesn't affect another's withdrawal visibility

**Durability:**
- **Committed changes survive system failures**
- Changes stored in non-volatile storage
- Example: ATM transaction persists even if system crashes

### **Q8: How is atomicity implemented in database systems?**
**A:** Atomicity is implemented through:
- **Transaction logs** that record all operations
- **Rollback mechanisms** to undo partial changes
- **Two-phase commit protocol** for distributed transactions
- **Shadow paging** techniques
- **Before and after images** in log records

### **Q9: What are different isolation levels?**
**A:** Four standard isolation levels (increasing order):
1. **Read Uncommitted:** Allows dirty reads, lowest isolation
2. **Read Committed:** Prevents dirty reads only
3. **Repeatable Read:** Prevents dirty reads and non-repeatable reads
4. **Serializable:** Highest isolation, prevents all anomalies including phantom reads

### **Q10: How does durability handle system crashes?**
**A:** Durability is ensured through:
- **Write-Ahead Logging (WAL):** Log records written before data
- **Force policy:** Commit records written to stable storage before commit
- **Database buffers:** Proper flushing to disk
- **Recovery procedures:** Redo committed transactions after crash

---

## 🎮 Transaction Control Questions

### **Q11: Explain transaction control commands with examples.**
**A:** **SQL Transaction Control Commands:**

```sql
BEGIN TRANSACTION;          -- Start new transaction
SAVEPOINT sp1;             -- Create intermediate checkpoint  
ROLLBACK TO sp1;           -- Partial rollback to savepoint
ROLLBACK;                  -- Complete rollback
COMMIT;                    -- Permanently save changes
```

**Example:**
```sql
BEGIN TRANSACTION;
    UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
    SAVEPOINT debit_done;
    UPDATE accounts SET balance = balance + 1000 WHERE id = 2;
    -- If error occurs: ROLLBACK TO debit_done;
    COMMIT;
```

### **Q12: What is the difference between ROLLBACK and ROLLBACK TO SAVEPOINT?**
**A:**
- **ROLLBACK:** **Complete undo** of entire transaction, returns to transaction start
- **ROLLBACK TO SAVEPOINT:** **Partial undo** to specific checkpoint, transaction continues from savepoint

### **Q13: When should you use savepoints?**
**A:** Use savepoints when:
- **Long transactions** with multiple logical steps
- **Error recovery** needed for specific operations only
- **Partial rollback** is more efficient than complete rollback
- **Complex business logic** requires intermediate checkpoints

---

## 🚦 Concurrency Problems Questions

### **Q14: What are the main problems in concurrent transaction execution?**
**A:** **Four main concurrency problems:**

1. **Dirty Read:** Reading uncommitted data from another transaction
2. **Lost Update:** One transaction's update overwritten by another
3. **Unrepeatable Read:** Same data read multiple times gives different values
4. **Phantom Read:** Different number of rows returned in repeated queries

### **Q15: Explain dirty read problem with example.**
**A:** **Dirty Read Example:**
```
Time  T1                           T2
1     UPDATE A = 500;              
2                                  SELECT A; (reads 500)
3     ROLLBACK;                    
4                                  Uses incorrect value 500!
```

**Problem:** T2 read **uncommitted data** from T1, which was later rolled back.

### **Q16: How does lost update problem occur?**
**A:** **Lost Update Example:**
```
Time  T1              T2
1     READ A = 100;   
2                     READ A = 100;
3     A = A + 50;     
4                     A = A + 30;
5     WRITE A = 150;  
6                     WRITE A = 130;  (T1's update lost!)
```

**Problem:** Both transactions read same initial value, T2's final write **overwrites T1's update**.

### **Q17: What is phantom read and how is it different from unrepeatable read?**
**A:**
- **Unrepeatable Read:** **Same row gives different values** in repeated reads
- **Phantom Read:** **Different number of rows** returned in repeated queries

**Phantom Read Example:**
```
T1: SELECT COUNT(*) FROM orders WHERE date = '2024-01-01'; (returns 5)
T2: INSERT INTO orders VALUES (..., '2024-01-01', ...); COMMIT;
T1: SELECT COUNT(*) FROM orders WHERE date = '2024-01-01'; (returns 6)
```

---

## 🔀 Serializability Questions

### **Q18: What is serializability and why is it important?**
**A:** **Serializability** means a concurrent schedule produces the same result as some serial execution of the same transactions.

**Importance:**
- **Ensures correctness** of concurrent execution
- **Maintains database consistency**
- **Provides isolation** between transactions
- **Standard for concurrency control** protocols

### **Q19: Explain conflict serializability with example.**
**A:** **Conflict Serializability** checks if schedule is equivalent to serial schedule based on conflicting operations.

**Conflicting Operations:**
- Same data item + Different transactions + At least one write

**Example:**
```
Schedule S: R1(A), W2(A), R2(B), W1(B)

Conflicts:
R1(A) → W2(A)  (T1 must precede T2)
R2(B) → W1(B)  (T2 must precede T1)

Creates cycle T1 → T2 → T1
Result: NOT conflict serializable
```

### **Q20: How do you test for conflict serializability?**
**A:** **Testing Steps:**
1. **Identify conflicting operations** (R-W, W-R, W-W on same item)
2. **Create precedence graph** with transactions as nodes
3. **Add directed edges** from Ti to Tj if Ti conflicts before Tj
4. **Check for cycles** - No cycles = Conflict serializable

### **Q21: What is the difference between conflict and view serializability?**
**A:**
- **Conflict Serializability:** Based on **conflicting operations order**
- **View Serializability:** Based on **read-from relationships**

**Relationship:** All conflict serializable schedules are view serializable, but **not vice versa**. View serializability is **more general** but **harder to test** (NP-complete).

---

## 🔒 Locking Questions

### **Q22: Explain different types of locks in database systems.**
**A:** **Two main lock types:**

**Shared Lock (S-lock):**
- **Multiple transactions** can hold simultaneously
- **Allows concurrent reading**
- **Prevents writing** while held

**Exclusive Lock (X-lock):**
- **Only one transaction** can hold
- **Prevents both reading and writing**
- **Required for write operations**

**Compatibility Matrix:**
```
       S   X
   S   ✓   ✗
   X   ✗   ✗
```

### **Q23: What is Two-Phase Locking (2PL) protocol?**
**A:** **2PL Protocol** has two phases:

**Growing Phase:**
- Transaction can **acquire locks only**
- Cannot release any locks
- Continues until **lock point** (maximum locks held)

**Shrinking Phase:**
- Transaction can **release locks only**  
- Cannot acquire new locks
- Continues until **all locks released**

**Guarantee:** 2PL ensures **conflict serializability**

### **Q24: Compare different variations of 2PL.**
**A:** **2PL Variations:**

**Basic 2PL:**
- **Standard two phases**
- **Deadlocks possible**
- **Cascading rollbacks possible**

**Conservative 2PL:**
- **Acquire all locks before execution**
- **Prevents deadlocks**
- **Reduces concurrency**

**Strict 2PL:**
- **Hold exclusive locks until commit/abort**
- **Prevents cascading rollbacks**
- **Most commonly used**

**Rigorous 2PL:**
- **Hold all locks until commit/abort**
- **Simplifies recovery**
- **Lowest concurrency**

### **Q25: Why doesn't 2PL guarantee freedom from deadlocks?**
**A:** **2PL doesn't prevent deadlocks** because:
- Transactions can still **request locks in different orders**
- **Circular wait conditions** can develop
- Example: T1 holds A, wants B; T2 holds B, wants A

**Solution:** Need additional **deadlock handling mechanisms** (prevention, detection, or avoidance).

---

## ⚰️ Deadlock Questions

### **Q26: What is deadlock and how does it occur?**
**A:** **Deadlock** is a situation where **two or more transactions wait for each other indefinitely**.

**Conditions for Deadlock:**
1. **Mutual exclusion** - Exclusive resource access
2. **Hold and wait** - Holding resources while requesting others
3. **No preemption** - Resources cannot be forcibly taken
4. **Circular wait** - Circular chain of waiting transactions

**Example:**
```
T1: LOCK-X(A); ... wait for LOCK-X(B)
T2: LOCK-X(B); ... wait for LOCK-X(A)
```

### **Q27: Explain different deadlock handling strategies.**
**A:** **Three main strategies:**

**1. Deadlock Prevention:**
- **Wait-Die:** Older transaction waits, younger dies
- **Wound-Wait:** Older wounds (aborts) younger, younger waits

**2. Deadlock Detection:**
- **Wait-for graph** method
- **Periodic cycle detection**
- **Victim selection and abort**

**3. Deadlock Avoidance:**
- **Banker's algorithm** for databases
- **Resource allocation** only if safe state maintained

### **Q28: How does wait-for graph work for deadlock detection?**
**A:** **Wait-for Graph Method:**
1. **Nodes represent transactions**
2. **Edge Ti → Tj** means Ti waits for resource held by Tj
3. **Cycle in graph** indicates deadlock
4. **When deadlock detected,** select victim to abort

**Example:**
```
T1 waits for T2, T2 waits for T3, T3 waits for T1
Graph: T1 → T2 → T3 → T1 (Cycle = Deadlock!)
```

### **Q29: What criteria are used for victim selection in deadlock?**
**A:** **Victim Selection Criteria:**
- **Minimum cost** to abort and restart
- **Least amount of work** done by transaction
- **Fewest number of locks** held
- **Least data items** accessed
- **Avoid starvation** - don't repeatedly choose same transaction

---

## ⏰ Timestamp Questions

### **Q30: How do timestamp-based protocols work?**
**A:** **Timestamp Protocol:**
- **Each transaction** assigned unique timestamp at start
- **Data items** have read and write timestamps
- **Older transactions** have priority over newer ones

**Basic Rules:**
- **Read rule:** TS(Ti) ≥ W-TS(X) → Allow read
- **Write rule:** TS(Ti) ≥ R-TS(X) AND TS(Ti) ≥ W-TS(X) → Allow write

### **Q31: What are the advantages and disadvantages of timestamp protocols?**
**A:** **Advantages:**
- **No deadlocks** possible
- **No waiting** required
- **Guaranteed serializability**

**Disadvantages:**
- **High abort rate** for conflicting transactions
- **Starvation** possible for long transactions
- **Overhead** of timestamp management

---

## 🔧 Recovery Questions

### **Q32: What are different types of failures in database systems?**
**A:** **Three main failure types:**

**1. Transaction Failure:**
- **Logical errors** (incorrect input, constraint violations)
- **System errors** (deadlock, resource unavailability)

**2. System Crash:**
- **Power failure**
- **Operating system crash**
- **Database software failure**

**3. Disk Failure:**
- **Head crash**
- **Disk corruption**  
- **Controller malfunction**

### **Q33: What is Write-Ahead Logging (WAL) protocol?**
**A:** **WAL Protocol Rules:**
1. **Log record must reach stable storage** before corresponding database modification
2. **Transaction cannot commit** until commit log record is in stable storage
3. **Ensures atomicity and durability**

**Benefits:**
- **Recovery possible** from transaction logs
- **Minimal performance impact** on normal operations
- **Standard approach** in most database systems

### **Q34: Explain the phases of log-based recovery.**
**A:** **Three Recovery Phases:**

**1. Analysis Phase:**
- **Scan log forward** from last checkpoint
- **Identify active transactions** at time of crash
- **Build undo-list** (uncommitted) and **redo-list** (committed)

**2. Redo Phase:**
- **Scan log forward** from oldest relevant log record
- **Redo operations** of all committed transactions
- **Ensure durability** of committed changes

**3. Undo Phase:**
- **Scan log backward**
- **Undo operations** of all uncommitted transactions
- **Ensure atomicity** by removing partial changes

### **Q35: What is the purpose of checkpointing in recovery?**
**A:** **Checkpointing Purpose:**
- **Reduce recovery time** by creating known consistent state
- **Limit log scanning** during recovery
- **Force modified buffers** to stable storage periodically

**Checkpoint Process:**
1. **Stop accepting new transactions**
2. **Complete all active transactions**
3. **Write all modified buffers** to disk
4. **Write checkpoint record** to log
5. **Resume normal operation**

---

## 💡 Advanced Questions

### **Q36: How do distributed transactions maintain ACID properties?**
**A:** **Distributed Transaction Challenges:**
- **Atomicity** across multiple sites
- **Consistency** with network partitions
- **Isolation** in distributed environment
- **Durability** with site failures

**Solutions:**
- **Two-Phase Commit (2PC)** protocol
- **Distributed locking** mechanisms
- **Consensus algorithms** (Paxos, Raft)
- **Eventual consistency** models

### **Q37: What is optimistic vs pessimistic concurrency control?**
**A:** 
**Pessimistic (Locking):**
- **Assume conflicts will occur**
- **Lock resources** before access
- **Higher overhead,** lower concurrency

**Optimistic (Timestamp/Validation):**
- **Assume conflicts are rare**
- **Check for conflicts** at commit time
- **Lower overhead,** higher abort rate

### **Q38: How does MVCC (Multi-Version Concurrency Control) work?**
**A:** **MVCC Approach:**
- **Multiple versions** of each data item
- **Readers never block** writers
- **Writers never block** readers
- **Each transaction** sees consistent snapshot

**Benefits:**
- **Higher concurrency**
- **No read locks** needed
- **Consistent read** views

### **Q39: What are compensation transactions?**
**A:** **Compensation Transactions:**
- **Logical undo** of committed transaction effects
- **Used in long-running** business processes
- **Example:** Booking cancellation compensates booking

**When Used:**
- **Saga pattern** in microservices
- **Business process** rollback
- **Long-running transactions**

### **Q40: How do you handle transaction processing in NoSQL databases?**
**A:** **NoSQL Transaction Challenges:**
- **Eventual consistency** models
- **Limited ACID** support
- **Distributed nature**

**Solutions:**
- **Application-level** transaction management
- **Compensation-based** approaches
- **Saga patterns**
- **Two-phase commit** where supported
- **Document-level** atomicity

---

## 🔥 Quick Viva Tips

### **Key Points to Remember:**
1. **Always relate to real-world examples** (banking, e-commerce)
2. **Draw diagrams** for states, graphs, protocols
3. **Mention trade-offs** between approaches
4. **Connect concepts** - how locking relates to serializability
5. **Discuss practical implementations** in actual databases

### **Common Follow-up Questions:**
- "How would you implement this in practice?"
- "What are the performance implications?"
- "How does this scale with large databases?"
- "What happens in distributed environments?"
- "Can you give a real-world scenario?"

### **Confidence Boosters:**
- **Practice drawing** precedence graphs quickly
- **Memorize ACID** property examples
- **Know recovery algorithm** steps by heart
- **Understand deadlock** scenarios clearly
- **Be ready with banking/e-commerce** examples for every concept
