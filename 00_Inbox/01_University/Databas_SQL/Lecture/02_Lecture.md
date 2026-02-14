# Data Representation and 'Modelling'

**Source:** Glasgow Caledonian University (GCU) **Core Focus:** Transitioning from raw data storage (files) to conceptual and relational data modelling.

## 1. Data in Files (The Problem Space)

Before data is "modelled," it exists in raw formats. The document identifies two main categories:

### A. Spreadsheet-Based Data (e.g., .xlsx, .csv)

- **Characteristics:** Rectangular organization (rows and columns).
    
- **The Issue:** Real-world government or transport data (e.g., Department for Transport statistics) often suffers from **poor formatting**.
    
- **CSV (Comma Separated Values):**
    
    - **Headed:** Contains a "schema" (column names) at the top (e.g., `id, product, price`).
        
    - **No Header:** Schema is not stored with the data; the program must "know" what each column represents.
        
- **Redundancy:** The document highlights that file-based data often contains massive duplication (redundancy), leading to inefficiency.
    

### B. Markup & Structured Data (.JSON, .XML)

Used primarily for machine-to-machine communication and software ecosystems.

- **XML (eXtensible Markup Language):** Human-readable, uses tags (markup) to define structure. Often parsed as input to trigger program components.
    
- **JSON (JavaScript Object Notation):** A lighter, attribute-value pair format.
    
- **The "Snake" Warning:** In languages like Python, **inconsistent whitespace** in data/code interpretation can break the logic (visualized by the "angry snake" graphic).
    

## 2. The Concept of 'Modelling'

Modelling is not just about drawing diagrams; it is a formal language used to depict a real-world system.

### Criteria for a Model:

1. **Depiction:** Describing a real system (e.g., the location of a falling body).
    
2. **Language:** Using a formal syntax (e.g., Calculus, UML, SQL).
    
3. **Communication:** Must be shared and understood by others using that language.
    
4. **Repeatability:** Models must avoid faulty methods, unconscious bias, or manipulated results.
    

### Comparison of Modelling Types:

- **Mathematical Modelling:** Uses Differential Calculus ($F_g = \frac{Gm_1m_2}{r^2}$) to model physical systems.
    
- **Object-Oriented (OO) Modelling:** Uses **UML (Unified Modeling Language)** to depict software classes and relationships (e.g., Hospital -> Staff -> Doctor).
    
- **Relational Modelling:** Uses **ERDs (Entity Relationship Diagrams)** and **SQL** to depict database structures.
    

## 3. Relational Database Fundamentals

This is the "core" of the course. A relational database is **self-describing**.

### Key Terminology (The "Must-Knows"):

| Term             | Definition                                                                |
| ---------------- | ------------------------------------------------------------------------- |
| **Schema**       | The entire structure/description of data, packaged _inside_ the database. |
| **Entity**       | A specific "thing" being modelled (e.g., "Employee," "Book").             |
| **Attribute**    | The properties of an entity (columns in a table).                         |
| **Table**        | The axiomatic form of representation in relational DBs.                   |
| **Tuple**        | A single row of data (an $n$-tuple where $n$ is the number of columns).   |
| **Primary Key**  | A unique identifier for a row (no two rows can have the same PK).         |
| **Relationship** | The links between tables (e.g., One-to-Many, Many-to-Many).               |
### Entity vs. Instance

- **Entity Type:** The blueprint or template (e.g., the concept of a "Book").
    
- **Entity Instance:** A specific record within that entity (e.g., the physical copy of "Introduction to Algorithms").

## 4. The "Point of View" in Modelling

**Crucial Concept:** There is no single "correct" model for a "thing." The model depends entirely on the **Problem Domain**.

## 5. Metadata and Physical Representation

Data representation often requires understanding the "invisible" characters that define structure in a text file:

- **Control Characters:** The `\n` (newline) character is essential for separating rows in a CSV.
    
- **Verbose vs. Non-Verbose Headers:** Data providers often use shortened codes (e.g., `PC` for Postcode) which require documentation/metadata to decode.
    

## 6. Data Integrity and Constraints

A model is only as good as the rules it enforces.

- **Referential Integrity:** Ensuring that relationships between tables remain consistent (e.g., you cannot have an order for a customer that does not exist).
    
- **Positional Quality:** In geographic data (like the Codepoint example), numerical indicators reflect the accuracy of coordinates, serving as an attribute that defines the reliability of the data instance.
### Case Study: The Book Entity

If we model a "Book," the attributes change based on who is using the data:

- **Amazon View:** Focuses on **Sales, Dimensions, Price, and Reviews**.
    
- **Library View:** Focuses on **Condition and "Dewey" Decimal Number**.
    
- **Shared Attributes:** Both need **ISBN, Author, Title, and Publisher**.
    

> _"What something is depends on what it does."_ — The modelling domain provides the meaning of an entity.