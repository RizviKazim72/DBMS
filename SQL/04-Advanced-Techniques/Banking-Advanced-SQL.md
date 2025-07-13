# 04-Advanced Techniques - Banking System ke Saath

Bhai, ab **SQL ke advanced weapons** sikhenge! 🔥 **Banking system** jaise **HDFC, SBI** ka database banayenge aur **complex financial analytics** karenge!

Real scenario: Tumhe **Bank ka backend developer** banana hai. **Fraud detection** kaise kare? **Loan eligibility** kaise calculate kare? **Customer risk assessment** kaise analyze kare? **Advanced SQL techniques** ke saath! 💰

---

## 🎯 **What We'll Build: Digital Banking System**

### **Tables Structure:**
- **CUSTOMERS** - Account holders, KYC details
- **ACCOUNTS** - Bank accounts, balances, types
- **TRANSACTIONS** - All money movements
- **LOANS** - Loan applications, approvals, EMIs
- **CARDS** - Credit/Debit cards, limits
- **BRANCHES** - Bank branch network
- **EMPLOYEES** - Bank staff hierarchy

### **Real Banking Features:**
- Transaction fraud detection
- Customer credit scoring
- Loan eligibility assessment
- Revenue analytics by product
- Risk management dashboards

---

## 🏗️ **Banking Database Schema**

### **CUSTOMERS Table:**
```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_number VARCHAR(12) UNIQUE NOT NULL,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(15) NOT NULL,
    date_of_birth DATE NOT NULL,
    gender ENUM('Male', 'Female', 'Other') NOT NULL,
    occupation VARCHAR(100),
    annual_income DECIMAL(12,2),
    credit_score INT DEFAULT 0 CHECK (credit_score BETWEEN 300 AND 850),
    kyc_status ENUM('Pending', 'Completed', 'Rejected') DEFAULT 'Pending',
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### **BRANCHES Table:**
```sql
CREATE TABLE branches (
    branch_id INT PRIMARY KEY AUTO_INCREMENT,
    branch_code VARCHAR(6) UNIQUE NOT NULL,
    branch_name VARCHAR(100) NOT NULL,
    address TEXT NOT NULL,
    city VARCHAR(50) NOT NULL,
    state VARCHAR(50) NOT NULL,
    pincode VARCHAR(6) NOT NULL,
    phone VARCHAR(15),
    manager_name VARCHAR(100),
    is_active BOOLEAN DEFAULT TRUE,
    established_date DATE NOT NULL
);
```

### **ACCOUNTS Table:**
```sql
CREATE TABLE accounts (
    account_id INT PRIMARY KEY AUTO_INCREMENT,
    account_number VARCHAR(16) UNIQUE NOT NULL,
    customer_id INT NOT NULL,
    branch_id INT NOT NULL,
    account_type ENUM('Savings', 'Current', 'Fixed Deposit', 'Recurring Deposit') NOT NULL,
    balance DECIMAL(15,2) DEFAULT 0.00,
    minimum_balance DECIMAL(10,2) DEFAULT 1000.00,
    interest_rate DECIMAL(5,2) DEFAULT 3.50,
    status ENUM('Active', 'Frozen', 'Closed') DEFAULT 'Active',
    opened_date DATE NOT NULL,
    last_transaction_date TIMESTAMP,
    
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (branch_id) REFERENCES branches(branch_id)
);
```

### **TRANSACTIONS Table:**
```sql
CREATE TABLE transactions (
    transaction_id INT PRIMARY KEY AUTO_INCREMENT,
    transaction_reference VARCHAR(20) UNIQUE NOT NULL,
    account_id INT NOT NULL,
    transaction_type ENUM('Credit', 'Debit') NOT NULL,
    amount DECIMAL(12,2) NOT NULL,
    balance_after DECIMAL(15,2) NOT NULL,
    description VARCHAR(200),
    transaction_mode ENUM('ATM', 'Online', 'Branch', 'UPI', 'NEFT', 'RTGS', 'Cheque') NOT NULL,
    beneficiary_account VARCHAR(16),
    transaction_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    processing_date DATE NOT NULL,
    status ENUM('Pending', 'Success', 'Failed', 'Reversed') DEFAULT 'Pending',
    charges DECIMAL(8,2) DEFAULT 0.00,
    
    FOREIGN KEY (account_id) REFERENCES accounts(account_id),
    INDEX idx_trans_account_date (account_id, transaction_date),
    INDEX idx_trans_type_date (transaction_type, transaction_date)
);
```

### **LOANS Table:**
```sql
CREATE TABLE loans (
    loan_id INT PRIMARY KEY AUTO_INCREMENT,
    loan_number VARCHAR(15) UNIQUE NOT NULL,
    customer_id INT NOT NULL,
    loan_type ENUM('Personal', 'Home', 'Car', 'Education', 'Business') NOT NULL,
    principal_amount DECIMAL(12,2) NOT NULL,
    interest_rate DECIMAL(5,2) NOT NULL,
    tenure_months INT NOT NULL,
    emi_amount DECIMAL(10,2) NOT NULL,
    outstanding_amount DECIMAL(12,2) NOT NULL,
    application_date DATE NOT NULL,
    approval_date DATE,
    disbursement_date DATE,
    status ENUM('Applied', 'Approved', 'Rejected', 'Disbursed', 'Closed') DEFAULT 'Applied',
    purpose TEXT,
    
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

### **CARDS Table:**
```sql
CREATE TABLE cards (
    card_id INT PRIMARY KEY AUTO_INCREMENT,
    card_number VARCHAR(16) UNIQUE NOT NULL,
    customer_id INT NOT NULL,
    account_id INT NOT NULL,
    card_type ENUM('Debit', 'Credit') NOT NULL,
    card_variant VARCHAR(50), -- Classic, Gold, Platinum, etc.
    credit_limit DECIMAL(10,2) DEFAULT 0.00,
    available_limit DECIMAL(10,2) DEFAULT 0.00,
    expiry_date DATE NOT NULL,
    issue_date DATE NOT NULL,
    status ENUM('Active', 'Blocked', 'Expired', 'Lost') DEFAULT 'Active',
    annual_fee DECIMAL(8,2) DEFAULT 0.00,
    
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (account_id) REFERENCES accounts(account_id)
);
```

### **EMPLOYEES Table:**
```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    employee_code VARCHAR(8) UNIQUE NOT NULL,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(15) NOT NULL,
    designation VARCHAR(100) NOT NULL,
    department ENUM('Operations', 'Loans', 'Cards', 'Accounts', 'IT', 'HR', 'Management') NOT NULL,
    branch_id INT NOT NULL,
    manager_id INT,
    salary DECIMAL(10,2) NOT NULL,
    hire_date DATE NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    
    FOREIGN KEY (branch_id) REFERENCES branches(branch_id),
    FOREIGN KEY (manager_id) REFERENCES employees(employee_id)
);
```

---

## 📊 **Sample Data - Banking Style**

### **Branches Data:**
```sql
INSERT INTO branches (branch_code, branch_name, address, city, state, pincode, manager_name, established_date) VALUES 
('BOM001', 'Mumbai Central', 'Plot 123, CST Road, Mumbai', 'Mumbai', 'Maharashtra', '400001', 'Rajesh Kumar', '2010-01-15'),
('DEL001', 'Delhi Connaught Place', 'Block A, CP, New Delhi', 'New Delhi', 'Delhi', '110001', 'Priya Sharma', '2008-03-20'),
('BLR001', 'Bangalore MG Road', 'MG Road, Bangalore', 'Bangalore', 'Karnataka', '560001', 'Amit Singh', '2012-06-10'),
('CHE001', 'Chennai Anna Salai', 'Anna Salai, Chennai', 'Chennai', 'Tamil Nadu', '600002', 'Sneha Reddy', '2011-09-05'),
('HYD001', 'Hyderabad Banjara Hills', 'Banjara Hills, Hyderabad', 'Hyderabad', 'Telangana', '500034', 'Vikash Gupta', '2013-12-01');
```

### **Customers Data:**
```sql
INSERT INTO customers (customer_number, first_name, last_name, email, phone, date_of_birth, gender, occupation, annual_income, credit_score, kyc_status) VALUES 
('CUST00000001', 'Rahul', 'Sharma', 'rahul.sharma@email.com', '9876543210', '1990-05-15', 'Male', 'Software Engineer', 1200000.00, 750, 'Completed'),
('CUST00000002', 'Priya', 'Singh', 'priya.singh@email.com', '9876543211', '1988-08-22', 'Female', 'Marketing Manager', 800000.00, 720, 'Completed'),
('CUST00000003', 'Amit', 'Kumar', 'amit.kumar@email.com', '9876543212', '1985-12-10', 'Male', 'Business Owner', 2500000.00, 780, 'Completed'),
('CUST00000004', 'Sneha', 'Patel', 'sneha.patel@email.com', '9876543213', '1992-03-28', 'Female', 'Doctor', 1500000.00, 760, 'Completed'),
('CUST00000005', 'Vikash', 'Gupta', 'vikash.gupta@email.com', '9876543214', '1987-11-05', 'Male', 'Teacher', 600000.00, 680, 'Completed');
```

### **Accounts Data:**
```sql
INSERT INTO accounts (account_number, customer_id, branch_id, account_type, balance, minimum_balance, opened_date) VALUES 
('1234567890123456', 1, 1, 'Savings', 250000.00, 10000.00, '2020-01-15'),
('1234567890123457', 2, 2, 'Savings', 180000.00, 10000.00, '2019-03-20'),
('1234567890123458', 3, 3, 'Current', 850000.00, 25000.00, '2018-06-10'),
('1234567890123459', 4, 4, 'Savings', 320000.00, 10000.00, '2021-02-14'),
('1234567890123460', 5, 5, 'Savings', 95000.00, 5000.00, '2020-09-25'),
('1234567890123461', 1, 1, 'Fixed Deposit', 500000.00, 0.00, '2022-01-10'),
('1234567890123462', 3, 3, 'Savings', 150000.00, 10000.00, '2019-08-15');
```

### **Loans Data:**
```sql
INSERT INTO loans (loan_number, customer_id, loan_type, principal_amount, interest_rate, tenure_months, emi_amount, outstanding_amount, application_date, approval_date, disbursement_date, status) VALUES 
('HL2024000001', 1, 'Home', 5000000.00, 8.50, 240, 45134.00, 4800000.00, '2024-01-15', '2024-02-01', '2024-02-15', 'Disbursed'),
('PL2024000001', 2, 'Personal', 300000.00, 12.00, 36, 9965.00, 250000.00, '2024-03-10', '2024-03-20', '2024-03-25', 'Disbursed'),
('CL2024000001', 3, 'Car', 800000.00, 9.50, 84, 12847.00, 720000.00, '2023-12-05', '2024-01-10', '2024-01-20', 'Disbursed'),
('EL2024000001', 4, 'Education', 1000000.00, 10.00, 120, 13215.00, 950000.00, '2024-02-20', '2024-03-05', '2024-03-15', 'Disbursed'),
('BL2024000001', 3, 'Business', 2000000.00, 11.00, 60, 43386.00, 1900000.00, '2024-04-01', '2024-04-15', '2024-04-25', 'Disbursed');
```

---

## 🔍 **SUBQUERIES - Queries within Queries**

**Subqueries** ek query ke andar doosri query likhne ka tarika hai. **Complex business logic** ke liye perfect!

### **Types of Subqueries:**
1. **Scalar Subqueries** - Single value return karte hain
2. **Row Subqueries** - Single row return karte hain  
3. **Table Subqueries** - Multiple rows return karte hain

### **Real Banking Examples:**

#### **1. Find Customers with Above Average Balance:**
```sql
-- Customers whose account balance is above average
SELECT 
    c.customer_number,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    a.account_number,
    a.balance,
    (SELECT AVG(balance) FROM accounts WHERE status = 'Active') AS avg_balance,
    a.balance - (SELECT AVG(balance) FROM accounts WHERE status = 'Active') AS difference_from_avg
FROM customers c
JOIN accounts a ON c.customer_id = a.customer_id
WHERE a.balance > (
    SELECT AVG(balance) 
    FROM accounts 
    WHERE status = 'Active'
)
AND a.status = 'Active'
ORDER BY a.balance DESC;
```

#### **2. Find Customers with Multiple Accounts:**
```sql
-- Customers who have more than one account
SELECT 
    c.customer_number,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.email,
    account_count.total_accounts,
    account_count.total_balance
FROM customers c
JOIN (
    SELECT 
        customer_id,
        COUNT(*) AS total_accounts,
        SUM(balance) AS total_balance
    FROM accounts 
    WHERE status = 'Active'
    GROUP BY customer_id
    HAVING COUNT(*) > 1
) account_count ON c.customer_id = account_count.customer_id
ORDER BY account_count.total_accounts DESC;
```

#### **3. High Value Transactions Analysis:**
```sql
-- Transactions that are 10x higher than customer's average
SELECT 
    t.transaction_reference,
    c.customer_number,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    t.amount,
    t.transaction_type,
    t.transaction_mode,
    t.transaction_date,
    customer_avg.avg_transaction_amount,
    ROUND(t.amount / customer_avg.avg_transaction_amount, 2) AS multiplier_of_avg
FROM transactions t
JOIN accounts a ON t.account_id = a.account_id
JOIN customers c ON a.customer_id = c.customer_id
JOIN (
    SELECT 
        a.customer_id,
        AVG(t.amount) AS avg_transaction_amount
    FROM transactions t
    JOIN accounts a ON t.account_id = a.account_id
    WHERE t.status = 'Success'
    GROUP BY a.customer_id
) customer_avg ON c.customer_id = customer_avg.customer_id
WHERE t.amount > (customer_avg.avg_transaction_amount * 10)
   AND t.status = 'Success'
ORDER BY multiplier_of_avg DESC;
```

#### **4. Branch Performance Comparison:**
```sql
-- Branches performing above company average
SELECT 
    b.branch_code,
    b.branch_name,
    b.city,
    branch_stats.total_customers,
    branch_stats.total_deposits,
    branch_stats.avg_balance_per_customer,
    company_avg.company_avg_deposits,
    ROUND(
        ((branch_stats.total_deposits - company_avg.company_avg_deposits) * 100.0) / 
        company_avg.company_avg_deposits, 2
    ) AS performance_vs_avg_percent
FROM branches b
JOIN (
    SELECT 
        a.branch_id,
        COUNT(DISTINCT a.customer_id) AS total_customers,
        SUM(a.balance) AS total_deposits,
        AVG(a.balance) AS avg_balance_per_customer
    FROM accounts a
    WHERE a.status = 'Active'
    GROUP BY a.branch_id
) branch_stats ON b.branch_id = branch_stats.branch_id
CROSS JOIN (
    SELECT AVG(branch_deposits.total_deposits) AS company_avg_deposits
    FROM (
        SELECT 
            branch_id,
            SUM(balance) AS total_deposits
        FROM accounts 
        WHERE status = 'Active'
        GROUP BY branch_id
    ) branch_deposits
) company_avg
WHERE branch_stats.total_deposits > company_avg.company_avg_deposits
ORDER BY performance_vs_avg_percent DESC;
```

---

## 📊 **CTEs (Common Table Expressions) - Readable Complex Queries**

**CTEs** complex queries ko **readable aur reusable** banane ka best way hai! **WITH clause** use karte hain.

### **Real Banking Examples:**

#### **1. Customer Financial Summary with CTEs:**
```sql
-- Complete customer financial portfolio analysis
WITH customer_accounts AS (
    SELECT 
        c.customer_id,
        c.customer_number,
        CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
        c.annual_income,
        c.credit_score,
        COUNT(a.account_id) AS total_accounts,
        SUM(a.balance) AS total_balance
    FROM customers c
    LEFT JOIN accounts a ON c.customer_id = a.customer_id AND a.status = 'Active'
    GROUP BY c.customer_id
),
customer_loans AS (
    SELECT 
        customer_id,
        COUNT(*) AS total_loans,
        SUM(principal_amount) AS total_loan_amount,
        SUM(outstanding_amount) AS total_outstanding,
        SUM(emi_amount) AS total_monthly_emi
    FROM loans 
    WHERE status IN ('Disbursed', 'Approved')
    GROUP BY customer_id
),
customer_transactions AS (
    SELECT 
        a.customer_id,
        COUNT(t.transaction_id) AS total_transactions,
        SUM(CASE WHEN t.transaction_type = 'Credit' THEN t.amount ELSE 0 END) AS total_credits,
        SUM(CASE WHEN t.transaction_type = 'Debit' THEN t.amount ELSE 0 END) AS total_debits,
        AVG(t.amount) AS avg_transaction_amount
    FROM transactions t
    JOIN accounts a ON t.account_id = a.account_id
    WHERE t.status = 'Success'
      AND t.transaction_date >= DATE_SUB(CURRENT_DATE, INTERVAL 6 MONTH)
    GROUP BY a.customer_id
)
SELECT 
    ca.customer_number,
    ca.customer_name,
    ca.annual_income,
    ca.credit_score,
    ca.total_accounts,
    ca.total_balance,
    COALESCE(cl.total_loans, 0) AS total_loans,
    COALESCE(cl.total_outstanding, 0) AS loan_outstanding,
    COALESCE(cl.total_monthly_emi, 0) AS monthly_emi_burden,
    COALESCE(ct.total_transactions, 0) AS transactions_6months,
    COALESCE(ct.avg_transaction_amount, 0) AS avg_transaction_amount,
    CASE 
        WHEN ca.total_balance > 500000 AND ca.credit_score > 750 THEN 'Premium'
        WHEN ca.total_balance > 100000 AND ca.credit_score > 700 THEN 'Gold'
        WHEN ca.total_balance > 50000 AND ca.credit_score > 650 THEN 'Silver'
        ELSE 'Basic'
    END AS customer_tier,
    ROUND(
        (COALESCE(cl.total_monthly_emi, 0) * 12 * 100.0) / NULLIF(ca.annual_income, 0), 2
    ) AS debt_to_income_ratio
FROM customer_accounts ca
LEFT JOIN customer_loans cl ON ca.customer_id = cl.customer_id
LEFT JOIN customer_transactions ct ON ca.customer_id = ct.customer_id
ORDER BY ca.total_balance DESC;
```

#### **2. Fraud Detection Analysis:**
```sql
-- Suspicious transaction patterns detection
WITH unusual_transactions AS (
    SELECT 
        t.transaction_id,
        t.account_id,
        t.amount,
        t.transaction_mode,
        t.transaction_date,
        t.description,
        LAG(t.transaction_date) OVER (
            PARTITION BY t.account_id 
            ORDER BY t.transaction_date
        ) AS previous_transaction_time,
        TIMESTAMPDIFF(MINUTE, 
            LAG(t.transaction_date) OVER (
                PARTITION BY t.account_id 
                ORDER BY t.transaction_date
            ), 
            t.transaction_date
        ) AS minutes_since_last_transaction
    FROM transactions t
    WHERE t.status = 'Success'
      AND t.transaction_date >= DATE_SUB(CURRENT_TIMESTAMP, INTERVAL 24 HOUR)
),
customer_transaction_patterns AS (
    SELECT 
        a.customer_id,
        a.account_id,
        AVG(t.amount) AS avg_transaction_amount,
        STDDEV(t.amount) AS stddev_transaction_amount,
        COUNT(*) AS total_transactions_30days
    FROM transactions t
    JOIN accounts a ON t.account_id = a.account_id
    WHERE t.status = 'Success'
      AND t.transaction_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
    GROUP BY a.customer_id, a.account_id
),
suspicious_flags AS (
    SELECT 
        ut.transaction_id,
        ut.account_id,
        ut.amount,
        ut.transaction_mode,
        ut.transaction_date,
        ut.minutes_since_last_transaction,
        ctp.avg_transaction_amount,
        ctp.stddev_transaction_amount,
        CASE 
            WHEN ut.amount > (ctp.avg_transaction_amount + 3 * ctp.stddev_transaction_amount) 
            THEN 'High Amount Anomaly'
            WHEN ut.minutes_since_last_transaction < 5 AND ut.amount > 10000 
            THEN 'Rapid High Value Transactions'
            WHEN ut.transaction_mode = 'ATM' AND ut.amount > 50000 
            THEN 'High ATM Withdrawal'
            WHEN TIME(ut.transaction_date) BETWEEN '00:00:00' AND '05:00:00' AND ut.amount > 25000 
            THEN 'Late Night High Value'
            ELSE NULL
        END AS fraud_flag
    FROM unusual_transactions ut
    JOIN customer_transaction_patterns ctp ON ut.account_id = ctp.account_id
)
SELECT 
    sf.transaction_id,
    c.customer_number,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    a.account_number,
    sf.amount,
    sf.transaction_mode,
    sf.transaction_date,
    sf.fraud_flag,
    sf.minutes_since_last_transaction,
    ROUND(sf.avg_transaction_amount, 2) AS customer_avg_amount
FROM suspicious_flags sf
JOIN accounts a ON sf.account_id = a.account_id
JOIN customers c ON a.customer_id = c.customer_id
WHERE sf.fraud_flag IS NOT NULL
ORDER BY sf.transaction_date DESC;
```

#### **3. Loan Eligibility Assessment:**
```sql
-- Dynamic loan eligibility calculation
WITH customer_financial_profile AS (
    SELECT 
        c.customer_id,
        c.customer_number,
        CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
        c.annual_income,
        c.credit_score,
        c.occupation,
        TIMESTAMPDIFF(YEAR, c.date_of_birth, CURRENT_DATE) AS age,
        SUM(a.balance) AS total_savings,
        COUNT(a.account_id) AS total_accounts
    FROM customers c
    LEFT JOIN accounts a ON c.customer_id = a.customer_id AND a.status = 'Active'
    WHERE c.kyc_status = 'Completed' AND c.is_active = TRUE
    GROUP BY c.customer_id
),
existing_loan_obligations AS (
    SELECT 
        customer_id,
        COUNT(*) AS active_loans,
        SUM(outstanding_amount) AS total_outstanding,
        SUM(emi_amount) AS total_monthly_emi
    FROM loans 
    WHERE status = 'Disbursed'
    GROUP BY customer_id
),
transaction_behavior AS (
    SELECT 
        a.customer_id,
        COUNT(t.transaction_id) AS transactions_6months,
        AVG(CASE WHEN t.transaction_type = 'Credit' THEN t.amount END) AS avg_credit_amount,
        COUNT(CASE WHEN t.transaction_type = 'Credit' THEN 1 END) AS total_credits
    FROM transactions t
    JOIN accounts a ON t.account_id = a.account_id
    WHERE t.status = 'Success'
      AND t.transaction_date >= DATE_SUB(CURRENT_DATE, INTERVAL 6 MONTH)
    GROUP BY a.customer_id
),
eligibility_calculation AS (
    SELECT 
        cfp.customer_id,
        cfp.customer_number,
        cfp.customer_name,
        cfp.annual_income,
        cfp.credit_score,
        cfp.age,
        cfp.total_savings,
        COALESCE(elo.total_monthly_emi, 0) AS existing_emi,
        COALESCE(tb.transactions_6months, 0) AS recent_activity,
        -- Eligibility factors
        CASE 
            WHEN cfp.credit_score >= 750 THEN 30
            WHEN cfp.credit_score >= 700 THEN 25
            WHEN cfp.credit_score >= 650 THEN 15
            WHEN cfp.credit_score >= 600 THEN 10
            ELSE 0
        END AS credit_score_points,
        CASE 
            WHEN cfp.annual_income >= 1000000 THEN 25
            WHEN cfp.annual_income >= 500000 THEN 20
            WHEN cfp.annual_income >= 300000 THEN 15
            WHEN cfp.annual_income >= 200000 THEN 10
            ELSE 5
        END AS income_points,
        CASE 
            WHEN cfp.age BETWEEN 25 AND 55 THEN 15
            WHEN cfp.age BETWEEN 21 AND 65 THEN 10
            ELSE 5
        END AS age_points,
        CASE 
            WHEN cfp.total_savings >= 100000 THEN 15
            WHEN cfp.total_savings >= 50000 THEN 10
            WHEN cfp.total_savings >= 25000 THEN 5
            ELSE 0
        END AS savings_points,
        CASE 
            WHEN COALESCE(elo.total_monthly_emi, 0) * 12 <= cfp.annual_income * 0.3 THEN 15
            WHEN COALESCE(elo.total_monthly_emi, 0) * 12 <= cfp.annual_income * 0.5 THEN 10
            WHEN COALESCE(elo.total_monthly_emi, 0) * 12 <= cfp.annual_income * 0.6 THEN 5
            ELSE -10
        END AS debt_burden_points
    FROM customer_financial_profile cfp
    LEFT JOIN existing_loan_obligations elo ON cfp.customer_id = elo.customer_id
    LEFT JOIN transaction_behavior tb ON cfp.customer_id = tb.customer_id
)
SELECT 
    customer_number,
    customer_name,
    annual_income,
    credit_score,
    age,
    total_savings,
    existing_emi,
    (credit_score_points + income_points + age_points + savings_points + debt_burden_points) AS total_eligibility_score,
    CASE 
        WHEN (credit_score_points + income_points + age_points + savings_points + debt_burden_points) >= 80 THEN 'Excellent - Up to 50L eligible'
        WHEN (credit_score_points + income_points + age_points + savings_points + debt_burden_points) >= 65 THEN 'Good - Up to 25L eligible'
        WHEN (credit_score_points + income_points + age_points + savings_points + debt_burden_points) >= 50 THEN 'Fair - Up to 10L eligible'
        WHEN (credit_score_points + income_points + age_points + savings_points + debt_burden_points) >= 35 THEN 'Poor - Up to 3L eligible'
        ELSE 'Not Eligible'
    END AS loan_eligibility_status,
    ROUND(annual_income * 0.40, 0) AS max_eligible_amount_income_based,
    recent_activity
FROM eligibility_calculation
ORDER BY total_eligibility_score DESC;
```

---

## 🏆 **WINDOW FUNCTIONS - Advanced Analytics**

**Window Functions** advanced analytics ke liye game-changer hain! **Ranking, running totals, moving averages** - sab kuch!

### **Common Window Functions:**
- **ROW_NUMBER()** - Unique row numbers
- **RANK()** - Ranking with gaps
- **DENSE_RANK()** - Ranking without gaps
- **SUM() OVER()** - Running totals
- **AVG() OVER()** - Moving averages

### **Real Banking Examples:**

#### **1. Customer Ranking by Balance:**
```sql
-- Rank customers by total balance within each city
SELECT 
    c.customer_number,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    b.city,
    SUM(a.balance) AS total_balance,
    ROW_NUMBER() OVER (ORDER BY SUM(a.balance) DESC) AS overall_rank,
    RANK() OVER (PARTITION BY b.city ORDER BY SUM(a.balance) DESC) AS rank_in_city,
    DENSE_RANK() OVER (PARTITION BY b.city ORDER BY SUM(a.balance) DESC) AS dense_rank_in_city,
    PERCENT_RANK() OVER (PARTITION BY b.city ORDER BY SUM(a.balance)) AS percentile_in_city
FROM customers c
JOIN accounts a ON c.customer_id = a.customer_id
JOIN branches b ON a.branch_id = b.branch_id
WHERE a.status = 'Active'
GROUP BY c.customer_id, b.city
ORDER BY b.city, rank_in_city;
```

#### **2. Transaction Running Balance:**
```sql
-- Running balance calculation for each account
SELECT 
    t.transaction_reference,
    a.account_number,
    c.customer_number,
    t.transaction_type,
    t.amount,
    t.transaction_date,
    SUM(
        CASE 
            WHEN t.transaction_type = 'Credit' THEN t.amount 
            ELSE -t.amount 
        END
    ) OVER (
        PARTITION BY t.account_id 
        ORDER BY t.transaction_date, t.transaction_id
        ROWS UNBOUNDED PRECEDING
    ) AS running_balance,
    LAG(t.amount) OVER (
        PARTITION BY t.account_id 
        ORDER BY t.transaction_date
    ) AS previous_transaction_amount,
    LEAD(t.amount) OVER (
        PARTITION BY t.account_id 
        ORDER BY t.transaction_date
    ) AS next_transaction_amount,
    ROW_NUMBER() OVER (
        PARTITION BY t.account_id 
        ORDER BY t.transaction_date DESC
    ) AS transaction_sequence
FROM transactions t
JOIN accounts a ON t.account_id = a.account_id
JOIN customers c ON a.customer_id = c.customer_id
WHERE t.status = 'Success'
ORDER BY a.account_number, t.transaction_date;
```

#### **3. Monthly Transaction Trends:**
```sql
-- Monthly transaction volume and amount trends
SELECT 
    YEAR(t.transaction_date) AS transaction_year,
    MONTH(t.transaction_date) AS transaction_month,
    DATE_FORMAT(t.transaction_date, '%Y-%m') AS month_year,
    COUNT(*) AS total_transactions,
    SUM(t.amount) AS total_amount,
    AVG(t.amount) AS avg_transaction_amount,
    -- Previous month comparison
    LAG(COUNT(*)) OVER (ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date)) AS prev_month_transactions,
    LAG(SUM(t.amount)) OVER (ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date)) AS prev_month_amount,
    -- Growth calculations
    ROUND(
        ((COUNT(*) - LAG(COUNT(*)) OVER (ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date))) * 100.0) /
        NULLIF(LAG(COUNT(*)) OVER (ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date)), 0), 2
    ) AS transaction_growth_percent,
    ROUND(
        ((SUM(t.amount) - LAG(SUM(t.amount)) OVER (ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date))) * 100.0) /
        NULLIF(LAG(SUM(t.amount)) OVER (ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date)), 0), 2
    ) AS amount_growth_percent,
    -- Moving averages (3 month)
    AVG(COUNT(*)) OVER (
        ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date)
        ROWS 2 PRECEDING
    ) AS moving_avg_transactions_3month,
    AVG(SUM(t.amount)) OVER (
        ORDER BY YEAR(t.transaction_date), MONTH(t.transaction_date)
        ROWS 2 PRECEDING
    ) AS moving_avg_amount_3month
FROM transactions t
WHERE t.status = 'Success'
   AND t.transaction_date >= DATE_SUB(CURRENT_DATE, INTERVAL 12 MONTH)
GROUP BY YEAR(t.transaction_date), MONTH(t.transaction_date)
ORDER BY transaction_year, transaction_month;
```

#### **4. Top Performing Branches Analysis:**
```sql
-- Branch performance ranking with multiple metrics
SELECT 
    b.branch_code,
    b.branch_name,
    b.city,
    branch_metrics.total_customers,
    branch_metrics.total_accounts,
    branch_metrics.total_deposits,
    branch_metrics.total_loans_disbursed,
    branch_metrics.monthly_transactions,
    -- Rankings
    RANK() OVER (ORDER BY branch_metrics.total_deposits DESC) AS deposit_rank,
    RANK() OVER (ORDER BY branch_metrics.total_loans_disbursed DESC) AS loan_rank,
    RANK() OVER (ORDER BY branch_metrics.total_customers DESC) AS customer_rank,
    RANK() OVER (ORDER BY branch_metrics.monthly_transactions DESC) AS activity_rank,
    -- Performance scores
    ROUND(
        (RANK() OVER (ORDER BY branch_metrics.total_deposits) + 
         RANK() OVER (ORDER BY branch_metrics.total_loans_disbursed) + 
         RANK() OVER (ORDER BY branch_metrics.total_customers) + 
         RANK() OVER (ORDER BY branch_metrics.monthly_transactions)) / 4.0, 2
    ) AS overall_performance_score,
    -- Percentiles
    PERCENT_RANK() OVER (ORDER BY branch_metrics.total_deposits) AS deposit_percentile,
    NTILE(4) OVER (ORDER BY branch_metrics.total_deposits) AS deposit_quartile
FROM branches b
JOIN (
    SELECT 
        b.branch_id,
        COUNT(DISTINCT c.customer_id) AS total_customers,
        COUNT(DISTINCT a.account_id) AS total_accounts,
        SUM(a.balance) AS total_deposits,
        SUM(COALESCE(l.principal_amount, 0)) AS total_loans_disbursed,
        COUNT(DISTINCT t.transaction_id) AS monthly_transactions
    FROM branches b
    LEFT JOIN accounts a ON b.branch_id = a.branch_id AND a.status = 'Active'
    LEFT JOIN customers c ON a.customer_id = c.customer_id
    LEFT JOIN loans l ON c.customer_id = l.customer_id AND l.status = 'Disbursed'
    LEFT JOIN transactions t ON a.account_id = t.account_id 
                              AND t.status = 'Success'
                              AND t.transaction_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
    GROUP BY b.branch_id
) branch_metrics ON b.branch_id = branch_metrics.branch_id
ORDER BY overall_performance_score;
```

---

## 🎯 **CASE Statements - Conditional Logic**

**CASE statements** complex conditional logic ke liye use karte hain. **Business rules** implement karne ke liye perfect!

### **Real Banking Examples:**

#### **1. Customer Risk Assessment:**
```sql
-- Comprehensive customer risk profiling
SELECT 
    c.customer_number,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.credit_score,
    c.annual_income,
    total_accounts.account_count,
    total_accounts.total_balance,
    loan_info.total_outstanding,
    recent_activity.transaction_frequency,
    -- Risk scoring based on multiple factors
    CASE 
        WHEN c.credit_score >= 750 AND c.annual_income >= 1000000 THEN 'Very Low Risk'
        WHEN c.credit_score >= 700 AND c.annual_income >= 500000 THEN 'Low Risk'
        WHEN c.credit_score >= 650 AND c.annual_income >= 300000 THEN 'Medium Risk'
        WHEN c.credit_score >= 600 OR c.annual_income >= 200000 THEN 'High Risk'
        ELSE 'Very High Risk'
    END AS credit_risk_category,
    CASE 
        WHEN total_accounts.total_balance >= 1000000 THEN 'High Net Worth'
        WHEN total_accounts.total_balance >= 500000 THEN 'Affluent'
        WHEN total_accounts.total_balance >= 100000 THEN 'Mass Affluent'
        WHEN total_accounts.total_balance >= 25000 THEN 'Mass Market'
        ELSE 'Basic Banking'
    END AS wealth_segment,
    CASE 
        WHEN recent_activity.transaction_frequency >= 50 THEN 'Very Active'
        WHEN recent_activity.transaction_frequency >= 20 THEN 'Active'
        WHEN recent_activity.transaction_frequency >= 10 THEN 'Moderate'
        WHEN recent_activity.transaction_frequency >= 1 THEN 'Low Activity'
        ELSE 'Inactive'
    END AS activity_level,
    CASE 
        WHEN loan_info.debt_to_income_ratio > 0.6 THEN 'Over Leveraged'
        WHEN loan_info.debt_to_income_ratio > 0.4 THEN 'High Leverage'
        WHEN loan_info.debt_to_income_ratio > 0.2 THEN 'Moderate Leverage'
        WHEN loan_info.debt_to_income_ratio > 0 THEN 'Low Leverage'
        ELSE 'No Debt'
    END AS leverage_status,
    -- Recommendation engine
    CASE 
        WHEN c.credit_score >= 750 AND total_accounts.total_balance >= 500000 AND loan_info.total_outstanding = 0 THEN 'Offer Premium Credit Card'
        WHEN c.credit_score >= 700 AND recent_activity.transaction_frequency >= 20 THEN 'Offer Personal Loan'
        WHEN total_accounts.total_balance >= 100000 AND recent_activity.transaction_frequency < 5 THEN 'Offer Investment Products'
        WHEN c.annual_income >= 500000 AND loan_info.total_outstanding = 0 THEN 'Offer Home Loan'
        ELSE 'Focus on Basic Banking Services'
    END AS recommendation
FROM customers c
LEFT JOIN (
    SELECT 
        customer_id,
        COUNT(*) AS account_count,
        SUM(balance) AS total_balance
    FROM accounts 
    WHERE status = 'Active'
    GROUP BY customer_id
) total_accounts ON c.customer_id = total_accounts.customer_id
LEFT JOIN (
    SELECT 
        customer_id,
        SUM(outstanding_amount) AS total_outstanding,
        SUM(outstanding_amount) / NULLIF(MAX(c.annual_income), 0) AS debt_to_income_ratio
    FROM loans l
    JOIN customers c ON l.customer_id = c.customer_id
    WHERE l.status = 'Disbursed'
    GROUP BY customer_id
) loan_info ON c.customer_id = loan_info.customer_id
LEFT JOIN (
    SELECT 
        a.customer_id,
        COUNT(t.transaction_id) AS transaction_frequency
    FROM transactions t
    JOIN accounts a ON t.account_id = a.account_id
    WHERE t.status = 'Success'
      AND t.transaction_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
    GROUP BY a.customer_id
) recent_activity ON c.customer_id = recent_activity.customer_id
WHERE c.is_active = TRUE
ORDER BY c.credit_score DESC, total_accounts.total_balance DESC;
```

#### **2. Transaction Categorization & Fee Calculation:**
```sql
-- Smart transaction categorization with dynamic fee calculation
SELECT 
    t.transaction_reference,
    a.account_number,
    c.customer_number,
    t.amount,
    t.transaction_type,
    t.transaction_mode,
    t.transaction_date,
    -- Transaction categorization
    CASE 
        WHEN t.description LIKE '%ATM%' OR t.transaction_mode = 'ATM' THEN 'ATM Transaction'
        WHEN t.description LIKE '%UPI%' OR t.transaction_mode = 'UPI' THEN 'Digital Payment'
        WHEN t.description LIKE '%NEFT%' OR t.transaction_mode = 'NEFT' THEN 'Bank Transfer'
        WHEN t.description LIKE '%SALARY%' OR t.description LIKE '%SAL%' THEN 'Salary Credit'
        WHEN t.description LIKE '%EMI%' OR t.description LIKE '%LOAN%' THEN 'Loan Payment'
        WHEN t.description LIKE '%UTILITY%' OR t.description LIKE '%ELECTRICITY%' OR t.description LIKE '%WATER%' THEN 'Utility Payment'
        WHEN t.description LIKE '%SHOPPING%' OR t.description LIKE '%PURCHASE%' THEN 'Shopping'
        ELSE 'Other'
    END AS transaction_category,
    -- Dynamic fee calculation
    CASE 
        WHEN a.account_type = 'Savings' AND t.transaction_mode = 'ATM' AND t.amount <= 10000 THEN 0
        WHEN a.account_type = 'Savings' AND t.transaction_mode = 'ATM' AND t.amount > 10000 THEN 5
        WHEN a.account_type = 'Current' AND t.transaction_mode = 'ATM' THEN 2
        WHEN t.transaction_mode = 'NEFT' AND t.amount <= 10000 THEN 2.50
        WHEN t.transaction_mode = 'NEFT' AND t.amount <= 100000 THEN 5.00
        WHEN t.transaction_mode = 'NEFT' AND t.amount > 100000 THEN 15.00
        WHEN t.transaction_mode = 'RTGS' AND t.amount <= 500000 THEN 25.00
        WHEN t.transaction_mode = 'RTGS' AND t.amount > 500000 THEN 50.00
        WHEN t.transaction_mode = 'UPI' THEN 0
        WHEN t.transaction_mode = 'Online' AND t.amount <= 25000 THEN 0
        WHEN t.transaction_mode = 'Online' AND t.amount > 25000 THEN 10.00
        ELSE 5.00
    END AS applicable_fee,
    -- Customer tier based benefits
    CASE 
        WHEN customer_tier.tier = 'Premium' THEN 0  -- No fees for premium customers
        WHEN customer_tier.tier = 'Gold' AND t.transaction_mode IN ('ATM', 'UPI', 'Online') THEN 0
        WHEN customer_tier.tier = 'Silver' AND t.transaction_mode = 'UPI' THEN 0
        ELSE 
            CASE 
                WHEN a.account_type = 'Savings' AND t.transaction_mode = 'ATM' AND t.amount <= 10000 THEN 0
                WHEN a.account_type = 'Savings' AND t.transaction_mode = 'ATM' AND t.amount > 10000 THEN 5
                WHEN a.account_type = 'Current' AND t.transaction_mode = 'ATM' THEN 2
                WHEN t.transaction_mode = 'NEFT' AND t.amount <= 10000 THEN 2.50
                WHEN t.transaction_mode = 'NEFT' AND t.amount <= 100000 THEN 5.00
                WHEN t.transaction_mode = 'NEFT' AND t.amount > 100000 THEN 15.00
                WHEN t.transaction_mode = 'RTGS' AND t.amount <= 500000 THEN 25.00
                WHEN t.transaction_mode = 'RTGS' AND t.amount > 500000 THEN 50.00
                WHEN t.transaction_mode = 'UPI' THEN 0
                WHEN t.transaction_mode = 'Online' AND t.amount <= 25000 THEN 0
                WHEN t.transaction_mode = 'Online' AND t.amount > 25000 THEN 10.00
                ELSE 5.00
            END
    END AS final_fee_after_benefits,
    -- Time-based categorization
    CASE 
        WHEN HOUR(t.transaction_date) BETWEEN 9 AND 17 THEN 'Business Hours'
        WHEN HOUR(t.transaction_date) BETWEEN 18 AND 23 THEN 'Evening'
        WHEN HOUR(t.transaction_date) BETWEEN 0 AND 5 THEN 'Late Night'
        ELSE 'Early Morning'
    END AS time_category
FROM transactions t
JOIN accounts a ON t.account_id = a.account_id
JOIN customers c ON a.customer_id = c.customer_id
LEFT JOIN (
    SELECT 
        customer_id,
        CASE 
            WHEN SUM(balance) >= 1000000 THEN 'Premium'
            WHEN SUM(balance) >= 500000 THEN 'Gold'
            WHEN SUM(balance) >= 100000 THEN 'Silver'
            ELSE 'Basic'
        END AS tier
    FROM accounts 
    WHERE status = 'Active'
    GROUP BY customer_id
) customer_tier ON c.customer_id = customer_tier.customer_id
WHERE t.status = 'Success'
   AND t.transaction_date >= DATE_SUB(CURRENT_DATE, INTERVAL 7 DAY)
ORDER BY t.transaction_date DESC;
```

---

## 📋 **Practice Exercises - Banking Scenarios**

### **Easy Level:**
1. **Above Average Customers:** Find customers with balance above branch average
2. **Loan Defaulters:** Customers with overdue EMIs using subqueries
3. **Top Transactions:** Rank transactions by amount within each month

### **Medium Level:**
1. **Customer Profitability:** Calculate revenue per customer using CTEs
2. **Seasonal Trends:** Monthly transaction patterns with window functions
3. **Risk Scoring:** Multi-factor customer risk assessment with CASE

### **Hard Level:**
1. **Fraud Detection:** Suspicious transaction patterns using complex CTEs
2. **Product Cross-sell:** Recommendation engine based on customer behavior
3. **Branch Efficiency:** Multi-metric branch performance analysis

---

## 🚀 **Performance Tips for Advanced Queries**

### **Indexing for Complex Queries:**
```sql
-- Essential indexes for banking analytics
CREATE INDEX idx_transactions_customer_date ON transactions(account_id, transaction_date, status);
CREATE INDEX idx_accounts_customer_balance ON accounts(customer_id, balance, status);
CREATE INDEX idx_loans_customer_status ON loans(customer_id, status, outstanding_amount);
CREATE INDEX idx_customers_income_score ON customers(annual_income, credit_score, is_active);

-- Composite indexes for window functions
CREATE INDEX idx_trans_account_date_amount ON transactions(account_id, transaction_date, amount);
CREATE INDEX idx_branch_performance ON accounts(branch_id, status, balance);
```

### **Query Optimization Tips:**
1. **Use CTEs** instead of nested subqueries for readability
2. **Limit data** with proper WHERE clauses before window functions
3. **Index all columns** used in PARTITION BY and ORDER BY
4. **Use EXPLAIN** to analyze query execution plans

---

## 🎯 **Next Steps**

**Completed:** ✅ Advanced SQL techniques mastered with Banking system  
**Next Module:** **05-Performance-King** - Indexing, Query Optimization, High-traffic handling

**Real Learning:** Ab tumhe pata hai ki **Banks jaise critical systems** kaise **complex analytics** aur **business intelligence** handle karte hain! **Risk assessment, fraud detection, customer profiling** - sab pro level! 💪

**Ready for Performance Optimization?** High-traffic applications ke liye **indexing strategies** aur **query tuning** sikhaaunga! ⚡🔧
