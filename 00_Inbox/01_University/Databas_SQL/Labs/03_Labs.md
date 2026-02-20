
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
## IS NULL and IS NOT NULL

⚠️ **Important:** Cannot use `= NULL` or `!= NULL` - must use `IS NULL` or `IS NOT NULL`

### Example

```sql
-- Find students with no email address
SELECT name, student_id FROM students
WHERE email IS NULL;

-- Find students who have submitted their assignment
SELECT name, submission_date FROM assignments
WHERE submission_date IS NOT NULL;

-- Count students missing phone numbers
SELECT COUNT(*) AS missing_phone
FROM students
WHERE phone IS NULL;

-- Get complete records only
SELECT * FROM students
WHERE email IS NOT NULL 
  AND phone IS NOT NULL 
  AND address IS NOT NULL;
```

### NULL vs Empty String

```sql
-- NULL means "unknown" or "missing"
WHERE email IS NULL          -- No email data at all

-- Empty string means "known to be empty"
WHERE email = ''             -- Email field exists but is blank

-- Find either condition
WHERE email IS NULL OR email = '';
```

### Using with Aggregates

```sql
-- Count students WITH email addresses
SELECT COUNT(email) FROM students;  -- Automatically excludes NULLs

-- Count all students including those without emails
SELECT COUNT(*) FROM students;

-- Find majors with students who haven't declared
SELECT major, COUNT(*) AS undeclared_count
FROM students
WHERE major IS NULL
GROUP BY major;
```
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

|Keyword|Type|Purpose|
|---|---|---|
|LIMIT|Clause|Restrict number of rows|
|COUNT|Aggregate|Count rows|
|DISTINCT|Modifier|Remove duplicates|
|ORDER BY|Clause|Sort results|
|ASC|Modifier|Sort ascending|
|DESC|Modifier|Sort descending|
|GROUP BY|Clause|Group for aggregation|
|AVG|Aggregate|Calculate average|
|AS|Keyword|Rename output column|
|IS NULL|Operator|Check if value is NULL|
|IS NOT NULL|Operator|Check if value is not NULL|

---

# Solution Code: 

```sql
.print "-----------------------------"
.print "Inside script <report.sql>"
.print "-----------------------------"


.mode box

.print "-----------------------------"

.print "1) Show a sample of the data for the first 10 astronauts (name, year selected, status, number of space flights)"

SELECT name, selection_year, status, space_flights FROM astronauts

    LIMIT 10;

.print "-----------------------------"

.print "2) Count the total number of astronauts in the dataset"

SELECT Count(name) AS Total_Astronauts FROM astronauts;



.print "-----------------------------"

.print "3) List all distinct astronaut statuses present in the data"

SELECT DISTINCT status FROM astronauts;

  
.print "-----------------------------"

.print "4) List astronauts who were selected after the year 1990 (show name, year, and status)"

SELECT name, selection_year, status FROM astronauts

    WHERE selection_year > 1990

    ORDER BY selection_year ASC;
  

.print "-----------------------------"

.print "5) List retired astronauts who completed more than one space flight and whose name begins with B"

SELECT name, selection_year, status, space_flights, space_flight_hours FROM astronauts

WHERE space_flights > 1

    AND status = 'Retired'

    AND name LIKE 'B%';


.print "-----------------------------"

.print "6) Count how many astronauts fall into each status category and order those from high to low"

SELECT

    status,

    COUNT(*) AS astronaut_count

FROM astronauts

GROUP BY status

ORDER BY astronaut_count DESC;

  

.print "-----------------------------"

.print "7) Calculate the average number of space flight hours for each astronaut status"

SELECT status, AVG(space_flight_hours) AS Average_flight_hours

    FROM astronauts

    GROUP BY status;


.print "-----------------------------"

.print "8) Identify the selection_year that produced the highest number of astronauts"

SELECT Count(name) AS highest_number_of_astronauts, selection_year FROM astronauts

    GROUP BY selection_year

    ORDER BY Count(name) DESC

    LIMIT 1;
  

.print "-----------------------------"

.print "9) Count how many astronauts have missing or unspecified alma mater information"

 SELECT Count(*) AS number_of_missing_alma_mater FROM astronauts

 WHERE alma_mater = '' OR alma_mater IS NULL;

  
  

.print "-----------------------------"

.print "10) Find astronauts whose mission list includes Apollo 11 (text search in flat file)"

SELECT name, missions FROM astronauts

    WHERE missions LIKE '%Apollo 11%';
 

.print "-----------------------------"

.print "11) Calculate the ratio of retired astronauts to active astronauts"

SELECT

    (SELECT COUNT(*) FROM astronauts WHERE status = 'Retired') * 1.0/

    (SELECT COUNT(*) FROM astronauts WHERE status = 'Active')

AS retired_to_Active_Fraction;

  
  

.print "-----------------------------"

.print "12) Calculate the % of female astronauts"

SELECT

    (SELECT Count(*) FROM astronauts WHERE sex = 'Female') * 100 /

    (SELECT Count(*) FROM astronauts WHERE sex = 'Male' OR sex = 'Female')

AS Percentage_of_Females;

  

.print "-----------------------------"

.print "13) Some people without missions"

SELECT name FROM astronauts

WHERE TRIM(missions) = ''

    OR missions IS NULL;

  

.print "-----------------------------"

.print "14) Some people with missions"

SELECT name, missions FROM astronauts

WHERE TRIM(missions) != '';

.mode list
```

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