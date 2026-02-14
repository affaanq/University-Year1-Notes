
# 01_SQL_Syntax_Glossary

**Tags:** #cheatsheet #syntax #operators

### Core Keywords & Operators

| **Keyword / Operator** | **Purpose**                                               | **Context / Example**                               |
| ---------------------- | --------------------------------------------------------- | --------------------------------------------------- |
| **SELECT**             | Specifies which columns to retrieve.                      | `SELECT name`                                       |
| **FROM**               | Specifies the table to query.                             | `FROM users`                                        |
| **WHERE**              | Filters rows based on conditions.                         | `WHERE id > 5`                                      |
| **GROUP BY**           | Groups rows into summary buckets.                         | `GROUP BY year`                                     |
| **ORDER BY**           | Sorts the result-set (ASC or DESC).                       | `ORDER BY date DESC`                                |
| **LIMIT**              | Restricts the number of rows returned.                    | `LIMIT 1`                                           |
| **COUNT()**            | Counts the number of rows.                                | `COUNT(*)`                                          |
| **IN**                 | Matches any value in a specified list (shorthand for OR). | **`WHERE city IN ('London', 'Mumbai', 'Glasgow')`** |
| **AS**                 | Creates an alias for a column or table.                   | `SELECT id AS user_id`                              |
| **AND / OR**           | Logical connectors for conditions.                        | `WHERE a = 1 AND b = 2`                             |
| **LIKE**               | Pattern matching with wildcards.                          | `WHERE name LIKE 'A%'`                              |
| **%**                  | Wildcard for `LIKE`.                                      | `'A%'` (Starts with A)                              |
| **=**                  | Equality comparison.                                      | `WHERE status = 'active'`                           |
| **< / >**              | Less than / Greater than.                                 | `WHERE cost < 100`                                  |
| *****                  | Wildcard for "all columns".                               | `SELECT * FROM table`                               |

---

# 02_Architecture_and_Workflow

**Tags:** #architecture #scripting

### Execution Flow

SQL scripts execute strictly **Top-to-Bottom**.

1. **Dependency Rule:** Objects (tables) must exist before they are manipulated.
    
2. **Order of Operations:**
    
    - **CLEAN:** `DROP TABLE IF EXISTS ...`
        
    - **BUILD:** `CREATE TABLE ...`
        
    - **LOAD:** `INSERT` or `.import`
        
    - **QUERY:** `SELECT ...`
        

### CLI vs. Engine

Distinguish between the tool and the language.

|**Command Type**|**Examples**|**Scope**|
|---|---|---|
|**Dot-Commands**|`.mode`, `.import`, `.read`|**SQLite CLI Only.** these configure the environment or handle file I/O. They are _not_ SQL.|
|**Standard SQL**|`SELECT`, `CREATE`|**Database Engine.** These work in any SQL environment (Python, DBeaver, etc.).|

---

# 03_DDL_Creating_Tables

**Tags:** #DDL #schema

### The "Clean Slate" Protocol

To prevent "Table already exists" errors during iterative development, always strictly clear the environment before building.

SQL

``` sql
-- 1. Drop old version
DROP TABLE IF EXISTS table_name;

-- 2. Create new version
CREATE TABLE table_name (
    id INTEGER PRIMARY KEY,
    col1 TEXT,
    col2 TEXT   -- No comma on the last column!
);
```

---

# 04_SQLite_Import_Guide

**Tags:** #csv #etl #sqlite

### The CLI Import Workflow

SQLite's automatic schema guessing is unreliable. Follow this strict sequence for CSV imports:

1. **Define Schema:** Manually `CREATE TABLE` first to enforce data types (INTEGER vs TEXT).
    
2. **Set Mode:** Switch to CSV parsing mode with `.mode csv`.
    
3. **Execute Import:** Use `.import FILE_PATH TABLE_NAME`.
    

**Snippet:**

SQL

``` sql
-- Inside a .sql script or CLI
.mode csv
.import MOCK_DATA.csv target_table
```

> [!NOTE] Handling Headers
> 
> If the CSV contains headers, SQLite may import them as a data row.
> 
> - **Fix:** Use `DELETE FROM table WHERE id = 'id_header_text';` after import if the version of SQLite does not support `.import --skip 1`.
>     

---

# 05_Querying_Logic

**Tags:** #DQL #logic

### Column Selection

- **Correct:** `SELECT col1, col2 FROM table`
    
- **Incorrect:** `SELECT (col1, col2) FROM table`
    
- **Rule:** Parentheses in `SELECT` are reserved for functions or sub-queries, not for grouping column lists.
    

### Boolean Logic Pitfalls

SQL logic is strict.

- **The "English" Trap:** `WHERE col = 'A' OR 'B'`
    
    - _Result:_ Evaluates `'B'` as `TRUE`. Returns all rows.
        
- **The Fix:** Explicitly state the column for every condition.
    
    - _Correct:_ `WHERE col = 'A' OR col = 'B'`
        
    - _Efficient:_ `WHERE col IN ('A', 'B')`
        

### Operator Precedence

Semicolons terminate execution immediately.

- **Rule:** Never place a semicolon mid-query (e.g., before an `AND` or `ORDER BY` clause). The parser will stop reading, and the subsequent lines will be treated as garbage text.

# Example of Reporting

``` sql

.print "-----------------------------"
.print "Inside script <report.sql>"
.print "-----------------------------"

.mode box


.print "-----------------------------"

.print "1) All columns where id < 10"
SELECT * FROM people WHERE id < 10;


.print "-----------------------------"

.print "2) All columns where id > 900"
SELECT * FROM people WHERE id > 900; 
  

.print "-----------------------------"

.print "3) First name where id < 10"
SELECT first_name FROM people WHERE id < 10;

  
.print "-----------------------------"

.print "4) Second name (surname) where id < 100 AND favourite colour is red"
SELECT last_name FROM people WHERE id < 100 AND fav_color = 'red';


.print "-----------------------------"

.print "5) id, first name, second name (surname), city where favourite colour is blue OR indigo"
SELECT first_name, last_name, city_name FROM people WHERE fav_color = 'blue' OR fav_color = 'indigo';

  

.print "-----------------------------"

.print "6) All columns where email ends with '.com'"
SELECT email FROM people
WHERE email LIKE '%.com';


.print "-----------------------------"

.print "7) First name and second name where both begin with 'C'"
SELECT first_name FROM people
WHERE first_name LIKE 'C%'
    AND last_name LIKE 'C%';

  
.print "-----------------------------"

.print "8) First name, city, and favourite colour where city begins with 'L' and colour is blue"
SELECT (first_name, city_name, fav_color) FROM people
WHERE city_name LIKE 'L%'
    AND fav_color = 'blue';
    
 -- Extra: GOOD one to solve:
 -- identify the selection_year which produced the highest number of astranauts
 SELECT selection_year, COUNT(*) AS selected -- rename it as selected
 FROM astronauts
 GROUP BY selection_year
 ORDER BY selected DESC
 LIMIT 1;
 

.mode list
```


### Imperative vs. Declarative Paradigms

![[Pasted image 20260214122759.png]]

The core difference lies in **how** much control you take over the execution process versus the **result** you want to achieve.

|**Feature**|**Imperative (e.g., Python)**|**Declarative (e.g., SQL)**|
|---|---|---|
|**Focus**|**How** to do it (Step-by-step instructions).|**What** to do (Desired output).|
|**Control**|High control over flow, loops, and state.|The system decides the most efficient way to get the data.|
|**Example**|"Open file, loop through lines, if 'x' exists, print it."|"SELECT * FROM table WHERE name = 'x';"|

---

### 2. Data Strategy: Pandas vs. Databases

While Pandas is a powerhouse for data science, it is a **tool for analysis**, not a **system for storage**. Using it as a database is a strategic error for any scalable application.

#### **Pandas (The Analyst’s Scalpel)**

- **Use Case:** Quick, exploratory data analysis (EDA), cleaning, and local manipulation.
    
- **Memory:** Operates entirely in RAM. If your dataset exceeds your computer's memory, Pandas will crash.
    
- **Persistence:** Data is volatile. If the script stops, the data is gone unless you manually export it (CSV, JSON).
    
- **Concurrency:** Only one person/process can safely edit the data at a time.
    

#### **Databases (The Foundation)**

- **Use Case:** Large-scale storage, multi-user applications, and long-term reliability.
    
- **Scale:** Handles Terabytes of data by utilizing disk storage and indexing.
    
- **Concurrency:** Designed for hundreds of users to read and write simultaneously without data corruption.
    
- **Structure:** Enforces "Schemas" (rules) to ensure data integrity and relationships between tables.
    

> **The Brutal Truth:** If you try to use Pandas to manage a growing system, you are building a house on sand. Use SQL to **manage and fetch** your data; use Pandas to **interrogate** it once it’s fetched.