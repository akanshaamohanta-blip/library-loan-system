# JDBC Library Loan Management System

> End-to-End JDBC Application with Transaction Management & Performance Evaluation using Apache Derby

**Course:** Database Implementation in JDBC (CSE3488)  
**University:** Siksha 'O' Anusandhan (Deemed to be University), Bhubaneswar  
**Academic Year:** 2025–2026

---

## Overview

A console-driven Library Loan Management System built entirely in Java using JDBC and Apache Derby (embedded mode). The system demonstrates:

- Explicit ACID transaction management (commit, rollback, savepoints)
- PreparedStatement-based parameterized CRUD operations
- Built-in performance benchmarking across multiple JDBC strategies
- Modular architecture with proper resource cleanup

---

## Project Structure

```
jdbc-library-loan-system/
│
├── src/
│   └── com/dbms/lab/
│       └── mini_project_1.java       # Main application (all modules)
│
├── lib/
│   └── derby.jar                     # Apache Derby embedded driver
│
├── LibraryDB/                        # Auto-created by Derby on first run
│
├── README.md
└── .gitignore
```

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Java JDK | 17 or above |
| Apache Derby | Latest (embedded) |
| IDE | IntelliJ IDEA / Eclipse / VS Code |

---

## Setup & Run Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/jdbc-library-loan-system.git
cd jdbc-library-loan-system
```

### 2. Add Derby JAR to Classpath

Download `derby.jar` from [Apache Derby Downloads](https://db.apache.org/derby/derby_downloads.html) and place it inside the `lib/` folder.

### 3. Compile

```bash
javac -cp lib/derby.jar src/com/dbms/lab/mini_project_1.java -d out/
```

### 4. Run

```bash
java -cp out:lib/derby.jar com.dbms.lab.mini_project_1
```

> **Windows:** Replace `:` with `;` in the classpath  
> ```bash
> java -cp out;lib/derby.jar com.dbms.lab.mini_project_1
> ```

---

## CLI Menu

```
========== LIBRARY LOAN MANAGEMENT ==========
1. Register Member
2. Add Book
3. Process Loan
4. Return Book
5. View Active Loans
6. Performance Test
7. Exit
Enter Choice:
```

---

## Sample Session

```
Database Connected Successfully.
Members table created. Books table created. Loans table created.

Enter Choice: 1
Enter Member ID: 12
Enter Member Name: Akansha
Member Registered Successfully.

Enter Choice: 2
Enter Book ID: 20  Enter Book Title: Harry Potter  Enter Author Name: J.K. Rowling
Book Added Successfully.

Enter Choice: 3
Enter Member ID: 12  Enter Book ID: 20
Loan Processed Successfully.

Enter Choice: 5
===== ACTIVE LOANS =====
Loan ID: 2 | Member ID: 12 | Book ID: 20 | Loan Date: 2026-05-15

Enter Choice: 4
Enter Loan ID: 2
Book Returned Successfully.
```

---

## Database Schema

### Members
| Field | Type | Constraint |
|-------|------|------------|
| MemberID | INT | Primary Key |
| Name | VARCHAR(100) | Not Null |
| ActiveLoans | INT | Default 0 |

### Books
| Field | Type | Constraint |
|-------|------|------------|
| BookID | INT | Primary Key |
| Title | VARCHAR(200) | Not Null |
| Author | VARCHAR(100) | Not Null |
| Available | BOOLEAN | Default TRUE |

### Loans
| Field | Type | Constraint |
|-------|------|------------|
| LoanID | INT | Identity (PK) |
| MemberID | INT | FK → Members |
| BookID | INT | FK → Books |
| LoanDate | DATE | CURRENT_DATE |
| ReturnDate | DATE | NULL if active |

---

## Benchmark Results

| Test Case | Records | Avg Time (ms) | Throughput |
|-----------|---------|---------------|------------|
| Individual Inserts | 1000 | 2378.97 | 420.35 ops/sec |
| Batch Inserts | 1000 | 311.51 | 3210.18 ops/sec |
| Full Table Scan | 10000 | 310.00 | Moderate |
| Indexed Lookup | 10000 | 45.00 | High |
| Statement Execution | 5000 | 220.00 | Moderate |
| PreparedStatement | 5000 | 140.00 | High |

**Key finding:** Batch inserts are ~7.6× faster than individual inserts; indexed lookups are ~6.9× faster than full-table scans.

---

## Transaction Management

The `processLoan()` method demonstrates full ACID compliance:

```java
con.setAutoCommit(false);
// Step 1: Check book availability
// Step 2: Insert loan record        ← Savepoint here
// Step 3: Mark book unavailable
// Step 4: Increment member loan count
con.commit();                         // All steps succeed → commit
// On any failure → con.rollback()   // Atomic rollback
```

---

## Common Issues

| Error | Cause | Fix |
|-------|-------|-----|
| `XSDB6` SQLState | Derby DB locked | Delete `.lck` files in `LibraryDB/` |
| `23505` SQLState | Duplicate primary key | Use a unique ID |
| `ClassNotFoundException` | `derby.jar` missing | Add it to classpath |

---

## References

- [Apache Derby Documentation](https://db.apache.org/derby/docs/)
- [Oracle JDBC Tutorial](https://docs.oracle.com/javase/tutorial/jdbc/)
- Database System Concepts — Silberschatz, Korth & Sudarshan

---

## License

This project is submitted as academic coursework for CSE3488 at ITER, SOA University.
