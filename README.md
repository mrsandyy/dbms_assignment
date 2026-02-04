# dbms_assignment

### **Q1: University Database**
*Concepts: Defaults, Checks, Composite Primary Key.*

```sql
-- 1. Create Tables
CREATE TABLE Student (
    RollNo INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    AdmissionDate DATE DEFAULT (CURRENT_DATE),
    Status VARCHAR(10) DEFAULT 'Active'
);

CREATE TABLE Course (
    CourseID INT PRIMARY KEY,
    Credits INT CHECK (Credits BETWEEN 1 AND 5)
);

CREATE TABLE Enrollment (
    RollNo INT,
    CourseID INT,
    PRIMARY KEY (RollNo, CourseID), -- Composite Primary Key
    FOREIGN KEY (RollNo) REFERENCES Student(RollNo),
    FOREIGN KEY (CourseID) REFERENCES Course(CourseID)
);

-- 3. Insert Sample Data
INSERT INTO Student (RollNo, Name) VALUES (1, 'John Doe'); -- Uses defaults
INSERT INTO Course VALUES (101, 4);
INSERT INTO Enrollment VALUES (1, 101);

-- 4. Verify Constraints (These will ERROR)
INSERT INTO Student (RollNo, Name) VALUES (2, NULL); -- Fails: Name is NULL
INSERT INTO Course VALUES (102, 6); -- Fails: Credits > 5
```

---

### **Q2: Banking System**
*Concepts: On Delete Cascade, Check, Default.*

```sql
-- 1. Create Tables
CREATE TABLE Account (
    AccountNumber INT PRIMARY KEY,
    Balance DECIMAL(10, 2) CHECK (Balance >= 1000),
    AccountType VARCHAR(20) DEFAULT 'Savings'
);

CREATE TABLE Transaction (
    TransID INT PRIMARY KEY,
    AccountNumber INT,
    TransTime TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    Amount DECIMAL(10, 2) CHECK (Amount > 0), -- Disallow negative
    FOREIGN KEY (AccountNumber) REFERENCES Account(AccountNumber) 
    ON DELETE CASCADE
);

-- 2. Demonstrate ON DELETE CASCADE
INSERT INTO Account VALUES (1001, 5000.00, 'Savings');
INSERT INTO Transaction (TransID, AccountNumber, Amount) VALUES (1, 1001, 500.00);
DELETE FROM Account WHERE AccountNumber = 1001; 
-- Result: The Transaction row for 1001 is automatically deleted.

-- 3. Invalid Transactions (ERROR)
INSERT INTO Account VALUES (1002, 500.00, 'Current'); -- Fails: Balance < 1000
INSERT INTO Transaction VALUES (2, 1002, NOW(), -200); -- Fails: Negative Amount
```

---

### **Q3: Enterprise Database**
*Concepts: Foreign Key Restrict, Salary Check.*

```sql
-- 1. Create Tables
CREATE TABLE Department (
    DeptID INT PRIMARY KEY,
    DeptName VARCHAR(50)
);

CREATE TABLE Employee (
    EmpID INT PRIMARY KEY,
    DeptID INT,
    Salary DECIMAL(10, 2) CHECK (Salary > 15000),
    Status VARCHAR(20) DEFAULT 'Working',
    FOREIGN KEY (DeptID) REFERENCES Department(DeptID) 
    ON DELETE RESTRICT -- Prevents deletion if employee exists
);

-- 3. Show Failed DELETE
INSERT INTO Department VALUES (1, 'HR');
INSERT INTO Employee VALUES (101, 1, 20000, DEFAULT);
DELETE FROM Department WHERE DeptID = 1; 
-- Result: ERROR - Cannot delete or update a parent row: foreign key constraint fails.
```

---

### **Q4: E-Commerce Database**
*Concepts: Defaults, referencing valid customers.*

```sql
-- 1. Create Tables
CREATE TABLE Customer (CustID INT PRIMARY KEY, Name VARCHAR(50)); -- Prerequisite

CREATE TABLE Product (
    ProductID INT PRIMARY KEY,
    Price DECIMAL(10, 2) CHECK (Price > 0),
    Quantity INT DEFAULT 0
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustID INT REFERENCES Customer(CustID),
    OrderDate DATE DEFAULT (CURRENT_DATE)
);

-- 2 & 3. Prove DEFAULT Constraint
INSERT INTO Customer VALUES (1, 'Alice');
INSERT INTO Orders (OrderID, CustID) VALUES (500, 1); 
-- Note: OrderDate was omitted.
SELECT * FROM Orders; 
-- Result: OrderDate column contains today's date automatically.
```

---

### **Q5: Alter Table Employee**
*Concepts: ALTER ADD/DROP constraints.*

```sql
-- 1. Add NOT NULL to Name
ALTER TABLE Employee MODIFY Name VARCHAR(50) NOT NULL; 
-- (SQL Server: ALTER COLUMN Name VARCHAR(50) NOT NULL)

-- 2. Add DEFAULT salary
ALTER TABLE Employee ALTER COLUMN Salary SET DEFAULT 25000;
-- (SQL Server: ADD CONSTRAINT DF_Sal DEFAULT 25000 FOR Salary)

-- 3. Add CHECK constraint
ALTER TABLE Employee ADD CONSTRAINT CHK_Sal CHECK (Salary >= 20000);

-- 4. Remove DEFAULT constraint
ALTER TABLE Employee ALTER COLUMN Salary DROP DEFAULT;
-- (SQL Server: ALTER TABLE Employee DROP CONSTRAINT DF_Sal)

-- 5. Show Description
DESC Employee; 
-- (SQL Server: EXEC sp_help 'Employee')
```
