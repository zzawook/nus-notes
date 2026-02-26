# L03: Data Warehousing and Dimensional Modelling

**CS4221 - Database Tuning** | NUS Computer Science

> A **data warehouse** is a separate database optimized for *analyzing* business data, as opposed to the operational databases that handle day-to-day transactions. The key idea: **OLTP systems do the business; the data warehouse helps you understand the business.**

---

## Table of Contents

1. [OLTP vs OLAP](#1-oltp-vs-olap)
2. [Data Warehouse](#2-data-warehouse)
3. [Dimensional Modelling](#3-dimensional-modelling)
4. [Star Schema](#4-star-schema)
5. [Fact Table](#5-fact-table)
6. [Dimension Tables](#6-dimension-tables)
7. [Hierarchy and Decomposition](#7-hierarchy-and-decomposition)
8. [OLAP Operations: Slice/Dice](#8-olap-operations-slicedice)
9. [Aggregation Queries and Window Functions](#9-aggregation-queries-and-window-functions)
10. [CUBE, ROLLUP, and Drill-Down](#10-cube-rollup-and-drill-down)
11. [Bus Architecture](#11-bus-architecture)
12. [Building a Data Warehouse](#12-building-a-data-warehouse)

---

## 1. OLTP vs OLAP

### Motivation (Kimball Quote)

> *"The users of an **operational system** turn the wheels of the organization. They take orders, sign up new customers, and log complaints. Users of an operational system almost always deal with one record at a time. They repeatedly perform the same operational tasks over and over."*
>
> *"The users of a **data warehouse**, on the other hand, watch the wheels of the organization turn. They count the new orders and compare them with last week's orders and ask why the new customers signed up and what the customers complained about. Users of a data warehouse almost never deal with one row at a time. Rather, their questions often require that hundreds or thousands of rows be searched and compressed into an answer set."*
>
> -- **The Data Warehouse Toolkit**, by Ralph Kimball and Margy Ross

### OLTP (Online Transaction Processing)

**Examples:** Airline reservation, banking, university systems, e-shops, address books, student management portals.

**Characteristics:**
- Many **short transactions** involving **updates** and mostly **point queries**
- Examples: update account balance, enroll in a course, add book to shopping cart

**Concerns:**
- Queries touch **small amounts of data** (e.g., one or two records)
- Updates are **frequent**
- Data must be **up-to-date** and **consistent** at all times
- **Concurrency** is the biggest performance concern

### OLAP (Online Analytical Processing)

**Examples:** Business analysis, reporting, auditing, forecasting, business intelligence.

**Characteristics:**
- **Long transactions** involving **complex queries**
- Examples: report total sales per department per month, identify top-selling books, count classes with fewer than ten students

**Concerns:**
- Queries touch **large amounts of data**
- Updates are **infrequent** (e.g., only at the beginning)
- Individual queries can require **lots of resources**
- Operating on **static snapshots** of data and **approximate answers** may be acceptable

### Comparison Table

| Aspect | **OLTP** | **OLAP** |
|---|---|---|
| **User** | Clerk, IT professional | Knowledge worker |
| **Function** | Day-to-day operations | Decision support |
| **DB Design** | Application-oriented | Subject-oriented |
| **Data** | Current, up-to-date; Detailed; Flat relational; Isolated | Historical; Summarized; Multi-dimensional; Integrated, consolidated |
| **Usage** | Repetitive | Ad-hoc |
| **Access** | Read/Write; Index on primary key | Read mostly; Lots of scans |
| **Unit of Work** | Short, simple transaction | Complex queries |
| **#Records Accessed** | Tens | Millions |
| **#Users** | Thousands | Hundreds |
| **DB Size** | 100MB to GB | 100GB to TB |
| **Metric** | Transaction throughput | Query throughput, response |

### The Problem

Doing OLTP and OLAP in the **same database system** is often **impractical**:
- An analyst's query that calculates the **sum of all sales** acquires **locks on the sales table for consistency** -- new sales transactions are **blocked**

> **Think of it this way:** Imagine a librarian trying to count every book in the library while people are constantly borrowing and returning books. The librarian either blocks everyone from touching books during the count, or gets an inaccurate count. Neither is acceptable -- so we use a separate system for counting (the data warehouse).

### The Solution

A dedicated **data warehouse** that:
- Makes the organization's information easily **accessible**
- Presents information **consistently**
- Is **adaptive** and resilient to change
- **Protects** the organization's information assets
- Serves as the **foundation** for improved decision-making
- Is **accepted** by the business community

> **OLTP** = Doing the business
> **OLAP** = Understanding the business

---

## 2. Data Warehouse

### Two Schools of Thought: Inmon vs Kimball

#### Bill Inmon (Top-Down)

> *"A data warehouse is a subject-oriented, integrated, nonvolatile, and time-variant collection of data in support of management's decisions. The data warehouse contains granular corporate data."*
> -- **Building the Data Warehouse**, by Bill Inmon

- Advocates a centralized **enterprise data warehouse**
- Integrates data from various sources, providing a **holistic view** of enterprise data
- Emphasises a **normalized schema** (minimizing redundancy, maintaining data integrity and consistency)
- **Top-down design**: start with overall enterprise data model, then create specific subject-area data marts

#### Ralph Kimball (Bottom-Up)

> *"A data warehouse is a copy of transaction data specifically structured for query and analysis. In its most simplistic form, a data mart presents the data from a single business process. These business processes cross the boundaries of organizational functions."*
> -- **The Data Warehouse Toolkit**, by Ralph Kimball

- **Bottom-up design**: start with individual **data marts**, each for a particular **business process**
- Data marts are then integrated into a broader data warehouse
- Promotes **dimensional modelling**, specifically **star schema**
- Advocates a **decentralized data warehouse** comprised of data marts

### Kimball's Principles for Data Warehousing

| Principle | Description |
|---|---|
| **Consistency** | Information must be presented in a **standardized** manner |
| **Accessibility** | Data should be easily **accessible** for analysis and decision-making |
| **Security** | Warehouse must **protect** the organization's information assets |
| **Adaptability** | Must be **resilient and responsive** to business and technical changes |
| **Acceptance** | Must be **accepted by business users** and seen as valuable |
| **Decision Support** | Serves as the **foundation** for improved decision-making |

---

## 3. Dimensional Modelling

### Business Process Example: Retail

Consider a retail **business process**. The **transactions** recorded are the **individual sales** at each point-of-sale as recorded on the receipt.

| product | date | store | quantity |
|---|---|---|---|
| FAIRPRICE PREMIUM OYSTER SAUCE | 12/03/13 | Toa Payoh Lorong 4 Blk 192 | 12 |
| FAIRPRICE PREMIUM OYSTER SAUCE | 12/03/13 | 900 South Woodlands Drive | 11 |
| BONCAFE FILTERS BAGS - NATURAL | 13/03/13 | Toa Payoh Lorong 4 Blk 192 | 34 |
| CLOROX BLEACH - LEMON | 13/03/13 | Yishun Ave 9, Blk 10 | 4 |

### Data Mart as a Cube

- A data mart can be seen as a **cube** containing individual **facts** (i.e., line of a receipt) and their **measures** (i.e., quantity sold) of the transactions
- Each entry in a cell is a single value (or set of values) called a **measure**
- Other values become **dimensions**
- The cube has three **dimensions** (product, date, store). It could be a **hypercube** with more dimensions (e.g., customers, promotions, etc.)

> **Think of it this way:** Imagine a 3D spreadsheet. Each axis represents a dimension (e.g., product on the x-axis, date on the y-axis, store on the z-axis). Each cell at the intersection of these three axes contains the measure (e.g., quantity sold). With more than 3 dimensions, it becomes a hypercube that we can't physically visualize, but the concept is the same.

### Entity-Relationship Diagram

The cube can be modelled by an **entity-relationship diagram** with:
- One **n-ary relationship** (the facts)
- Many **entity sets** (the dimensions)

The number of entity sets equals the number of dimensions (i.e., degrees of freedom).

---

## 4. Star Schema

### Definition

A **star schema** is composed of **one fact table** and **several dimension tables**.

The diagram looks like a star: the fact table is in the center, connected to each dimension table around it.

**Structure:**
```
          [Date Dimension]
                |
[Store Dimension] --- [Fact Table] --- [Product Dimension]
```

**Fact Table contains:**
- **Dimensions** (foreign keys): Product Key, Date Key, Store Key
- **Measures**: Quantity

**Dimension Tables contain:**
- Surrogate Key (primary key)
- Descriptive Attributes

---

## 5. Fact Table

### Definition and Properties

The **fact table** records, for each of the business process, its **measures** (also known as **facts**, e.g., quantity, total price, etc.). It also records the **surrogate keys** of the **dimension rows** in the different **dimension tables** that describe the transaction (e.g., the product, the date, the store, etc.).

**Key property:** The fact table records the transactions of the business process at the **finest available granularity**.

### Example

| product | date | store | quantity |
|---|---|---|---|
| 1 | 1 | 1 | 12 |
| 1 | 1 | 2 | 11 |
| 2 | 2 | 1 | 34 |
| 2 | 2 | 1 | 3 |
| 3 | 2 | 3 | 4 |
| 2 | 2 | 3 | 12 |

- The product, date, and store columns contain **surrogate keys** (integers referencing dimension tables)
- Surrogate keys replace NULLs (better than NULL values)

---

## 6. Dimension Tables

### Definition

The **dimension tables** provide as comprehensive as possible a **description** of the dimension for the sake of analysis. Each entry in a dimension table has a **surrogate key** used in the fact table to refer to it (i.e., a kind of foreign key).

> A **surrogate key** is a **synthetic key** not derived from application data (i.e., unlike natural key).

### Product Dimension

| product | sku | name | category | price |
|---|---|---|---|---|
| 1 | 261721 | FAIRPRICE PREMIUM OYSTER SAUCE | Groceries | 2.6 |
| 2 | 263789 | BONCAFE FILTERS BAGS - WHITE | Beverages | 2.3 |
| 3 | 265147 | CLOROX BLEACH - LEMON | Household Items | 4.25 |

Records: name, stock keeping unit number (SKU), category, unit price, etc.

**Special Rows:** May also have rows for special products (e.g., without SKU) and unidentified products (i.e., better than NULL values).

### Store Dimension

| store | postal | address | blkno | area | phone |
|---|---|---|---|---|---|
| 1 | 310192 | Toa Payoh Lorong 4 | Blk 192 | TOA PAYOH | 65208019 |
| 2 | 730900 | South Woodlands Drive | NO. 900 | WOODLANDS | 64582558 |
| 3 | 768888 | Yishun Ave 9 | Blk 10 | YISHUN | 67665009 |

Records: name, location (address), phone number, etc.

**Special Rows:** May also have rows for unidentified stores.

### Date Dimension

| date | date_actual | day_name | month_actual | ... |
|---|---|---|---|---|
| 1 | 12/03/13 | Tuesday | 3 | ... |
| 2 | 13/03/13 | Wednesday | 3 | ... |

Records: date in different formats, day of the week, position in the calendar, fiscal year, etc.

**Special Rows:** May also have rows for unidentified dates.

### Date Dimension Schema (Full)

```sql
CREATE TABLE date (
    date              INT PRIMARY KEY,
    date_actual       DATE NOT NULL,
    epoch             BIGINT NOT NULL,
    day_suffix        VARCHAR(4) NOT NULL,
    day_name          VARCHAR(9) NOT NULL,
    day_of_week       INT NOT NULL,
    day_of_month      INT NOT NULL,
    day_of_quarter    INT NOT NULL,
    day_of_year       INT NOT NULL,
    week_of_month     INT NOT NULL,
    week_of_year      INT NOT NULL,
    week_of_year_iso  CHAR(10) NOT NULL,
    month_actual      INT NOT NULL,
    month_name        VARCHAR(9) NOT NULL,
    month_name_abbr   CHAR(3) NOT NULL,
    quarter_actual    INT NOT NULL,
    quarter_name      VARCHAR(9) NOT NULL,
    year_actual       INT NOT NULL,
    first_day_of_week     DATE NOT NULL,
    last_day_of_week      DATE NOT NULL,
    first_day_of_month    DATE NOT NULL,
    last_day_of_month     DATE NOT NULL,
    first_day_of_quarter  DATE NOT NULL,
    last_day_of_quarter   DATE NOT NULL,
    first_day_of_year     DATE NOT NULL,
    last_day_of_year      DATE NOT NULL,
    mmyyyy            CHAR(6) NOT NULL,
    mmddyyyy          CHAR(10) NOT NULL,
    weekend_indr      BOOLEAN NOT NULL
);
```

---

## 7. Hierarchy and Decomposition

### Field Hierarchy

Different fields of a dimension may define **hierarchies**. For instance:
- **Calendar hierarchy:** Calendar Year > Calendar Quarter > Calendar Month > Day
- **Fiscal hierarchy:** Fiscal Year > Fiscal Quarter > Fiscal Month > Day
- **Week hierarchy:** Week > Day

A dimension table may **play several roles** in a star schema. For instance, the **date** dimension can be used to indicate the **order** dates, **delivery** dates, and **payment** dates of an order in an inventory star schema.

### No Decomposition (No Snowflake Schema)

Kimball advocates **not to further decompose** the entity sets (i.e., no snowflake schema). Instead:
- Information can be **repeated in the dimensions** for simplicity and efficiency
- **Normalization is not a concern** because data should have been **cleaned** and there is **no update**

> **Think of it this way:** In an OLTP system, we normalize to avoid update anomalies. But a data warehouse is **read-only** (no updates!) -- so the main reason for normalization disappears. Keeping everything in flat dimension tables makes queries simpler and faster (fewer joins).

**Example:** Even if `blkno` and `street` can uniquely identify the `area`, we do NOT decompose `store` into separate `store` and `area` tables. This decomposition is **unnecessary** because the check should have been done during ETL and there is no update in the warehouse.

---

## 8. OLAP Operations: Slice/Dice

### Slice and Dice Operation

Queries typically **slice** and **dice** the cube. They also **aggregate** the measure.

This maps to SQL as:
- **Slice/Dice** = `WHERE` clause (select = filtering conditions)
- **Project** = `SELECT` clause (choosing which columns to display)

**Example:** For each month, what is the total sales of all products in the store in Woodlands.

### Slice vs Dice

| Operation | Definition | Example |
|---|---|---|
| **Slice** | Select a layer of the cube by **fixing a dimension** | View total sales for each store |
| **Dice** | Select a sub-cube by applying **filters on multiple dimensions** | View sales of sauces for stores in Woodlands by month |

---

## 9. Aggregation Queries and Window Functions

### Natural Join and Aggregation Function

Queries typically calculate aggregate functions on the **natural join** (assuming Universal relation) of the fact table and dimension tables needed to express the conditions and group.

- **Conditions** (i.e., `WHERE`) and **grouping** (i.e., `GROUP BY`) are on **attributes of specific dimensions**

```sql
SELECT <grouped attributes of the dimension>,
       <aggregation of measures (i.e., attributes of fact tables)>
FROM sales
  NATURAL JOIN date
  NATURAL JOIN product
  NATURAL JOIN store
WHERE
  <conditions on attributes of the dimensions>  -- => slice/dice
GROUP BY
  <attributes of the dimension>                 -- => aggregation
```

> **Note:** NATURAL JOIN allows us to forget about surrogate keys -- the join is done automatically on matching column names.

### Window Functions

Queries can also use **window functions** over partitions by specific dimensions on the natural join of the fact table with dimension tables.

A **window function** performs calculation across a set of rows related to the current row.

```sql
SELECT <dimension columns>,
  <window_function(measure)>
    OVER
    (PARTITION BY <dimensions> ORDER BY <ordering columns>)
FROM sales
  NATURAL JOIN date
  NATURAL JOIN product
  NATURAL JOIN store
WHERE
  <conditions on attributes of the dimensions>
```

---

## 10. CUBE, ROLLUP, and Drill-Down

### Hierarchies (Hasse Diagram)

Users can take advantage of the hierarchies defined by different fields of the dimensions of the cube structure to **roll-up** or **drill-down**.

```
            [Store, Date, Product]        -- most detailed
           /          |          \
    [Store, Date] [Store, Product] [Date, Product]
           \          |          /
         [Store]    [Date]    [Product]
                \     |     /
                  [Total]                 -- most aggregated

  Drill-Down = going UP (adding dimensions)
  Roll-Up    = going DOWN (removing dimensions)
```

### CUBE

`CUBE` is a subclause of `GROUP BY` that defines **multiple simultaneous grouping sets**. CUBE generates **all possible** grouping sets of the input columns. It answers the question: "Give me the aggregation for **every possible combination** of these dimensions."

`CUBE(A,B,C)` is equivalent to the (ordered) union of the query with **no GROUP BY** (at the end) and the seven queries with:
```
GROUP BY A, B, C
GROUP BY A, B
GROUP BY A, C
GROUP BY B, C
GROUP BY A
GROUP BY B
GROUP BY C
```

**Example:**

```sql
SELECT p.category, st.area,
  ROUND(AVG(s.quantity * p.price), 2) AS avgvolume
FROM product p, sales s, store st
WHERE p.product = s.product
  AND s.store = st.store
GROUP BY CUBE(p.category, st.area);
```

| category | area | avgvolume |
|---|---|---|
| Household Items | YISHUN | 5178.07 |
| Groceries | YISHUN | 3787.16 |
| *null* | YISHUN | 8965.23 | *(subtotal for YISHUN)* |
| ... | ... | ... |
| Toiletries | *null* | 6203.28 | *(subtotal for Toiletries)* |
| *null* | *null* | 25168.51 | *(grand total)* |

CUBE calculates the average sales: **overall**, **by area**, **by category**, **by category and area**.

### ROLLUP

`ROLLUP` is a subclause of `GROUP BY` that defines **multiple simultaneous grouping sets**, **considering the order of input columns**. It answers the question: "Give me the aggregation at **each level of this hierarchy**, from most detailed to grand total."

`ROLLUP(A,B,C)` is equivalent to the (ordered) union of the query with no `GROUP BY` (at the end) and the three queries with:
```
GROUP BY A, B, C
GROUP BY A, B
GROUP BY A
```

> **Key difference from CUBE:** ROLLUP is **order-dependent** -- it only generates grouping sets that progressively drop the rightmost column. CUBE generates **all** combinations. So `ROLLUP(A,B,C)` gives 4 grouping sets while `CUBE(A,B,C)` gives 2^3 = 8.

**Example:**

```sql
SELECT p.category, st.area,
  ROUND(AVG(s.quantity * p.price), 2) AS avgvolume
FROM product p, sales s, store st
WHERE p.product = s.product
  AND s.store = st.store
GROUP BY ROLLUP(p.category, st.area);
```

| category | area | avgvolume |
|---|---|---|
| Toiletries | BISHAN | 1223.66 |
| Toiletries | JURONG EAST | 4979.62 |
| Toiletries | *null* | 6203.28 | *(subtotal for Toiletries)* |
| ... | ... | ... |
| *null* | *null* | 25168.51 | *(grand total)* |

ROLLUP calculates the average sales: **overall**, **by category**, **by category and area**.

> **Note:** ROLLUP does NOT compute by area alone (unlike CUBE which would).

### Drill-Down

Drill-Down can be done using `GROUP BY` by **adding dimensions**. There is no special subclause needed.

**Before Drill-Down (by category only):**
```sql
SELECT p.category,
  ROUND(AVG(s.quantity * p.price), 2) AS avgvolume
FROM product p, sales s, store st
WHERE p.product = s.product
  AND s.store = st.store
GROUP BY p.category;
```

| category | avgvolume |
|---|---|
| Toiletries | 6203.28 |
| Groceries | 5513.19 |

**After Drill-Down (add area):**
```sql
SELECT p.category, st.area,
  ROUND(AVG(s.quantity * p.price), 2) AS avgvolume
FROM product p, sales s, store st
WHERE p.product = s.product
  AND s.store = st.store
GROUP BY p.category, st.area;
```

| category | area | avgvolume |
|---|---|---|
| Toiletries | BISHAN | 1223.66 |
| Toiletries | JURONG EAST | 4979.62 |

### General Queries

More generally, queries may involve:
- Complex Boolean conditions
- Grouping, partitioning, and sorting
- Aggregate and window functions
- Statistical functions
- Time series functions
- Spatial data functions

---

## 11. Bus Architecture

### Definition

A data warehouse may contain **several fact tables** that **share common dimensions**. This is known as the **bus architecture**.

**Example:**
```
Dimensions:  date   product   store   promotion   vendor   warehouse   shipper
               |      |        |         |          |         |           |
Fact:     [Purchase Order] ---|---------|----------|---------|-----------|
               |      |        |
Fact:        [Inventory] -----|
               |      |        |         |
Fact:          [Sales] -------|---------|
```

- Multiple fact tables (Purchase Order, Inventory, Sales) share common dimensions (date, product, store)
- Each fact table connects to the dimensions relevant to its business process

---

## 12. Building a Data Warehouse

### Steps

1. **Data Integration with ETL**
   - **Extract** data from various OLTP (transactional) systems
   - **Transform** data through cleaning, deduplication, type casting, business rule enforcement
   - **Load** the cleaned and integrated data into warehouse (`INSERT INTO ... VALUES`)

2. **Dimensional Modeling for OLAP**
   - **Simplify** the design using star schema (`CREATE TABLE ... AS (SELECT FROM WHERE)`)

3. **Database Optimization**
   - **Optimize** data storage, tune DBMS for read-heavy access patterns
   - Use a dedicated system for OLAP

4. **Enable BI and Exploration**
   - **Build** dashboards, reports, and interactive tools for end-users
   - Leverage visualization platforms (e.g., Tableau, Power BI, etc.)

### Four Components of a Data Warehouse Application

| Component | Description |
|---|---|
| **Data Staging** | Extract, transform, and prepare data from source systems |
| **Warehousing** | Store the cleaned and integrated data |
| **OLAP (Analytical)** | Perform analytical processing on the data |
| **Visualization and Exploration** | Reports, dashboards, and interactive exploration |

### Data Warehouse Ecosystems

**Extract-Transform-Load (ETL):**
- IBM InfoSphere DataStage
- Oracle Warehouse Builder
- SQL Server Integration Services
- Pentaho Kettle *(open-source)*

**Data Warehouse Management System:**
- Oracle, IBM DB2, Microsoft SQL Server, PostgreSQL
- Teradata
- SAP HANNA

**Online Analytical Processing and Exploratory Data Analysis:**
- Microsoft Excel and Power BI
- Oracle Reports
- Cognos
- Tableau
- Business Intelligence and Reporting Tools (BIRT)
- Data mining tools
- Machine learning algorithms
- Python with Pandas

### Life Cycle

The development and life cycle of a data warehouse application:

```
Business Requirements
  ├── Technical architecture design → Product selection and installation
  ├── Modelling → Physical design → ETL & data staging design and development → Deployment and maintenance
  └── Analytical application specification → Analytical application development
```

---

## Quick Reference: CUBE vs ROLLUP

| Feature | **CUBE** | **ROLLUP** |
|---|---|---|
| **Grouping sets** | All possible combinations | Order-dependent, progressive removal |
| **CUBE/ROLLUP(A,B,C)** generates | 2^3 = 8 grouping sets | 3+1 = 4 grouping sets |
| **Includes GROUP BY B alone?** | Yes | No |
| **Use case** | Cross-tabulation / pivot tables | Hierarchical subtotals |

## Quick Reference: Star Schema Components

| Component | Description | Key Property |
|---|---|---|
| **Fact Table** | Central table with measures and surrogate keys | Records at **finest granularity** |
| **Dimension Table** | Descriptive tables around the fact table | Uses **surrogate keys** (synthetic, not natural) |
| **Measures** | Numeric values in the fact table (qty, price, etc.) | Aggregated in queries |
| **Star Schema** | One fact table + several dimension tables | No decomposition (no snowflake) |
| **Bus Architecture** | Multiple fact tables sharing common dimensions | Enables cross-process analysis |

## Quick Reference: Inmon vs Kimball

| Aspect | **Inmon (Top-Down)** | **Kimball (Bottom-Up)** |
|---|---|---|
| **Approach** | Enterprise data warehouse first | Individual data marts first |
| **Schema** | Normalized | Dimensional (star schema) |
| **Structure** | Centralized | Decentralized (data marts) |
| **Design** | Top-down (enterprise model → data marts) | Bottom-up (data marts → warehouse) |
| **Focus** | Data integrity, consistency | Simplicity, query performance |
