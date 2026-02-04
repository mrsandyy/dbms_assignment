Here is the expected **MySQL execution output** for the assignment. This simulates running the queries in a MySQL command line client or Workbench (MySQL 8.0+ is required for CHECK constraints to work).

---

### **Q1: University Database Output**

**1. Create Tables & Insert Sample Data**
```text
mysql> INSERT INTO Student (RollNo, Name) VALUES (1, 'John Doe');
Query OK, 1 row affected

mysql> INSERT INTO Course VALUES (101, 4);
Query OK, 1 row affected

mysql> SELECT * FROM Student;
+--------+----------+---------------+--------+
| RollNo | Name     | AdmissionDate | Status |
+--------+----------+---------------+--------+
|      1 | John Doe | 2023-10-27    | Active |  <-- Note: Defaults applied
+--------+----------+---------------+--------+
```

**2. Verify Constraint Violations (Invalid Inserts)**
```text
mysql> INSERT INTO Student (RollNo, Name) VALUES (2, NULL);
ERROR 1048 (23000): Column 'Name' cannot be null

mysql> INSERT INTO Course VALUES (102, 6);
ERROR 3819 (HY000): Check constraint 'course_chk_1' is violated.
```

---

### **Q2: Banking System Output**

**1. Demonstrate ON DELETE CASCADE**
```text
mysql> INSERT INTO Account VALUES (1001, 5000.00, 'Savings');
Query OK, 1 row affected

mysql> INSERT INTO Transaction (TransID, AccountNumber, Amount) VALUES (1, 1001, 500.00);
Query OK, 1 row affected

mysql> DELETE FROM Account WHERE AccountNumber = 1001;
Query OK, 1 row affected

mysql> SELECT * FROM Transaction WHERE AccountNumber = 1001;
Empty set (0.00 sec) 
-- Result: The transaction was deleted automatically.
```

**2. Invalid Transactions**
```text
mysql> INSERT INTO Account VALUES (1002, 500.00, 'Current');
ERROR 3819 (HY000): Check constraint 'account_chk_1' is violated.

mysql> INSERT INTO Transaction VALUES (2, 1002, NOW(), -200);
ERROR 3819 (HY000): Check constraint 'transaction_chk_1' is violated.
```

---

### **Q3: Enterprise Database Output**

**1. Show failed DELETE (Foreign Key Restrict)**
```text
mysql> INSERT INTO Department VALUES (1, 'HR');
Query OK, 1 row affected

mysql> INSERT INTO Employee VALUES (101, 1, 20000, DEFAULT);
Query OK, 1 row affected

mysql> DELETE FROM Department WHERE DeptID = 1;
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`testdb`.`employee`, CONSTRAINT `employee_ibfk_1` FOREIGN KEY (`DeptID`) REFERENCES `department` (`DeptID`) ON DELETE RESTRICT)
```

---

### **Q4: E-Commerce Output**

**1. Prove DEFAULT constraint execution**
```text
mysql> INSERT INTO Customer VALUES (1, 'Alice');
Query OK, 1 row affected

mysql> INSERT INTO Orders (OrderID, CustID) VALUES (500, 1);
Query OK, 1 row affected

mysql> SELECT * FROM Orders;
+---------+--------+------------+
| OrderID | CustID | OrderDate  |
+---------+--------+------------+
|     500 |      1 | 2023-10-27 |  <-- Result: Date automatically inserted
+---------+--------+------------+
```

---

### **Q5: Alter Employee Table Output**

**1. Show Table Description (DESC) after all changes**
*Context: After adding NOT NULL, Default 25000, Check > 20000, and then Removing Default.*

```text
mysql> DESC Employee;
+--------+---------------+------+-----+---------+-------+
| Field  | Type          | Null | Key | Default | Extra |
+--------+---------------+------+-----+---------+-------+
| EmpID  | int           | NO   | PRI | NULL    |       |
| Name   | varchar(50)   | NO   |     | NULL    |       | <-- NOT NULL applied
| Salary | decimal(10,2) | YES  |     | NULL    |       | <-- Default removed
+--------+---------------+------+-----+---------+-------+
3 rows in set (0.01 sec)
```
*(Note: The CHECK constraint does not appear in `DESC` output in MySQL; it appears in `SHOW CREATE TABLE Employee`)*.
