
# Database Modeling - Relational Design
## Top-Down Modeling

### Process Steps

1. **Analyze the Description** - Read narrative about the business/application
2. **Identify Entities** - Determine what "things" exist in the system
3. **Discover Attributes** - Find properties that describe each entity
4. **Identify Keys** - Determine candidate, primary, and foreign keys
5. **Define Relationships** - Establish how entities relate to each other


**Identified Elements:**
- **Entities:** students, courses, students_courses (junction)
- **Attributes:** student_id, first_name, last_name, email, course_id, course_name, credits
- **Relationship:** many-to-many (students ↔ courses)

---

## Entity Relationships

Relationships define how entities are connected. There are three main types:

### 1. One-to-One (1:1)

One entity instance relates to exactly one instance of another entity.

**Examples:**
- Person ↔ National Insurance Number
- Person ↔ Passport
- Country ↔ Flag

```sql
CREATE TABLE persons (
    person_id INTEGER PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE passports (
    passport_id INTEGER PRIMARY KEY,
    passport_number VARCHAR(20) UNIQUE,
    person_id INTEGER UNIQUE,  -- UNIQUE enforces 1:1
    FOREIGN KEY (person_id) REFERENCES persons(person_id)
);
```

**Diagram:**
```
Person         Passport
[ID: 1] ——————— [Passport: UK123456]
[ID: 2] ——————— [Passport: US789012]
```

---

### 2. One-to-Many (1:M)

One entity instance relates to multiple instances of another entity.

**Examples:**
- Person → Multiple Addresses
- Sports Team → Multiple Players
- Company → Multiple Departments
- Customer → Multiple Orders

```sql
CREATE TABLE teams (
    team_id INTEGER PRIMARY KEY,
    team_name VARCHAR(100)
);

CREATE TABLE players (
    player_id INTEGER PRIMARY KEY,
    player_name VARCHAR(100),
    team_id INTEGER,  -- Foreign key, not unique
    FOREIGN KEY (team_id) REFERENCES teams(team_id)
);
```

**Diagram:**
```
Team                Players
[Manchester Utd] ——— [Player 1: Rashford]
                 ├── [Player 2: Fernandes]
                 └── [Player 3: Casemiro]
```

---

### 3. Many-to-Many (M:N)

Multiple instances of one entity relate to multiple instances of another entity.

**Examples:**
- Authors ↔ Books (author can write many books, book can have many authors)
- Customers ↔ Products
- Ingredients ↔ Recipes
- Employees ↔ Tasks
- Programmers ↔ Programming Languages

⚠️ **Important:** Relational databases **do not allow direct M:N relationships**. They must be implemented using a **junction table** (also called bridge, link, or associative table).

```sql
-- Without Junction (WRONG - can't do this directly)
-- students M:N courses ❌

-- With Junction (CORRECT)
-- students 1:M students_courses M:1 courses ✅
```

**Diagram:**
```
Students          Students_Courses       Courses
[Alice]    ———┐
[Bob]      ———┼——— [Alice-Math]    ———┬— [Mathematics]
[Charlie]  ———┤    [Alice-Physics] ———┤  [Physics]
               │    [Bob-Math]     ———┤  [Chemistry]
               │    [Bob-Chemistry]———┤
               └——— [Charlie-Physics]─┘
```

---

## Keys - Primary and Foreign

### Primary Keys

**Definition:** A single (or composite) attribute that **uniquely identifies** an entity instance.

**Requirements:**
- Must be **unique** for each record
- Must have a **value** (cannot be NULL)
- Should be **stable** (not change over time)

**Analogy:** Like a house key - unique and opens access to specific data

```sql
CREATE TABLE students (
    student_id INTEGER PRIMARY KEY AUTOINCREMENT,  -- Primary Key
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100)
);
```

**Why student_id and not name?**
- Names can change (marriage, legal name change)
- Names can be duplicated (two "John Smith"s)
- Identifiers must remain **stable**

---

### Foreign Keys

**Definition:** An attribute in one table that references the primary key of another table.

**Purpose:**
- Implements relationships between entities
- Maps to the primary key value in the parent table
- Enforces **referential integrity**

**Analogy:** Like a passport visa - grants access to another "country" (table)

```sql
CREATE TABLE courses (
    course_id INTEGER PRIMARY KEY AUTOINCREMENT,
    course_name VARCHAR(100)
);

CREATE TABLE enrollments (
    enrollment_id INTEGER PRIMARY KEY AUTOINCREMENT,
    student_id INTEGER,  -- Foreign Key
    course_id INTEGER,   -- Foreign Key
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

**Relationship Flow:**
``` sql
Parent Table (Primary Key)  →  Child Table (Foreign Key)

courses.course_id  ——→  enrollments.course_id
students.student_id ——→  enrollments.student_id
```

**Referential Integrity:**
- Every foreign key value **must** correspond to an existing primary key
- Cannot insert enrollment with non-existent student_id
- Maintains data consistency

---

## Junction Tables

Junction tables (bridge/link/associative tables) solve the many-to-many relationship problem.

### Why Junction Tables?

Relational databases cannot directly represent M:N relationships in a single table structure. Junction tables break M:N into two 1:M relationships.

### Structure

**Junction Table Contains:**
1. Foreign key to first entity (1:M)
2. Foreign key to second entity (1:M)
3. Optional additional attributes (enrollment date, grade, etc.)
4. Optional primary key (composite or separate)

### Visual Model

```
Entity A ——— 1:M ——— Junction Table ——— M:1 ——— Entity B

This represents: Entity A M:N Entity B
```

---

## Scenario 1: Students-Courses

### Business Requirements

> Students can enroll in multiple courses. Each course can have many students. We need to track enrollment dates and grades.

### Entity Analysis

**Entities:**
- **Students** (main entity)
- **Courses** (main entity)
- **Students_Courses** (junction/associative entity)

**Relationship:** Many-to-Many (M:N)

### Entity-Relationship Diagram

```
┌─────────────┐              ┌──────────────────┐              ┌─────────────┐
│  Students   │              │ Students_Courses │              │   Courses   │
├─────────────┤              ├──────────────────┤              ├─────────────┤
│ student_id  │PK         ┌──│ student_id      │FK         ┌──│ course_id   │PK
│ first_name  │           │  │ course_id       │FK         │  │ course_name │
│ last_name   │           │  │ enrollment_date │           │  │ credits     │
│ email       │           │  │ grade           │           │  └─────────────┘
└─────────────┘           │  └──────────────────┘           │
       │                  │           │                     │
       └──────────────────┘           └─────────────────────┘
            1:M                             M:1
```

### Complete SQL Implementation

```sql
-- ========================================
-- STUDENTS-COURSES DATABASE IMPLEMENTATION
-- ========================================

-- Drop tables if they exist (in reverse order due to foreign keys)
DROP TABLE IF EXISTS students_courses;
DROP TABLE IF EXISTS courses;
DROP TABLE IF EXISTS students;

-- ========================================
-- 1. STUDENTS TABLE
-- ========================================
CREATE TABLE students (
    student_id INTEGER PRIMARY KEY AUTOINCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);

-- ========================================
-- 2. COURSES TABLE
-- ========================================
CREATE TABLE courses (
    course_id INTEGER PRIMARY KEY AUTOINCREMENT,
    course_name VARCHAR(100) NOT NULL,
    credits INTEGER NOT NULL
);

-- ========================================
-- 3. JUNCTION TABLE (STUDENTS_COURSES)
-- ========================================
CREATE TABLE students_courses (
    student_id INTEGER NOT NULL,
    course_id INTEGER NOT NULL,
    enrollment_date DATE DEFAULT CURRENT_DATE,
    grade VARCHAR(2),
    PRIMARY KEY (student_id, course_id),  -- Composite primary key
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);

-- ========================================
-- SAMPLE DATA
-- ========================================

-- Insert students
INSERT INTO students (first_name, last_name, email) VALUES
    ('Alice', 'Johnson', 'alice.j@university.edu'),
    ('Bob', 'Smith', 'bob.smith@university.edu'),
    ('Charlie', 'Brown', 'charlie.b@university.edu'),
    ('Diana', 'Prince', 'diana.p@university.edu');

-- Insert courses
INSERT INTO courses (course_name, credits) VALUES
    ('Database Systems', 15),
    ('Web Development', 15),
    ('Data Structures', 20),
    ('Machine Learning', 20),
    ('Software Engineering', 15);

-- Insert enrollments
INSERT INTO students_courses (student_id, course_id, enrollment_date, grade) VALUES
    (1, 1, '2024-09-01', 'A'),
    (1, 2, '2024-09-01', 'B+'),
    (1, 4, '2024-09-01', NULL),  -- Still in progress
    (2, 1, '2024-09-01', 'B'),
    (2, 3, '2024-09-01', 'A-'),
    (3, 2, '2024-09-01', 'A'),
    (3, 3, '2024-09-01', 'B+'),
    (3, 5, '2024-09-01', 'A-'),
    (4, 1, '2024-09-01', 'A+'),
    (4, 4, '2024-09-01', NULL);

-- ========================================
-- USEFUL QUERIES
-- ========================================

-- Query 1: List all courses for a specific student
SELECT 
    s.first_name,
    s.last_name,
    c.course_name,
    sc.grade
FROM students s
JOIN students_courses sc ON s.student_id = sc.student_id
JOIN courses c ON sc.course_id = c.course_id
WHERE s.student_id = 1;

-- Query 2: List all students in a specific course
SELECT 
    c.course_name,
    s.first_name,
    s.last_name,
    sc.grade
FROM courses c
JOIN students_courses sc ON c.course_id = sc.course_id
JOIN students s ON sc.student_id = s.student_id
WHERE c.course_id = 1
ORDER BY s.last_name;

-- Query 3: Count enrollments per course
SELECT 
    c.course_name,
    COUNT(*) AS student_count
FROM courses c
JOIN students_courses sc ON c.course_id = sc.course_id
GROUP BY c.course_name
ORDER BY student_count DESC;

-- Query 4: Find students with no enrollments
SELECT s.first_name, s.last_name
FROM students s
LEFT JOIN students_courses sc ON s.student_id = sc.student_id
WHERE sc.student_id IS NULL;

-- Query 5: Calculate average grade per course (A=4, B=3, etc.)
SELECT 
    c.course_name,
    AVG(
        CASE 
            WHEN sc.grade LIKE 'A%' THEN 4
            WHEN sc.grade LIKE 'B%' THEN 3
            WHEN sc.grade LIKE 'C%' THEN 2
            WHEN sc.grade LIKE 'D%' THEN 1
            ELSE NULL
        END
    ) AS average_gpa
FROM courses c
JOIN students_courses sc ON c.course_id = sc.course_id
WHERE sc.grade IS NOT NULL
GROUP BY c.course_name;
```

### Key Points

- **Composite Primary Key:** `(student_id, course_id)` ensures no duplicate enrollments
- **Additional Attributes:** `enrollment_date` and `grade` add context to relationship
- **Referential Integrity:** Foreign keys prevent invalid enrollments
- **Flexibility:** Easy to add/remove enrollments without affecting students or courses

---

## Scenario 2: Hotel Booking System

### Business Requirements

> A hotel chain owns a number of boutique hotels around the world. Each hotel has rooms. Reservations are accepted from individual customers and account customers (accounts receive a discount). A reservation may be for more than one room and is associated with a payment.

### Entity Analysis

**Entities Identified:**
1. **Hotels** - Boutique hotels in the chain
2. **Rooms** - Individual rooms in each hotel
3. **Customers** - People making reservations
4. **Accounts** - Corporate/regular account holders (discount)
5. **Reservations** - Booking records
6. **Reservation_Rooms** - Junction table (M:N between reservations and rooms)
7. **Payments** - Payment information

### Relationships

```
Hotels 1:M Rooms
Customers 1:M Reservations
Accounts 1:M Reservations
Reservations M:N Rooms (via Reservation_Rooms)
Reservations 1:1 Payments
```

### Entity-Relationship Diagram

```
┌──────────┐        ┌──────────┐
│  Hotels  │1      M│  Rooms   │
│──────────│────────│──────────│
│ hotel_id │        │ room_id  │
│ name     │        │ hotel_id │FK
│ location │        │ room_num │
└──────────┘        │ type     │
                    │ price    │
                    └──────────┘
                          │
                          │ M
                          │
┌────────────┐            │           ┌─────────────────┐
│ Customers  │1           │          M│ Reservation_    │
│────────────│────┐       │      ┌────│ Rooms           │
│customer_id │    │       │      │    │─────────────────│
│ name       │    │       │      │    │ reservation_id  │FK
│ email      │    │       │      │    │ room_id         │FK
└────────────┘    │       │      │    │ check_in        │
                  │       │      │    │ check_out       │
┌────────────┐    │       │      │    └─────────────────┘
│  Accounts  │1   │      M│      │              │
│────────────│────┤  ┌────┴────┐ │              │
│ account_id │    └──│Reserves │─┘              │
│ company    │       │─────────│                │
│ discount   │       │ res_id  │                │
└────────────┘       │cust_id  │FK              │
                     │acct_id  │FK              │
                     │ date    │                │
                     └─────────┘                │
                          │1                    │
                          │                     │
                          │1                    │
                     ┌────┴─────┐               │
                     │ Payments │               │
                     │──────────│               │
                     │payment_id│               │
                     │ res_id   │FK             │
                     │ amount   │               │
                     │ method   │               │
                     └──────────┘               │
```

### Partial SQL Implementation

```sql
-- ========================================
-- HOTEL BOOKING SYSTEM - PARTIAL IMPLEMENTATION
-- ========================================

-- Drop tables in reverse order (foreign key dependencies)
DROP TABLE IF EXISTS reservation_rooms;
DROP TABLE IF EXISTS payments;
DROP TABLE IF EXISTS reservations;
DROP TABLE IF EXISTS rooms;
DROP TABLE IF EXISTS hotels;
DROP TABLE IF EXISTS accounts;
DROP TABLE IF EXISTS customers;

-- ========================================
-- 1. HOTELS TABLE
-- ========================================
CREATE TABLE IF NOT EXISTS hotels (
    hotel_id INTEGER PRIMARY KEY AUTOINCREMENT,
    hotel_name VARCHAR(100) NOT NULL,
    location VARCHAR(100) NOT NULL,
    star_rating INTEGER
);

-- ========================================
-- 2. ROOMS TABLE (1:M with Hotels)
-- ========================================
CREATE TABLE IF NOT EXISTS rooms (
    room_id INTEGER PRIMARY KEY AUTOINCREMENT,
    hotel_id INTEGER NOT NULL,
    room_number VARCHAR(10) NOT NULL,
    room_type VARCHAR(50) NOT NULL,  -- Single, Double, Suite
    price_per_night DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (hotel_id) REFERENCES hotels(hotel_id),
    UNIQUE(hotel_id, room_number)  -- Room numbers unique per hotel
);

-- ========================================
-- 3. CUSTOMERS TABLE
-- ========================================
CREATE TABLE IF NOT EXISTS customers (
    customer_id INTEGER PRIMARY KEY AUTOINCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20)
);

-- ========================================
-- 4. ACCOUNTS TABLE (Corporate Customers)
-- ========================================
CREATE TABLE IF NOT EXISTS accounts (
    account_id INTEGER PRIMARY KEY AUTOINCREMENT,
    company_name VARCHAR(100) NOT NULL,
    contact_email VARCHAR(100) NOT NULL,
    discount_percentage DECIMAL(5, 2) DEFAULT 0.00
);

-- ========================================
-- 5. RESERVATIONS TABLE
-- ========================================
CREATE TABLE IF NOT EXISTS reservations (
    reservation_id INTEGER PRIMARY KEY AUTOINCREMENT,
    customer_id INTEGER,
    account_id INTEGER,  -- NULL if individual, set if corporate
    reservation_date DATE DEFAULT CURRENT_DATE,
    status VARCHAR(20) DEFAULT 'Confirmed',  -- Confirmed, Cancelled, Completed
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (account_id) REFERENCES accounts(account_id),
    CHECK (customer_id IS NOT NULL OR account_id IS NOT NULL)
);

-- ========================================
-- 6. RESERVATION_ROOMS (Junction Table)
-- Implements M:N between Reservations and Rooms
-- ========================================
CREATE TABLE IF NOT EXISTS reservation_rooms (
    reservation_id INTEGER NOT NULL,
    room_id INTEGER NOT NULL,
    check_in_date DATE NOT NULL,
    check_out_date DATE NOT NULL,
    PRIMARY KEY (reservation_id, room_id),
    FOREIGN KEY (reservation_id) REFERENCES reservations(reservation_id),
    FOREIGN KEY (room_id) REFERENCES rooms(room_id),
    CHECK (check_out_date > check_in_date)
);

-- ========================================
-- 7. PAYMENTS TABLE (1:1 with Reservations)
-- ========================================
CREATE TABLE IF NOT EXISTS payments (
    payment_id INTEGER PRIMARY KEY AUTOINCREMENT,
    reservation_id INTEGER UNIQUE NOT NULL,  -- UNIQUE enforces 1:1
    amount DECIMAL(10, 2) NOT NULL,
    payment_method VARCHAR(50),  -- Credit Card, Cash, Bank Transfer
    payment_date DATE DEFAULT CURRENT_DATE,
    FOREIGN KEY (reservation_id) REFERENCES reservations(reservation_id)
);

-- ========================================
-- SAMPLE DATA
-- ========================================

-- Insert hotels
INSERT INTO hotels (hotel_name, location, star_rating) VALUES
    ('Grand Plaza Hotel', 'London, UK', 5),
    ('Sunset Beach Resort', 'Maldives', 5),
    ('City Center Inn', 'New York, USA', 4);

-- Insert rooms
INSERT INTO rooms (hotel_id, room_number, room_type, price_per_night) VALUES
    (1, '101', 'Single', 150.00),
    (1, '102', 'Double', 220.00),
    (1, '201', 'Suite', 450.00),
    (2, '1A', 'Beach Villa', 800.00),
    (2, '2A', 'Beach Villa', 800.00),
    (3, '305', 'Single', 120.00);

-- Insert customers
INSERT INTO customers (first_name, last_name, email, phone) VALUES
    ('John', 'Doe', 'john.doe@email.com', '+44-123-456789'),
    ('Jane', 'Smith', 'jane.smith@email.com', '+44-987-654321'),
    ('Mike', 'Johnson', 'mike.j@email.com', '+1-555-0123');

-- Insert accounts (corporate)
INSERT INTO accounts (company_name, contact_email, discount_percentage) VALUES
    ('TechCorp International', 'bookings@techcorp.com', 15.00),
    ('Global Consulting Ltd', 'travel@globalcons.com', 20.00);

-- Insert reservations (mix of individual and corporate)
INSERT INTO reservations (customer_id, account_id, reservation_date, status) VALUES
    (1, NULL, '2026-02-15', 'Confirmed'),      -- Individual booking
    (2, 1, '2026-02-16', 'Confirmed'),         -- Corporate booking
    (3, NULL, '2026-02-17', 'Confirmed');      -- Individual booking

-- Insert reservation_rooms (junction)
INSERT INTO reservation_rooms (reservation_id, room_id, check_in_date, check_out_date) VALUES
    (1, 1, '2026-03-01', '2026-03-05'),  -- John: Room 101 for 4 nights
    (2, 2, '2026-03-10', '2026-03-12'),  -- Jane: Room 102 for 2 nights
    (2, 3, '2026-03-10', '2026-03-12'),  -- Jane: Also Room 201 (multiple rooms)
    (3, 4, '2026-04-01', '2026-04-07');  -- Mike: Beach villa for 6 nights

-- Insert payments
INSERT INTO payments (reservation_id, amount, payment_method, payment_date) VALUES
    (1, 600.00, 'Credit Card', '2026-02-15'),
    (2, 1139.20, 'Bank Transfer', '2026-02-16'),  -- With 15% discount
    (3, 4800.00, 'Credit Card', '2026-02-17');

-- ========================================
-- Display tables (SQLite specific)
-- ========================================
.mode column
.headers on

.print "========== HOTELS =========="
SELECT * FROM hotels;

.print "\n========== ROOMS =========="
SELECT * FROM rooms;

.print "\n========== CUSTOMERS =========="
SELECT * FROM customers;

.print "\n========== RESERVATIONS =========="
SELECT * FROM reservations;

.print "\n========== RESERVATION_ROOMS (JUNCTION) =========="
SELECT * FROM reservation_rooms;

-- ========================================
-- USEFUL QUERIES
-- ========================================

-- Query 1: Show all reservations with customer details
.print "\n========== ALL RESERVATIONS WITH DETAILS =========="
SELECT 
    r.reservation_id,
    c.first_name || ' ' || c.last_name AS customer_name,
    CASE 
        WHEN a.account_id IS NOT NULL THEN a.company_name 
        ELSE 'Individual' 
    END AS booking_type,
    r.reservation_date,
    r.status
FROM reservations r
JOIN customers c ON r.customer_id = c.customer_id
LEFT JOIN accounts a ON r.account_id = a.account_id;

-- Query 2: Show all rooms for a specific reservation
.print "\n========== ROOMS FOR RESERVATION #2 =========="
SELECT 
    h.hotel_name,
    ro.room_number,
    ro.room_type,
    rr.check_in_date,
    rr.check_out_date,
    (julianday(rr.check_out_date) - julianday(rr.check_in_date)) AS nights,
    ro.price_per_night,
    (julianday(rr.check_out_date) - julianday(rr.check_in_date)) * ro.price_per_night AS total_cost
FROM reservation_rooms rr
JOIN rooms ro ON rr.room_id = ro.room_id
JOIN hotels h ON ro.hotel_id = h.hotel_id
WHERE rr.reservation_id = 2;

-- Query 3: Find available rooms for a date range
.print "\n========== AVAILABLE ROOMS (March 1-5, 2026) =========="
SELECT 
    h.hotel_name,
    r.room_number,
    r.room_type,
    r.price_per_night
FROM rooms r
JOIN hotels h ON r.hotel_id = h.hotel_id
WHERE r.room_id NOT IN (
    SELECT room_id 
    FROM reservation_rooms
    WHERE (check_in_date <= '2026-03-05' AND check_out_date >= '2026-03-01')
);

-- Query 4: Calculate total revenue per hotel
.print "\n========== REVENUE BY HOTEL =========="
SELECT 
    h.hotel_name,
    COUNT(DISTINCT rr.reservation_id) AS total_bookings,
    SUM((julianday(rr.check_out_date) - julianday(rr.check_in_date)) * ro.price_per_night) AS total_revenue
FROM hotels h
JOIN rooms ro ON h.hotel_id = ro.hotel_id
JOIN reservation_rooms rr ON ro.room_id = rr.room_id
GROUP BY h.hotel_name
ORDER BY total_revenue DESC;
```

### Key Design Features

**Junction Table (reservation_rooms):**
- Solves M:N relationship between reservations and rooms
- One reservation can have multiple rooms
- Stores check-in/check-out dates per room

**Flexible Customer Types:**
- Individual customers vs. corporate accounts
- Accounts get discount percentage
- Enforced by CHECK constraint: must have customer OR account

**Referential Integrity:**
- Foreign keys ensure valid relationships
- Cannot book non-existent rooms
- Cannot create payment without reservation

**Date Validation:**
- CHECK constraint ensures check_out > check_in
- Prevents logical errors

---

## SQL Keywords Reference

### DDL (Data Definition Language)

| Keyword | Purpose | Example |
|---------|---------|---------|
| `CREATE` | Create new table | `CREATE TABLE students (...)` |
| `DROP` | Delete table | `DROP TABLE students` |
| `IF EXISTS` | Conditional drop | `DROP TABLE IF EXISTS students` |
| `AUTOINCREMENT` | Auto-generate IDs | `id INTEGER PRIMARY KEY AUTOINCREMENT` |
| `PRIMARY KEY` | Define primary key | `student_id INTEGER PRIMARY KEY` |
| `FOREIGN KEY` | Define foreign key | `FOREIGN KEY (student_id) REFERENCES students(student_id)` |
| `REFERENCES` | Specify parent table | `FOREIGN KEY (...) REFERENCES table(column)` |
| `UNIQUE` | Enforce uniqueness | `email VARCHAR(100) UNIQUE` |
| `NOT NULL` | Require value | `name VARCHAR(50) NOT NULL` |
| `CHECK` | Validation constraint | `CHECK (price > 0)` |
| `DEFAULT` | Default value | `status VARCHAR(20) DEFAULT 'Active'` |

### Data Types

| Type | Description | Example |
|------|-------------|---------|
| `INTEGER` | Whole numbers | `student_id INTEGER` |
| `VARCHAR(n)` | Variable-length text | `name VARCHAR(100)` |
| `TEXT` | Unlimited text | `description TEXT` |
| `DECIMAL(p,s)` | Fixed-point decimal | `price DECIMAL(10,2)` |
| `DATE` | Date value | `enrollment_date DATE` |
| `BOOLEAN` | True/False | `is_active BOOLEAN` |

### SQLite Utilities (Dot Commands)

| Command | Purpose |
|---------|---------|
| `.mode column` | Display in columns |
| `.headers on` | Show column headers |
| `.print "text"` | Print text to output |
| `.schema table` | Show table structure |

---

## Best Practices Checklist

### Database Design
- ✅ Use meaningful, descriptive table and column names
- ✅ Always define primary keys
- ✅ Use foreign keys to enforce relationships
- ✅ Normalize data to avoid redundancy
- ✅ Use junction tables for M:N relationships
- ✅ Choose stable identifiers (IDs, not names)

### SQL Scripts
- ✅ Drop tables in reverse order (child → parent)
- ✅ Create tables in correct order (parent → child)
- ✅ Use `IF EXISTS` to make scripts rerunnable
- ✅ Add comments to explain complex logic
- ✅ Use AUTOINCREMENT for surrogate keys
- ✅ Define constraints (NOT NULL, UNIQUE, CHECK)

### Junction Tables
- ✅ Name clearly (entity1_entity2 or descriptive name)
- ✅ Include both foreign keys
- ✅ Consider composite primary key
- ✅ Add additional attributes as needed
- ✅ Enforce referential integrity

---

## Common Patterns

### Pattern 1: Basic Junction Table
```sql
CREATE TABLE table1_table2 (
    table1_id INTEGER NOT NULL,
    table2_id INTEGER NOT NULL,
    PRIMARY KEY (table1_id, table2_id),
    FOREIGN KEY (table1_id) REFERENCES table1(id),
    FOREIGN KEY (table2_id) REFERENCES table2(id)
);
```

### Pattern 2: Junction with Additional Data
```sql
CREATE TABLE enrollments (
    student_id INTEGER NOT NULL,
    course_id INTEGER NOT NULL,
    enrollment_date DATE DEFAULT CURRENT_DATE,
    grade VARCHAR(2),
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

### Pattern 3: Query Across Junction
```sql
-- Get all Y related to specific X
SELECT y.*
FROM table2 y
JOIN table1_table2 j ON y.id = j.table2_id
WHERE j.table1_id = ?;
```

---

## Troubleshooting

### Error: Foreign key constraint failed
**Cause:** Trying to insert child record before parent exists
**Solution:** Insert parent records first, then children

### Error: Unique constraint failed
**Cause:** Trying to insert duplicate value in UNIQUE column
**Solution:** Check existing data, use different value

### Error: Table already exists
**Cause:** Trying to CREATE table that exists
**Solution:** Use `DROP TABLE IF EXISTS` first or `CREATE TABLE IF NOT EXISTS`

---

## Further Learning

**Next Topics to Explore:**
- [[SQL-Joins-Detailed]] - Inner, outer, cross joins
- [[Database-Normalization]] - 1NF, 2NF, 3NF
- [[SQL-Indexes]] - Performance optimization
- [[Database-Transactions]] - ACID properties
- [[SQL-Views]] - Virtual tables
- [[Database-Security]] - User permissions, SQL injection

---

**Tags:** #database #sql #modeling #relationships #junction-tables #foreign-keys #primary-keys #top-down-design

**Related:** [[SQL-Keywords-Data-Retrieval]], [[SQL-Basics]], [[Entity-Relationship-Diagrams]]