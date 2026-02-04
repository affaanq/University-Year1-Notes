
# 🗃️ SQLite: Workflow, Scripts, and Automation (Lab-02)

## 1. The Environment: OS vs. Inner Shell

The most common point of failure for beginners is confusing the **Operating System (PowerShell)** with the **Database Engine (SQLite)**.

|**Environment**|**Prompt**|**Language**|**Purpose**|
|---|---|---|---|
|**Windows PowerShell**|`PS C:\sqlite>`|PowerShell/CMD|Navigating folders, moving files, starting programs.|
|**SQLite Inner Shell**|`sqlite>`|SQL & Dot Commands|Interacting with the data inside a `.db` file.|

> [!CAUTION]
> 
> If you type `SELECT * FROM people;` while the prompt is `PS C:\>`, it **will fail** because PowerShell does not speak SQL.

---

## 2. Command Types: Dot vs. SQL

Inside the SQLite shell, there are two distinct categories of instructions:

### A. Dot Commands (The Tool Settings)

These are **not** SQL. They are instructions for the `sqlite3.exe` program itself.

- **Format:** Start with a dot (`.`), no semicolon at the end.
    
- **Key Commands:**
    
    - `.databases`: List attached database files.
        
    - `.tables`: Show all tables in the current database.
        
    - `.schema <table_name>`: Show the `CREATE` code for a specific table.
        
    - `.read <filename>.sql`: Execute a script file.
        
    - `.mode column` & `.headers on`: Make the output readable.
        
    - `.exit`: Close the shell safely and save work.
        

### B. SQL Commands (The Data Language)

These are the universal instructions for managing data.

- **Format:** No dot, **MUST** end with a semicolon (`;`).
    
- **Key Commands:**
    
    - `CREATE TABLE`: Define the "shape" (columns/types).
        
    - `INSERT INTO`: Add a row of data.
        
    - `SELECT`: Retrieve data.
        
    - `DROP TABLE`: Delete a table.
        

---

## 3. Automation via Scripting

Typing manually into a terminal is for amateurs. It leads to the typos you've been experiencing (like missing commas or wrong quotes). Real developers use **Idempotent Scripts**.

### The Script Hierarchy

To rebuild your entire database in one second, create this file structure in `C:\sqlite`:

#### 1. `create_people.sql`

This file handles the structure.

SQL

```
-- Ensure we start fresh by dropping the old table first
DROP TABLE IF EXISTS people; 

CREATE TABLE people (
    id INTEGER PRIMARY KEY, -- Use PRIMARY KEY for the ID
    age INTEGER,
    fname TEXT,
    surname TEXT
);
```

#### 2. `load_people.sql`

This file contains your data.

SQL

```
INSERT INTO people (id, age, fname, surname) VALUES (1, 34, 'Affaan', 'Qureshi');
-- ... add the other 29 rows here
```

#### 3. `report.sql`

This file runs your queries.

SQL

```
.mode column
.headers on

-- TASK: People under 40
SELECT fname, surname FROM people WHERE age < 40;

-- TASK: People over 60
SELECT * FROM people WHERE age > 60;
```

#### 4. `top_level.sql` (The Master Controller)

This is the single entry point for the entire lab.

SQL

```
.print "--- Starting Build ---"
.read create_people.sql
.print "--- Table Created ---"
.read load_people.sql
.print "--- Data Loaded ---"
.read report.sql
.print "--- Lab Complete ---"
```

---

## 4. Crucial Logic: Selection vs. Projection

When using the `SELECT` statement, understand what you are actually doing to the data:

- **Projection:** Choosing which **Columns** you see (the `SELECT` part).
    
- **Selection:** Choosing which **Rows** you see (the `WHERE` part).
    

---

## 5. Summary of Workflow Errors & Fixes

|**Error**|**Reason**|**Fix**|
|---|---|---|
|`Select-Object : ...`|Typing SQL in PowerShell.|Run `.\sqlite3.exe lab-02.db` first.|
|`Parse error: near "'QURESHI'"`|Missing comma between values.|Ensure every column has a comma: `'Affaan', 'Qureshi'`.|
|`...>` prompt|Missing semicolon.|Type `;` and hit Enter to end the statement.|
|`no such column: "Alice"`|Used double quotes for data.|Use **single quotes** for text data: `'Alice'`.|
