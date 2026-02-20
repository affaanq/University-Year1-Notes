
# SQL Keywords - Data Retrieval & Filtering

> Notes on SQL keywords for controlling query results without modifying the underlying data.

---

## LIMIT
Restricts the number of rows returned by a query.

**Use case:** Getting a sample of data or "top N" results

### Example
```sql
-- Get the first 5 students from the table
SELECT * FROM students
LIMIT 5;

-- Get top 3 highest scores
SELECT name, score FROM exam_results
ORDER BY score DESC
LIMIT 3;
```

---

## COUNT
Aggregate function that counts rows matching a condition.

**Use case:** Answering "how many?" questions

### Example
```sql
-- Count total number of students
SELECT COUNT(*) FROM students;

-- Count students with grade A
SELECT COUNT(*) FROM students
WHERE grade = 'A';

-- Count unique majors
SELECT COUNT(DISTINCT major) FROM students;
```

---

## DISTINCT
Removes duplicate values, showing only unique entries.

**Use case:** Finding unique categories, statuses, or values

### Example
```sql
-- Get all unique majors offered
SELECT DISTINCT major FROM students;

-- Get unique combinations of year and major
SELECT DISTINCT year, major FROM students;

-- Count unique cities
SELECT COUNT(DISTINCT city) FROM students;
```

---

## ORDER BY
Controls the sorting order of returned rows.

**Use case:** When sequence matters (alphabetical, chronological, numerical)

### Example
```sql
-- Sort students by name (alphabetically)
SELECT * FROM students
ORDER BY name;

-- Sort by enrollment date
SELECT * FROM students
ORDER BY enrollment_date;

-- Multiple column sorting
SELECT * FROM students
ORDER BY year, name;
```

⚠️ **Note:** Without ORDER BY, row order is not guaranteed!

---

## ASC and DESC
Specifies sorting direction with ORDER BY.

- **ASC** = Ascending (smallest to largest, A to Z)
- **DESC** = Descending (largest to smallest, Z to A)

### Example
```sql
-- Sort scores from lowest to highest (ASC is default)
SELECT name, score FROM exam_results
ORDER BY score ASC;

-- Sort scores from highest to lowest
SELECT name, score FROM exam_results
ORDER BY score DESC;

-- Multiple columns with different directions
SELECT name, year, gpa FROM students
ORDER BY year ASC, gpa DESC;
```

---

## GROUP BY
Groups rows with the same value(s) for aggregation.

**Use case:** Summarizing data by category

### Example
```sql
-- Count students in each major
SELECT major, COUNT(*) FROM students
GROUP BY major;

-- Average GPA by year
SELECT year, AVG(gpa) FROM students
GROUP BY year;

-- Multiple grouping columns
SELECT major, year, COUNT(*) FROM students
GROUP BY major, year;
```

⚠️ **Important:** Columns in SELECT must either be in GROUP BY or use an aggregate function!

---

## AVG
Aggregate function that calculates the average of numeric values.

**Use case:** Finding mean values (scores, prices, durations)

### Example
```sql
-- Average GPA of all students
SELECT AVG(gpa) FROM students;

-- Average score by subject
SELECT subject, AVG(score) FROM exam_results
GROUP BY subject;

-- Average with filtering
SELECT AVG(score) FROM exam_results
WHERE subject = 'Mathematics';
```

---

## AS
Assigns a readable alias to columns or calculations.

**Use case:** Making output more readable, especially with calculations

### Example
```sql
-- Rename column in output
SELECT name AS student_name, gpa AS grade_point_average
FROM students;

-- Name calculated columns
SELECT major, COUNT(*) AS total_students
FROM students
GROUP BY major;

-- Complex calculation with alias
SELECT name, (score * 0.7 + exam * 0.3) AS final_grade
FROM results;
```

---

## Combining Keywords

These keywords are powerful when combined:

### Example: Complete Query
```sql
-- Find top 5 majors by average GPA
SELECT 
    major AS degree_program,
    COUNT(*) AS student_count,
    AVG(gpa) AS average_gpa
FROM students
WHERE year >= 2
GROUP BY major
ORDER BY average_gpa DESC
LIMIT 5;
```

### Example: Unique Values Sorted
```sql
-- Get unique years with student count
SELECT 
    DISTINCT year,
    COUNT(*) AS students_enrolled
FROM students
GROUP BY year
ORDER BY year ASC;
```

---

## Quick Reference Table

| Keyword | Type | Purpose |
|---------|------|---------|
| LIMIT | Clause | Restrict number of rows |
| COUNT | Aggregate | Count rows |
| DISTINCT | Modifier | Remove duplicates |
| ORDER BY | Clause | Sort results |
| ASC | Modifier | Sort ascending |
| DESC | Modifier | Sort descending |
| GROUP BY | Clause | Group for aggregation |
| AVG | Aggregate | Calculate average |
| AS | Keyword | Rename output column |

---

## Common Patterns

### Pattern 1: Top N Results
```sql
SELECT column
FROM table
ORDER BY column DESC
LIMIT N;
```

### Pattern 2: Count by Category
```sql
SELECT category, COUNT(*) AS count
FROM table
GROUP BY category;
```

### Pattern 3: Unique Values
```sql
SELECT DISTINCT column
FROM table
ORDER BY column;
```

---

**Tags:** #sql #database #queries #aggregation

**Related:** [[SQL-Basics]], [[SQL-WHERE-Clause]], [[Database-Design]]