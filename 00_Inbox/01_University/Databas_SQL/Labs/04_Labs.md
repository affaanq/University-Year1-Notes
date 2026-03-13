
# 🗄️ From Flat Files to Relational Databases with Simple Relationships

> [!info] **Course Context**
> This lab transitions from storing everything in a single "flat" table to a properly normalized relational database using SQLite. We model **astronauts** and their **missions** — a classic many-to-many relationship.

## 1 — The Flat File Starting Point

### What Is a Flat File?

A **flat file** is a single table that holds *all* of your data — every attribute, every relationship, every piece of information — crammed into one place.

Think of it like a **giant spreadsheet** where every column you could possibly need is jammed side by side.

```
| Name           | Year | Group | Status  | Missions              | Flights | Hours  |
|----------------|------|-------|---------|------------------------|---------|--------|
| Neil Armstrong | 1962 | 2     | Retired | Gemini 8, Apollo 11    | 2       | 8820.5 |
| Buzz Aldrin    | 1963 | 3     | Retired | Gemini 12, Apollo 11   | 2       | 7935.2 |
```

> [!tip] **Analogy: The Junk Drawer**
> A flat file is like a kitchen junk drawer. Everything is *in there* — batteries, tape, scissors, menus — but finding something specific is slow, and things get tangled up. A relational database is like having **separate, labeled containers** for each category.

### Why Start Here?

Flat files are **beginner-friendly** because:
- You can load data in one step
- You can immediately run `SELECT` and `WHERE` queries
- There's no need to understand keys or joins yet

### The Problems (Even in This Simple Case)

| Problem | Explanation |
|---|---|
| **No primary key** | If two astronauts share the same name, there's no guaranteed way to tell them apart. |
| **No linking capability** | You can't connect this table to other tables meaningfully. |
| **Missions stored as text/lists** | `"Gemini 8, Apollo 11"` is a *string*, not queryable data. You can't ask "who flew on Apollo 11?" without messy string parsing. |
| **Data duplication** | The mission name "Apollo 11" is repeated for every astronaut who flew on it. If the name changes, you'd have to update *every* row. |
| **Update anomalies** | Changing one fact (e.g., a mission name) requires changing it in many places — miss one and your data is inconsistent. |

> [!danger] **The Cardinal Sin of Relational Design**
> Storing **lists or tuples inside a single column** (e.g., `"Gemini 8, Apollo 11"`) violates the principles of relational design. Each cell should hold **one atomic value**.

---

## 2 — Why Flat Files Break Down

Let's make this concrete with a scenario.

### Scenario: "Which astronauts flew on Apollo 11?"

**In the flat file:**
```sql
-- You'd have to do something ugly like:
SELECT name FROM astronauts WHERE missions LIKE '%Apollo 11%';
```

This is **fragile** because:
- `LIKE '%Apollo 11%'` would also match `"Apollo 110"` or `"Apollo 11-B"` if they existed
- You're relying on consistent formatting (spaces, commas, etc.)
- The database engine can't use indexes efficiently on `LIKE '%...'`

**In a relational database:**
```sql
SELECT a.name
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
JOIN missions m ON am.mission_id = m.mission_id
WHERE m.name = 'Apollo 11';
```

This is **precise**: you're matching against a dedicated, indexed column with exact values.

> [!tip] **Analogy: Finding a Book**
> - **Flat file** = a pile of books on the floor. To find one, you dig through everything.
> - **Relational database** = a library with a card catalog. You look up the book by its ID, and the catalog tells you exactly where it is.

---

## 3 — The Relational Model (Core Philosophy)

### The Big Idea

> **Different real-world "things" should be stored in separate tables, each with a primary key. Relationships between things are represented using foreign keys.**

This is the **single most important sentence** in this entire lecture. Everything else flows from it.

### The Three Rules

```
┌──────────────────────────────────────────────────────────┐
│  RULE 1: Separate entities into separate tables          │
│          (astronauts ≠ missions — they're different      │
│           real-world things)                             │
│                                                          │
│  RULE 2: Every table gets a primary key                  │
│          (a unique identifier for each row)              │
│                                                          │
│  RULE 3: Relationships use foreign keys                  │
│          (keys that "point to" rows in other tables)     │
└──────────────────────────────────────────────────────────┘
```

### What We're Building

```
┌─────────────────┐       ┌──────────────────────┐       ┌─────────────────┐
│   astronauts    │       │  astronauts_missions │       │    missions     │
│─────────────────│       │──────────────────────│       │─────────────────│
│ astronaut_id PK │──┐    │ astronaut_id FK,PK   │    ┌──│ mission_id PK   │
│ name            │  └───>│ mission_id   FK,PK   │<───┘  │ name (UNIQUE)   │
│ year            │       └──────────────────────┘       └─────────────────┘
│ group_num       │
│ status          │
│ birth_date      │
│ gender          │
│ ...             │
└─────────────────┘
```

This is a **three-table schema** that avoids every problem the flat file had.

---

## 4 — Primary Keys — The Identity of Every Row

### What Is a Primary Key?

A **primary key** (PK) is a column (or set of columns) that **uniquely identifies** every row in a table. No two rows can ever share the same primary key value, and it can never be `NULL`.

```sql
CREATE TABLE astronauts (
    astronaut_id INTEGER PRIMARY KEY,  -- unique ID for each astronaut
    name TEXT NOT NULL,
    ...
);
```

### Why Do We Need Them?

| Without PK | With PK |
|---|---|
| Two "John Smith" astronauts are indistinguishable | `astronaut_id = 47` vs `astronaut_id = 112` — always unique |
| Can't reliably link to other tables | Foreign keys reference the PK — guaranteed to find the right row |
| Deletion/update might affect the wrong row | PK ensures you target *exactly* the row you mean |

> [!tip] **Analogy: Student ID Number**
> Your name might not be unique in a university, but your **student ID** is. That's what a primary key does — it gives every row its own "ID number."

### Types of Primary Keys

| Type | Description | Example |
|---|---|---|
| **Surrogate key** | An artificial, auto-generated integer (most common) | `astronaut_id INTEGER PRIMARY KEY` |
| **Natural key** | A real-world value that happens to be unique | `email TEXT PRIMARY KEY` (risky — emails can change) |
| **Composite key** | Multiple columns together form the key | `PRIMARY KEY (astronaut_id, mission_id)` |

In this lab, `astronaut_id` and `mission_id` are **surrogate keys**. The junction table uses a **composite key**.

---

## 5 — Foreign Keys — The Glue Between Tables

### What Is a Foreign Key?

A **foreign key** (FK) is a column in one table that **references the primary key** of another table. It creates a link between the two tables.

```sql
CREATE TABLE astronauts_missions (
    astronaut_id INTEGER,
    mission_id INTEGER,
    FOREIGN KEY (astronaut_id) REFERENCES astronauts(astronaut_id),
    FOREIGN KEY (mission_id) REFERENCES missions(mission_id),
    PRIMARY KEY (astronaut_id, mission_id)
);
```

### What Does "Referential Integrity" Mean?

Foreign keys **enforce referential integrity** — they guarantee that every value in the FK column actually exists in the referenced table.

```
astronauts_missions:
| astronaut_id | mission_id |
|--------------|------------|
| 1            | 5          |  ✅ astronaut 1 exists, mission 5 exists
| 999          | 5          |  ❌ REJECTED if astronaut 999 doesn't exist
```

> [!warning] **SQLite Gotcha**
> In SQLite, foreign key enforcement is **OFF by default**. You must enable it:
> ```sql
> PRAGMA foreign_keys = ON;
> ```
> Without this, SQLite will happily let you insert orphaned foreign key values.

> [!tip] **Analogy: Concert Tickets**
> A foreign key is like a **concert ticket** that references a specific event. If the event (PK) doesn't exist, the ticket (FK) is invalid. The system refuses to issue a ticket for a non-existent concert.

---

## 6 — Many-to-Many Relationships and Junction Tables

### The Relationship

- One **astronaut** can fly on **many missions** (Neil Armstrong → Gemini 8, Apollo 11)
- One **mission** can include **many astronauts** (Apollo 11 → Armstrong, Aldrin, Collins)

This is a **many-to-many (M:N) relationship**.

### Why Can't We Just Add a Column?

You *can't* put a `mission_id` column in the `astronauts` table because each astronaut has *multiple* missions. And you can't put an `astronaut_id` column in the `missions` table because each mission has *multiple* astronauts.

**Neither side can hold a single foreign key.**

### The Solution: A Junction (Link) Table

```
┌────────────┐          ┌─────────────────────┐          ┌────────────┐
│ astronauts │ 1 ──── M │ astronauts_missions  │ M ──── 1│  missions  │
│            │          │                     │          │            │
│ PK: id     │◄─────────│ FK: astronaut_id    │          │ PK: id     │
│            │          │ FK: mission_id      │─────────►│            │
└────────────┘          └─────────────────────┘          └────────────┘
```

The junction table (`astronauts_missions`) sits *between* the two entity tables and:
- Contains **two foreign keys** (one to each entity)
- Has a **composite primary key** `(astronaut_id, mission_id)` so each astronaut–mission pair appears **only once**
- Contains **no other data** (though in other designs, you *could* add attributes of the relationship itself, like `role_on_mission`)

### Concrete Example

```
astronauts:                 missions:                astronauts_missions:
| id | name          |     | id | name       |     | astronaut_id | mission_id |
|----|---------------|     |----|------------|     |--------------|------------|
| 1  | Neil Armstrong|     | 1  | Gemini 8   |     | 1            | 1          |
| 2  | Buzz Aldrin   |     | 2  | Gemini 12  |     | 1            | 3          |
| 3  | Michael Collins|    | 3  | Apollo 11  |     | 2            | 2          |
                                                    | 2            | 3          |
                                                    | 3            | 3          |
```

Now you can ask:
- **"Who flew on Apollo 11?"** → Look up `mission_id = 3` in the junction table → get astronaut IDs 1, 2, 3 → look up their names
- **"What missions did Buzz Aldrin fly?"** → Look up `astronaut_id = 2` → get mission IDs 2, 3 → look up mission names

> [!tip] **Analogy: University Enrollment**
> - **Students** table (like astronauts)
> - **Courses** table (like missions)
> - **Enrollments** table (like the junction table)
> A student takes many courses. A course has many students. The enrollment table links them.

---

## 7 — The Astronauts Relational Schema (Full Walkthrough)

### Table 1: `astronauts`

```sql
CREATE TABLE IF NOT EXISTS astronauts (
    astronaut_id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    year INTEGER,
    group_num INTEGER,
    status TEXT,
    birth_date TEXT,
    birth_place TEXT,
    gender TEXT,
    alma_mater TEXT,
    undergraduate_major TEXT,
    graduate_major TEXT,
    military_rank TEXT,
    military_branch TEXT,
    space_flights INTEGER,
    space_flight_hours REAL,
    space_walks INTEGER,
    space_walk_hours REAL,
    death_date TEXT,
    death_mission TEXT
);
```

**Key design notes:**
- `astronaut_id` is a **surrogate primary key** — auto-incremented integer
- `name` is `NOT NULL` — every astronaut must have a name
- Numeric fields like `space_flights`, `space_flight_hours` store **aggregated statistics** about the astronaut
- No missions column — missions are linked via the junction table

### Table 2: `missions`

```sql
CREATE TABLE IF NOT EXISTS missions (
    mission_id INTEGER PRIMARY KEY,
    name TEXT NOT NULL UNIQUE
);
```

**Key design notes:**
- `mission_id` is the primary key
- `name` is `UNIQUE` — you can't accidentally insert "Apollo 11" twice
- This is a **simple lookup table** (just an ID and a name)

### Table 3: `astronauts_missions` (Junction Table)

```sql
CREATE TABLE IF NOT EXISTS astronauts_missions (
    astronaut_id INTEGER,
    mission_id INTEGER,
    FOREIGN KEY (astronaut_id) REFERENCES astronauts(astronaut_id),
    FOREIGN KEY (mission_id) REFERENCES missions(mission_id),
    PRIMARY KEY (astronaut_id, mission_id)
);
```

**Key design notes:**
- **Composite primary key** `(astronaut_id, mission_id)` — prevents duplicate pairings
- Two **foreign keys** enforce referential integrity
- No additional columns (pure link table)

---

## 8 — Loading Data (Header-less CSVs in SQLite)

### The Problem

The CSV files have **no header row**. SQLite's `.import` command normally treats the first row as data, but if you have headers, you'd need to skip them. Since we *don't* have headers here, the import is straightforward — but we must tell SQLite which table to load into.

### The Workflow

The lab uses a **modular approach** with separate SQL scripts:

``` sql
create_astronauts.sql      → CREATE TABLE IF NOT EXISTS astronauts (...)
create_missions.sql        → CREATE TABLE IF NOT EXISTS missions (...)
create_astronauts_missions.sql → CREATE TABLE IF NOT EXISTS astronauts_missions (...)

load_astronauts.sql        → .mode csv  /  .import data/astronauts.csv astronauts
load_missions.sql          → .mode csv  /  .import data/missions.csv missions
load_astronauts_missions.sql → .mode csv / .import data/astronauts_missions.csv astronauts_missions

top_level.sql              → .read create_astronauts.sql  (etc., runs everything in order)
```

### Loading Steps (for each table)

```sql
-- load_missions.sql (example)
.mode csv
.import data/missions.csv missions
```

### How to Test Your Load Scripts

```bash
# Step 1: Create the table
sqlite3 astronauts.db < create_missions.sql

# Step 2: Load the data
sqlite3 astronauts.db < load_missions.sql

# Step 3: Verify
sqlite3 astronauts.db "SELECT * FROM missions LIMIT 5;"
```

If you see rows of data, your load script is working.

> [!warning] **Common Pitfalls**
> - **Wrong column order**: The CSV columns must match the table column order exactly.
> - **Data type mismatches**: If a column expects `INTEGER` but the CSV has text, you'll get errors or silent corruption.
> - **Running load twice**: If you re-run the import without clearing the table first, you'll get duplicate rows. Use `DELETE FROM table_name;` before re-importing, or use `DROP TABLE IF EXISTS` before `CREATE TABLE`.

### Modularity Principle

> [!info] **Why Separate Scripts?**
> Keeping `CREATE` and `LOAD` in separate files follows the **Single Responsibility Principle**:
> - Each file does **one thing**
> - You can test each file independently
> - You can re-run loads without re-creating tables (or vice versa)
> - The `top_level.sql` orchestrates everything using `.read`

---
> [!important]
`ON` specifies the **condition that determines how two tables are matched together** during a `JOIN`. It tells SQLite exactly **which column in the left table corresponds to which column in the right table**.
## 9 — JOIN Operations Explained

### What Is a JOIN?

A `JOIN` combines rows from two (or more) tables based on a **related column** — typically a foreign key referencing a primary key.

### INNER JOIN (a.k.a. just `JOIN`)

Returns **only** the rows where a match exists in **both** tables.

```sql
SELECT a.name, m.name AS mission_name
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
JOIN missions m ON am.mission_id = m.mission_id;
```

**Visual:**
```
astronauts           astronauts_missions          missions
┌──────┐             ┌──────┬──────┐              ┌──────┐
│ id=1 │─────────────│ a=1  │ m=3  │──────────────│ id=3 │  ✅ MATCH
│ id=2 │─────────────│ a=2  │ m=3  │──────────────│ id=3 │  ✅ MATCH
│ id=4 │             │      │      │              │ id=7 │  ❌ No link
└──────┘             └──────┴──────┘              └──────┘
```

Astronaut 4 and Mission 7 are **excluded** because they have no link in the junction table.

### LEFT JOIN

Returns **all** rows from the **left** table, and matching rows from the right table. If there's no match, the right-side columns are filled with `NULL`.

```sql
SELECT a.name, COUNT(am.mission_id) AS mission_count
FROM astronauts a
LEFT JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
GROUP BY a.astronaut_id;
```

**Visual:**
```
astronauts (LEFT)     astronauts_missions (RIGHT)
┌──────────────┐      ┌──────────────┐
│ id=1 Armstrong│─────│ a=1, m=3     │  → Armstrong, Apollo 11
│ id=2 Aldrin   │─────│ a=2, m=3     │  → Aldrin, Apollo 11
│ id=4 Candidate│     │ (no match)   │  → Candidate, NULL  ← STILL INCLUDED
└──────────────┘      └──────────────┘
```

> [!tip] **When to Use LEFT JOIN vs JOIN**
> - Use **JOIN** when you only want entities that *have* related data (e.g., "astronauts who have missions")
> - Use **LEFT JOIN** when you want *all* entities, even those without related data (e.g., "all astronauts, even those with zero missions")

> [!tip] **Analogy: Party Guest List**
> - **INNER JOIN** = "Show me only guests who RSVP'd *and* actually showed up"
> - **LEFT JOIN** = "Show me ALL invited guests, and note who actually showed up (mark absent ones as NULL)"

### The ON Clause

`ON` specifies **which columns** to match between the two tables:

```sql
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
--                            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
--                            "Match rows where these values are equal"
```

This is always the **primary key = foreign key** mapping.

---

## 10 — Aggregation Functions (COUNT, SUM, AVG, MIN, MAX)

Aggregation functions **collapse multiple rows** into a single summary value.

### COUNT()

Counts the number of rows (or non-NULL values).

```sql
-- Count all astronauts
SELECT COUNT(*) FROM astronauts;

-- Count astronauts who have space flight hours recorded
SELECT COUNT(space_flight_hours) FROM astronauts;
-- ^ This IGNORES rows where space_flight_hours IS NULL
```

> [!warning] **COUNT(*) vs COUNT(column)**
> - `COUNT(*)` counts **all rows**, including those with NULLs
> - `COUNT(column)` counts only rows where `column IS NOT NULL`
> This distinction matters when dealing with sparse data!

### SUM()

Adds up all values in a column.

```sql
-- Total space flight hours across all astronauts
SELECT SUM(space_flight_hours) FROM astronauts;
```

### AVG()

Calculates the arithmetic mean. **Ignores NULLs.**

```sql
-- Average space flight hours
SELECT AVG(space_flight_hours) FROM astronauts;
```

> [!danger] **NULL Trap with AVG()**
> If 10 astronauts exist but only 7 have `space_flight_hours` recorded, `AVG()` divides by **7**, not 10. This might not be what you want. To include NULLs as zero:
> ```sql
> SELECT AVG(COALESCE(space_flight_hours, 0)) FROM astronauts;
> ```

### MIN() and MAX()

Return the smallest and largest values.

```sql
SELECT MIN(space_flight_hours), MAX(space_flight_hours) FROM astronauts;
```

### ROUND()

Rounds to a specified number of decimal places.

```sql
SELECT ROUND(AVG(space_flight_hours), 2) FROM astronauts;
-- Result: 345.67 (2 decimal places)
```

### ABS() and SQRT()

```sql
SELECT ABS(-42);    -- Returns 42
SELECT SQRT(144);   -- Returns 12.0
```

### Summary Table

| Function | Input | Output | NULL Handling |
|---|---|---|---|
| `COUNT(*)` | All rows | Number of rows | Counts all rows |
| `COUNT(col)` | Column values | Number of non-NULL values | Skips NULLs |
| `SUM(col)` | Numeric column | Total | Skips NULLs |
| `AVG(col)` | Numeric column | Arithmetic mean | Skips NULLs (divides by non-NULL count) |
| `MIN(col)` | Any column | Smallest value | Skips NULLs |
| `MAX(col)` | Any column | Largest value | Skips NULLs |
| `ROUND(val, n)` | Number, decimal places | Rounded number | Returns NULL if input is NULL |
| `ABS(val)` | Number | Absolute value | Returns NULL if input is NULL |
| `SQRT(val)` | Number | Square root | Returns NULL if input is NULL |

---

## 11 — GROUP BY and HAVING

### GROUP BY

`GROUP BY` takes all rows with the **same value** in the specified column(s) and collapses them into **one row per group**. You then use aggregate functions to summarize each group.

```sql
-- How many missions did each astronaut fly?
SELECT a.name, COUNT(am.mission_id) AS mission_count
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
GROUP BY a.astronaut_id;
```

**What happens internally:**

```
Before GROUP BY:                    After GROUP BY:
| name      | mission_id |         | name      | COUNT(mission_id) |
|-----------|------------|         |-----------|-------------------|
| Armstrong | 1          |         | Armstrong | 2                 |
| Armstrong | 3          |   →     | Aldrin    | 2                 |
| Aldrin    | 2          |         | Collins   | 1                 |
| Aldrin    | 3          |
| Collins   | 3          |
```

> [!warning] **The Golden Rule of GROUP BY**
> Every column in `SELECT` must either:
> 1. Be in the `GROUP BY` clause, OR
> 2. Be inside an aggregate function (COUNT, SUM, etc.)
>
> This is **wrong**:
> ```sql
> SELECT name, status, COUNT(*) FROM astronauts GROUP BY name;
> -- status is not in GROUP BY and not aggregated!
> ```

### HAVING

`HAVING` filters **after** grouping. It's like `WHERE`, but for grouped results.

```sql
-- Astronauts with more than 1 mission
SELECT a.name, COUNT(am.mission_id) AS mission_count
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
GROUP BY a.astronaut_id
HAVING mission_count > 1;
```

### WHERE vs HAVING — The Critical Difference

```
┌─────────────────────────────────────────────────────────────┐
│                    Query Execution Order                     │
│                                                             │
│  1. FROM / JOIN    ← tables are combined                    │
│  2. WHERE          ← individual rows are filtered           │
│  3. GROUP BY       ← rows are grouped                       │
│  4. HAVING         ← groups are filtered                    │
│  5. SELECT         ← columns and aggregates are computed    │
│  6. ORDER BY       ← results are sorted                    │
│  7. LIMIT          ← results are truncated                  │
└─────────────────────────────────────────────────────────────┘
```

| | WHERE | HAVING |
|---|---|---|
| **When** | Before grouping | After grouping |
| **Filters** | Individual rows | Grouped results |
| **Can use aggregates?** | ❌ No | ✅ Yes |
| **Example** | `WHERE status = 'Retired'` | `HAVING COUNT(*) > 3` |

> [!tip] **Analogy: Grading Exams**
> - **WHERE** = "Only look at exams from students in Section A" (filter before counting)
> - **GROUP BY** = "Group remaining exams by student"
> - **HAVING** = "Only show students whose average score is above 80" (filter after calculating)

---

## 12 — Other Essential Functions and Operators

### DISTINCT

Removes duplicate rows from results.

```sql
-- List all unique statuses
SELECT DISTINCT status FROM astronauts;
-- Returns: Active, Retired, Deceased, Management, etc.
```

### CASE (Conditional Logic)

SQL's version of `if/else`. Evaluates conditions and returns different values.

```sql
SELECT name,
    CASE
        WHEN space_flights > 5 THEN 'Veteran'
        WHEN space_flights > 0 THEN 'Experienced'
        ELSE 'Rookie'
    END AS experience_level
FROM astronauts;
```

**Structure:**
```
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE default_result
END
```

### IS NOT NULL / IS NULL

Tests whether a value is or isn't `NULL`. You **cannot** use `= NULL` or `!= NULL` — these don't work in SQL.

```sql
-- Astronauts who have recorded flight hours
SELECT name FROM astronauts WHERE space_flight_hours IS NOT NULL;

-- Astronauts with no death date (still alive)
SELECT name FROM astronauts WHERE death_date IS NULL;
```

> [!danger] **Common Mistake**
> ```sql
> -- ❌ WRONG — this will NEVER return results
> SELECT * FROM astronauts WHERE space_flight_hours = NULL;
>
> -- ✅ CORRECT
> SELECT * FROM astronauts WHERE space_flight_hours IS NULL;
> ```
> In SQL, `NULL` represents "unknown." You can't compare something to "unknown" and get a definitive answer — the result is always `NULL` (which is falsy).

### <> (Not Equal To)

```sql
-- All astronauts who are NOT retired
SELECT name FROM astronauts WHERE status <> 'Retired';
```

`<>` is the SQL standard. `!=` also works in SQLite but `<>` is more portable.

---

## 13 — Report Queries (All 14, Fully Explained)

### Query 1: Sample of 10 astronauts with mission count

> **Goal**: Show name, selection year, status, space flights, and a **calculated** mission count from the junction table.

```sql
SELECT
    a.name,
    a.year,
    a.status,
    a.space_flights,
    COUNT(am.mission_id) AS mission_count
FROM astronauts a
LEFT JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
GROUP BY a.astronaut_id
LIMIT 10;
```

**Why LEFT JOIN?** We want ALL astronauts in the sample, even if they have zero linked missions. An `INNER JOIN` would exclude astronauts with no missions.

**Why GROUP BY `astronaut_id`?** Because one astronaut can have multiple rows in the junction table (one per mission). We collapse them and COUNT.
 
---

### Query 2: Retired astronauts with more than one mission

> **Goal**: Filter by status *and* by aggregated mission count.

```sql
SELECT
    a.name,
    COUNT(am.mission_id) AS mission_count
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
WHERE a.status = 'Retired'
GROUP BY a.astronaut_id
HAVING mission_count > 1;
```

**Key insight**: `WHERE` filters individual rows (status = Retired) **before** grouping. `HAVING` filters groups (mission_count > 1) **after** grouping. Both are needed here.

---

### Query 3: Astronauts whose mission history includes Apollo 11

> **Goal**: Find astronauts linked to a specific mission.

```sql
SELECT a.name, a.year, a.status
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
JOIN missions m ON am.mission_id = m.mission_id
WHERE m.name = 'Apollo 11';
```

**This is the "three-table join" pattern** — the most common pattern with junction tables:
1. Start from the entity you want info about (`astronauts`)
2. Join through the junction table (`astronauts_missions`)
3. Join to the related entity (`missions`)
4. Filter on the related entity's attributes (`WHERE m.name = ...`)

---

### Query 4: Crew list for Apollo 11

> **Goal**: Show mission name alongside each crew member.

```sql
SELECT m.name AS mission_name, a.name AS astronaut_name
FROM missions m
JOIN astronauts_missions am ON m.mission_id = am.mission_id
JOIN astronauts a ON am.astronaut_id = a.astronaut_id
WHERE m.name = 'Apollo 11';
```

Same three-table join, but starting from `missions` instead of `astronauts`. The direction doesn't matter — joins are bidirectional.

---

### Query 5: Top 10 missions with the largest crews

> **Goal**: Count astronauts per mission, sort by largest.

```sql
SELECT m.name AS mission_name, COUNT(am.astronaut_id) AS crew_size
FROM missions m
JOIN astronauts_missions am ON m.mission_id = am.mission_id
GROUP BY m.mission_id
ORDER BY crew_size DESC
LIMIT 10;
```

**`ORDER BY ... DESC`** puts the largest values first. `LIMIT 10` takes only the top 10.

---

### Query 6: Mismatched flight counts

> **Goal**: Compare the `space_flights` value stored in the astronauts table with the actual count of linked missions.

```sql
SELECT
    a.name,
    a.space_flights AS recorded_flights,
    COUNT(am.mission_id) AS linked_missions,
    ABS(a.space_flights - COUNT(am.mission_id)) AS mismatch
FROM astronauts a
LEFT JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
GROUP BY a.astronaut_id
HAVING a.space_flights <> COUNT(am.mission_id)
ORDER BY mismatch DESC;
```

**Why this matters**: In a well-maintained database, the stored `space_flights` should match the count of linked missions. Discrepancies indicate **data quality issues** — exactly the kind of problem that flat files make worse and relational design makes visible.

**`ABS()`** ensures the mismatch is always positive regardless of which value is larger.

---

### Query 7: Total space flight hours

> **Goal**: A single aggregate across all astronauts.

```sql
SELECT SUM(space_flight_hours) AS total_flight_hours
FROM astronauts;
```

Simple `SUM()` — no grouping needed because we want one total for the whole table.

---

### Query 8: Min, max, and average flight hours

> **Goal**: Multiple aggregates in one query.

```sql
SELECT
    MIN(space_flight_hours) AS min_hours,
    MAX(space_flight_hours) AS max_hours,
    ROUND(AVG(space_flight_hours), 2) AS avg_hours
FROM astronauts;
```

**`ROUND(..., 2)`** ensures the average is displayed with exactly 2 decimal places.

---

### Query 9: Total space walks

```sql
SELECT SUM(space_walks) AS total_space_walks
FROM astronauts;
```

---

### Query 10: Average missions per astronaut (from relational links)

> **Goal**: Use the junction table to calculate a per-astronaut average.

```sql
SELECT ROUND(
    CAST(COUNT(am.mission_id) AS REAL) / COUNT(DISTINCT am.astronaut_id),
    2
) AS avg_missions_per_astronaut
FROM astronauts_missions am;
```

**Key techniques:**
- `CAST(... AS REAL)` prevents **integer division** (in SQLite, `5/3 = 1`, not `1.67`)
- `COUNT(DISTINCT am.astronaut_id)` counts unique astronauts who have at least one mission
- This uses the junction table directly — no need to join to astronauts

> [!warning] **Integer Division Trap**
> SQLite performs **integer division** by default when both operands are integers:
> ```sql
> SELECT 5 / 3;        -- Returns 1 (not 1.667)
> SELECT 5.0 / 3;      -- Returns 1.667
> SELECT CAST(5 AS REAL) / 3;  -- Returns 1.667
> ```
> Always `CAST` to `REAL` when you need decimal results.

---

### Query 11: Astronaut with the most missions

```sql
SELECT a.name, COUNT(am.mission_id) AS mission_count
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
GROUP BY a.astronaut_id
ORDER BY mission_count DESC
LIMIT 1;
```

**`ORDER BY ... DESC LIMIT 1`** is the standard SQL pattern for "find the maximum row."

---

### Query 12: Percentage of astronauts who flew at least one mission

```sql
SELECT ROUND(
    CAST(COUNT(DISTINCT am.astronaut_id) AS REAL) / 
    (SELECT COUNT(*) FROM astronauts) * 100,
    2
) AS percentage_with_missions
FROM astronauts_missions am;
```

**This uses a subquery**: `(SELECT COUNT(*) FROM astronauts)` calculates the total number of astronauts independently, which is then used as the denominator.

**`COUNT(DISTINCT am.astronaut_id)`** counts only astronauts who appear in the junction table (i.e., have at least one mission).

> [!info] **Subqueries**
> A subquery is a `SELECT` statement nested inside another query. It runs first and its result is used by the outer query. Here, the subquery acts as a **scalar value** (a single number used in arithmetic).

---

### Query 13: Average flight hours per mission (derived metric)

```sql
SELECT ROUND(
    SUM(a.space_flight_hours) / COUNT(DISTINCT m.mission_id),
    2
) AS avg_hours_per_mission
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
JOIN missions m ON am.mission_id = m.mission_id;
```

**This is a derived metric** — it doesn't exist in any table but is **calculated** from existing data. Total astronaut flight hours ÷ number of distinct missions.

---

### Query 14: Approximate standard deviation of flight hours

> **Goal**: Calculate standard deviation using the variance formula.

The formula for **population standard deviation** is:

$$\sigma = \sqrt{\frac{1}{N} \sum_{i=1}^{N} (x_i - \bar{x})^2}$$

Which can be rewritten as:

$$\sigma = \sqrt{AVG(x^2) - AVG(x)^2}$$

```sql
SELECT ROUND(
    SQRT(AVG(space_flight_hours * space_flight_hours) - 
         AVG(space_flight_hours) * AVG(space_flight_hours)),
    2
) AS approx_std_dev
FROM astronauts
WHERE space_flight_hours IS NOT NULL;
```

**Key details:**
- **`WHERE space_flight_hours IS NOT NULL`** — we explicitly ignore NULLs as instructed
- The formula uses the **computational form of variance**: `E[X²] - (E[X])²`
- `AVG(x*x)` = average of squared values
- `AVG(x) * AVG(x)` = square of the average
- `SQRT(difference)` = standard deviation

> [!tip] **Analogy: Spread of Test Scores**
> Standard deviation measures how "spread out" values are. If all astronauts had similar flight hours, the std dev would be small. If some had 0 hours and others had 10,000, it would be large.

---

## 14 — Key Takeaways and Mental Models

### The Evolution Path

```
LEVEL 1: Flat File (Single Table)
   │
   │  Problems: no keys, no relationships, 
   │  data duplication, lists in columns
   │
   ▼
LEVEL 2: Relational Database (Multiple Tables + Keys)
   │
   │  Solutions: primary keys identify rows,
   │  foreign keys link tables, junction tables
   │  handle many-to-many relationships
   │
   ▼
LEVEL 3: Queries That Leverage the Design
   │
   │  JOINs traverse relationships, 
   │  aggregations summarize data,
   │  derived metrics answer complex questions
   │
   ▼
RESULT: Data integrity + query power + scalability
```

### The Five Commandments of Relational Design

| # | Commandment | Violation Example |
|---|---|---|
| 1 | Every table shall have a primary key | A table where rows can't be uniquely identified |
| 2 | Different real-world things → different tables | Astronauts and missions crammed into one table |
| 3 | Relationships shall use foreign keys | Storing "Apollo 11, Gemini 8" as a text string |
| 4 | No lists or tuples in a single column | `missions = "Apollo 11, Gemini 8"` |
| 5 | Many-to-many → junction table | Trying to put multiple mission IDs in one astronaut row |

### NULL Handling Decision Tree

```
Is the column involved in arithmetic (SUM, AVG, etc.)?
├── YES → NULLs are automatically skipped by aggregate functions
│         But be careful: AVG divides by non-NULL count only!
│         Use COALESCE(col, 0) if you want NULLs treated as zero.
│
├── Is it in a WHERE clause?
│   ├── YES → Use IS NULL / IS NOT NULL (never = NULL)
│
└── Is it in a JOIN?
    ├── LEFT JOIN → non-matching rows produce NULLs in right table columns
    └── INNER JOIN → NULLs in join columns mean no match (row excluded)
```

---

## 15 — Quick Reference Cheat Sheet

### SQL Keywords at a Glance

| Keyword | Purpose | Example |
|---|---|---|
| `JOIN` | Combine rows from two tables on matching values | `JOIN missions m ON am.mission_id = m.mission_id` |
| `LEFT JOIN` | All left rows + matching right (NULL if no match) | `LEFT JOIN astronauts_missions am ON ...` |
| `ON` | Specifies the join condition | `ON a.astronaut_id = am.astronaut_id` |
| `DISTINCT` | Remove duplicate rows | `SELECT DISTINCT status FROM astronauts` |
| `GROUP BY` | Group rows by column value | `GROUP BY a.astronaut_id` |
| `HAVING` | Filter groups after aggregation | `HAVING COUNT(*) > 3` |
| `CASE` | Conditional logic | `CASE WHEN x > 5 THEN 'High' ELSE 'Low' END` |
| `IS NOT NULL` | Test for non-NULL | `WHERE death_date IS NOT NULL` |
| `<>` | Not equal to | `WHERE status <> 'Active'` |

### Aggregate Functions

| Function | Description | NULL Behavior |
|---|---|---|
| `COUNT(*)` | Count all rows | Includes NULLs |
| `COUNT(col)` | Count non-NULL values | Excludes NULLs |
| `SUM(col)` | Total | Skips NULLs |
| `AVG(col)` | Average | Skips NULLs |
| `MIN(col)` | Smallest value | Skips NULLs |
| `MAX(col)` | Largest value | Skips NULLs |
| `ROUND(val, n)` | Round to n decimal places | NULL → NULL |
| `ABS(val)` | Absolute value | NULL → NULL |
| `SQRT(val)` | Square root | NULL → NULL |

### Common Patterns

```sql
-- Three-table join through junction table
SELECT a.*, m.*
FROM astronauts a
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
JOIN missions m ON am.mission_id = m.mission_id;

-- Count with grouping
SELECT column, COUNT(*) FROM table GROUP BY column;

-- Top N pattern
SELECT ... ORDER BY metric DESC LIMIT N;

-- Percentage pattern
SELECT ROUND(
    CAST(subset_count AS REAL) / total_count * 100, 2
) AS percentage;

-- Subquery for total
SELECT ... / (SELECT COUNT(*) FROM table) ...;
```

![[Pasted image 20260227093856.png]]
# Solution Code 
``` sql

.print "-----------------------------"
.print "Inside script <report.sql>"
.print "-----------------------------"

.mode box

.print "-----------------------------"
.print "1) Sample of first 10 astronauts"
.print "   (name, selection year, status, space flights, mission count)"

SELECT

    a.name,
    a.year,
    a.status,
    a.space_flights,

    COUNT(am.mission_id) AS mission_count

FROM astronauts a

LEFT JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id
GROUP BY a.astronaut_id
LIMIT 10;

  
  

.print "-----------------------------"

.print "2) Retired astronauts who completed more than one mission"

SELECT

    a.name,
    COUNT(am.mission_id) AS mission_count

FROM astronauts 
JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id

WHERE a.status = 'Retired'

GROUP BY a.astronaut_id

HAVING mission_count > 1;

  
  

.print "-----------------------------"
.print "3) Astronauts whose missions include Apollo 11"

SELECT a.name, a.year, a.status

FROM astronauts a

JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id

JOIN missions m ON am.mission_id = m.mission_id

WHERE m.name = 'Apollo 11';

  
  

.print "-----------------------------"

.print "4) Crew list for Apollo 11"

SELECT m.name AS mission_name, a.name AS astronaut_name

FROM missions m

JOIN astronauts_missions am ON m.mission_id = am.mission_id

JOIN astronauts a ON am.astronaut_id = a.astronaut_id

WHERE m.name = 'Apollo 11';

  
  

.print "-----------------------------"
.print "5) Missions with the largest crews"

SELECT m.name AS mission_name, COUNT(am.astronaut_id) AS crew_size

FROM missions m

JOIN astronauts_missions am ON m.mission_id = am.mission_id

GROUP BY m.mission_id

ORDER BY crew_size DESC

LIMIT 10;

  
  

.print "-----------------------------"
.print "6) Astronauts where recorded space_flights does not match linked missions"

SELECT

    a.name,

    a.space_flights AS recorded_flights,

    COUNT(am.mission_id) AS linked_missions,

    ABS(a.space_flights - COUNT(am.mission_id)) AS mismatch

FROM astronauts a

LEFT JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id

GROUP BY a.astronaut_id

HAVING a.space_flights <> COUNT(am.mission_id)

ORDER BY mismatch DESC;

  

-- numeric below here

  

.print "-----------------------------"
.print "7) Total space flight hours across all astronauts"

SELECT SUM(space_flight_hours) AS total_flight_hours

FROM astronauts;

  
  

.print "-----------------------------"
.print "8) Minimum, Maximum, and Average space flight hours"

SELECT

    MIN(space_flight_hours) AS min_hours,

    MAX(space_flight_hours) AS max_hours,

    ROUND(AVG(space_flight_hours), 2) AS avg_hours

FROM astronauts;

  
  

.print "-----------------------------"
.print "9) Total number of space walks"

SELECT SUM(space_walks) AS total_space_walks

FROM astronauts;

  
  

.print "-----------------------------"
.print "10) Average missions per astronaut (using relational count)"

SELECT ROUND(

    CAST(COUNT(am.mission_id) AS REAL) / COUNT(DISTINCT am.astronaut_id),

    2

) AS avg_missions_per_astronaut

FROM astronauts_missions am;

  
  

.print "-----------------------------"
.print "11) Astronaut with the highest number of missions"

SELECT a.name, COUNT(am.mission_id) AS mission_count

FROM astronauts a

JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id

GROUP BY a.astronaut_id

ORDER BY mission_count DESC

LIMIT 1;

  
  

.print "-----------------------------"
.print "12) Percentage of astronauts who have flown at least one mission"

SELECT ROUND(

    CAST(COUNT(DISTINCT am.astronaut_id) AS REAL) /

    (SELECT COUNT(*) FROM astronauts) * 100,

    2

) AS percentage_with_missions

FROM astronauts_missions am;

  
  

.print "-----------------------------"
.print "13) Average flight hours per mission (derived metric)"

SELECT ROUND(

    SUM(a.space_flight_hours) / COUNT(DISTINCT m.mission_id),

    2

) AS avg_hours_per_mission

FROM astronauts a

JOIN astronauts_missions am ON a.astronaut_id = am.astronaut_id

JOIN missions m ON am.mission_id = m.mission_id;

  
  

.print "-----------------------------"
.print "14) Standard deviation approximation (using variance formula)"

SELECT ROUND(

    SQRT(

        AVG(space_flight_hours * space_flight_hours) -

        AVG(space_flight_hours) * AVG(space_flight_hours)

    ),

    2

) AS approx_std_dev

FROM astronauts

WHERE space_flight_hours IS NOT NULL;


.mode list
```

> [!warning]
> "." notation in sqlite needs not to have semicolon in the end.