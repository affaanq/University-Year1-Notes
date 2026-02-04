# Data Levels of Measurement (Stevens' Scales)

## 1. Nominal Data (Qualitative)
* **Definition:** Types of categorical data used for naming or labeling variables without any quantitative value.
* **Examples:** Gender, hair color, nationality, blood type, or even ZIP codes.
* **Key Property:** **Identity**. You can only say two things are the same or different. 
* **Analysis Limitations:**
    * **Allowed:** Counting (Frequency), Mode (most common category).
    * **Forbidden:** You cannot calculate a "mean" hair color. You cannot "rank" them (e.g., Red is not "greater" than Brown).

---

## 2. Ordinal Data (Qualitative/Categorical)
* **Definition:** Categorical data where the order matters, but the intervals between the values are not known or equal. 
* **Correction:** You noted "Original," but the technical term is **Ordinal** (think "Order").
* **Examples:** Survey ratings (Satisfied, Neutral, Dissatisfied), military rank, or socio-economic status.
* **Key Property:** **Rank**. You know $A > B$, but you don't know by *how much*. 
* **Analysis Limitations:**
    * **Allowed:** Median, Percentiles, Rank Correlation.
    * **Forbidden:** Addition and Subtraction. A "5-star" rating minus a "2-star" rating does not equal a "3-star" rating in a mathematical sense.



---

## 3. Interval Data (Quantitative)
* **Definition:** Numerical data with equal, consistent intervals between values, but it lacks a **true zero** point.
* **Examples:** Temperature ($^\circ\text{C}$ or $^\circ\text{F}$), IQ Score, or SAT scores.
* **Key Property:** **Distance**. The difference between $10^\circ\text{C}$ and $20^\circ\text{C}$ is the same as the difference between $30^\circ\text{C}$ and $40^\circ\text{C}$.
* **The "Zero" Problem:** $0^\circ\text{C}$ does not mean "no temperature"; it's just an arbitrary point on a scale.
* **Analysis Limitations:**
    * **Allowed:** Addition, Subtraction, Mean, Standard Deviation.
    * **Forbidden:** Ratios. You **cannot** say $100^\circ\text{C}$ is "twice as hot" as $50^\circ\text{C}$ because there is no absolute starting point.

---

## 4. Ratio Data (Quantitative)
* **Definition:** The "gold standard" of data. It has all the properties of interval data but includes a **true zero** point (where zero means a total absence of the variable).
* **Examples:** Height, weight, age, income, or distance.
* **Key Property:** **Absolute Zero**.
* **Analysis Power:**
    * **Allowed:** **All** mathematical operations, including multiplication and division (Ratios).
    * **Significance:** You can accurately say that someone who is $200\text{ cm}$ tall is exactly twice as tall as someone who is $100\text{ cm}$.

---

## Summary Comparison Table

| Data Level   | Order | Equal Intervals | True Zero | Mathematical Operations   |
| :----------- | :---: | :-------------: | :-------: | :------------------------ |
| **Nominal**  |  No   |       No        |    No     | Counts / Mode             |
| **Ordinal**  |  Yes  |       No        |    No     | Median / Rank             |
| **Interval** |  Yes  |       Yes       |    No     | Addition / Subtraction    |
| **Ratio**    |  Yes  |       Yes       |    Yes    | Multiplication / Division |


# Data Structures and Databases

## 1. Data Classification
While your course focuses on **Structured Data**, it is essential to understand the full spectrum for context in modern computing.

* **Structured Data:** Highly organized data that fits neatly into fixed fields (tables). It is easily searchable using SQL.
* **Unstructured Data:** Data that does not have a pre-defined model (e.g., PDFs, images, social media posts).
* **Semi-Structured Data:** Does not reside in a relational database but has some organizational markers (e.g., JSON, XML).



---

## 2. Database Models

### Relational Databases (RDBMS)
* **Structure:** Highly structured. It uses a fixed **Schema** where data is organized into tables with rows and columns.
* **Constraint:** Requires data to fit a pre-defined format before it can be stored.
* **Examples:** MySQL, PostgreSQL, **SQLite3**.
* **Language:** Uses **SQL** (Structured Query Language).

### Non-Relational Databases (NoSQL)
* **Structure:** Supports storage of **less structured** or "schema-less" data.
* **Flexibility:** Designed for the "write" of code where the structure might change frequently (Documents, Graphs, Key-Value pairs).
* **Use Case:** Ideal for Big Data and real-time web apps.
* **Examples:** MongoDB, Cassandra, Redis.



---

## 3. Artificial Intelligence in Data
AI acts as the bridge between "messy" real-world data and structured analysis.
* **Processing:** Used specifically when data appears to be **unstructured** (text, audio, video).
* **Pattern Extraction:** Used to identify "hidden" structures or relationships within vast datasets that traditional algorithms cannot detect.

---

## 4. The Database Environment
* **Definition:** A structured set of data held by a computer, designed for efficient storage, retrieval, and management, especially accessible in various ways (queries).
* **SQLite3:** The chosen environment for this course. 
    * **Nature:** A lightweight, disk-based Relational Database Management System (RDBMS).
    * **Portability:** It is "serverless," meaning the entire database is stored in a single file on your disk, making it perfect for learning SQL.
