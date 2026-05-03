<a name="top"></a>

# 📚 Data Analytics Learning Guide

A consolidated reference built from 20 real repos — covering every tool in the data analyst stack, organised by technology and skill level. Each section maps directly to a working project or study repo.

---

## 📋 Table of Contents

**By Technology**
- [🐍 Python](#-python)
- [🐼 Pandas](#-pandas)
- [🗄️ SQL Server (T-SQL)](#️-sql-server-t-sql)
- [🐬 MySQL](#-mysql)
- [🐘 PostgreSQL](#-postgresql)
- [📊 Excel](#-excel)
- [📈 Tableau](#-tableau)
- [💡 Power BI](#-power-bi)

**By Level**
- [🟢 Beginner](#-beginner)
- [🟡 Intermediate](#-intermediate)
- [🔴 Advanced / Projects](#-advanced--projects)

---

---

# 🐍 Python

---

## 🟢 Python — Beginner: Foundations
> **Repo:** `python-learning-journey`

A linear study guide from variables → web scraping. Each concept builds on the last.

### 1. Variables & Data Types

```python
# Variables — named containers
name       = "Alice"      # str
age        = 25           # int
height     = 1.68         # float
is_student = True         # bool
fruits     = ["apple", "banana"]   # list
person     = {"name": "Alice"}     # dict
coords     = (10.5, 20.3)          # tuple
```

> Python infers types automatically — no need to declare them upfront.

---

### 2. Comparison, Logical & Membership Operators

```python
x = 10; y = 5
x > y        # True   — greater than
x == y       # False  — equal to
x != y       # True   — not equal to

age = 20; has_id = True
age >= 18 and has_id   # True  — AND: both must be True
age < 18  or has_id    # True  — OR: at least one True
not has_id             # False — flips boolean

fruits = ["apple", "banana"]
"apple" in fruits      # True  — membership check
"grape" not in fruits  # True
```

---

### 3. if-elif-else

```python
score = 75

if score >= 90:
    print("Grade: A")
elif score >= 80:
    print("Grade: B")
elif score >= 70:
    print("Grade: C")    # ← this runs
else:
    print("Grade: F")
```

> Python checks top to bottom — first `True` block wins.

---

### 4. For Loops & Nested Loops

```python
# Basic for loop
for fruit in ["apple", "banana", "cherry"]:
    print(fruit)

# range() — stop is excluded
for i in range(1, 6):
    print(i)             # 1 2 3 4 5

# Nested — inner loop runs fully for each outer step
for i in range(2, 4):
    for j in range(1, 6):
        print(f"{i} x {j} = {i*j}")
```

---

### 5. While Loops

```python
count = 1
while count <= 5:
    print(f"Count: {count}")
    count += 1      # always increment — prevents infinite loop
```

> ⚠️ If the condition never becomes `False`, the loop runs forever.

---

### 6. Functions

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")                  # Hello, Alice!
greet("Bob", "Good morning")    # Good morning, Bob!

def add(a, b):
    return a + b

result = add(3, 7)    # 10
```

---

### 7. Type Conversion

```python
int("25")         # → 25     (string to int)
float("9.99")     # → 9.99   (string to float)
str(100)          # → "100"  (int to string — needed for concatenation)
int(9.99)         # → 9      (truncates, does NOT round)
list("hello")     # → ['h','e','l','l','o']
```

---

### 8. BeautifulSoup & Requests

```python
import requests
from bs4 import BeautifulSoup

response = requests.get("https://example.com")
print(response.status_code)   # 200 = OK

soup = BeautifulSoup(response.text, 'html.parser')

soup.find('p')           # first matching element
soup.find_all('p')       # list of ALL matching elements
```

**Common HTML tags to know:**

| Tag | What it is |
|---|---|
| `<table>` | Data table |
| `<tr>` | Table row |
| `<th>` | Header cell |
| `<td>` | Data cell |
| `<div>` | Block container |

> ⚠️ `find_all()` returns a **list** — always loop before calling `.text.strip()`

---

### 9. Mini Projects

**BMI Calculator** — input + casting + conditions + functions

**Automatic File Sorter** — `os` + `shutil` + loops + dictionary

```python
import os, shutil

folder_path = r"C:\Users\Downloads"
file_types = {
    "Images":   [".jpg", ".png", ".gif"],
    "Documents":[".pdf", ".docx", ".txt"],
    "Code":     [".py", ".js", ".html"],
}

for filename in os.listdir(folder_path):
    file_path = os.path.join(folder_path, filename)
    if os.path.isdir(file_path):
        continue
    _, ext = os.path.splitext(filename)
    for folder_name, extensions in file_types.items():
        if ext.lower() in extensions:
            dest = os.path.join(folder_path, folder_name)
            os.makedirs(dest, exist_ok=True)
            shutil.move(file_path, dest)
            break
```

---

## 🟡 Python — Intermediate: Web Scraping
> **Repos:** `python-webscraping-wikipedia`, `python-webscraping-amazon`

### Wikipedia Scraper (13 Steps)

```python
from bs4 import BeautifulSoup
import requests
import pandas as pd

# Step 1-3: set User-Agent header (disguise as browser) + fetch + parse
headers = {'User-Agent': 'Mozilla/5.0 ...'}
url = 'https://en.wikipedia.org/wiki/List_of_largest_companies_in_the_United_States_by_revenue'
page = requests.get(url, headers=headers)
soup = BeautifulSoup(page.text, 'html.parser')

# Step 4-5: locate the right table (pages have multiple)
table = soup.find_all('table')[0]

# Step 6-8: extract and clean column headers
world_titles = table.find_all('th')
world_table_titles = [title.text.strip() for title in world_titles]

# Step 9-10: create empty DataFrame
df = pd.DataFrame(columns=world_table_titles)

# Step 11-12: loop through rows, populate DataFrame
column_data = table.find_all('tr')
for row in column_data[1:]:                  # skip header row
    row_data = row.find_all('td')
    individual_row_data = [data.text.strip() for data in row_data]
    df.loc[len(df)] = individual_row_data

# Step 13: export
df.to_csv('Companies.csv', index=False)
```

> Always check `robots.txt` before scraping. Wikipedia permits scraping for non-commercial use.

---

### Amazon Price Tracker

Full automated tracker: scrape → log → alert.

```python
from bs4 import BeautifulSoup
import requests, time, datetime, csv, smtplib
import pandas as pd

def check_price():
    # Use Session + cookies to avoid bot detection
    session = requests.Session()
    session.headers.update({
        "User-Agent": "Mozilla/5.0 ...",
        "Accept-Language": "en-US,en;q=0.9",
    })
    session.get("https://www.amazon.com")  # collect cookies first
    time.sleep(2)

    URL = 'https://www.amazon.com/...'
    page = session.get(URL)
    soup = BeautifulSoup(page.content, "html.parser")

    title = soup.find(id="productTitle").get_text().strip()
    price = soup.find(id="apex-pricetopay-accessibility-label").get_text().strip()[3:]
    today = datetime.date.today()

    # Append to CSV (create header on first run with 'w', then 'a+')
    with open('AmazonWebScraperDataset.csv', 'a+', newline='', encoding='UTF8') as f:
        csv.writer(f).writerow((title, price, today))

    if float(price.replace(',','')) < 300000:
        send_mail()

# Run every 30 min for 24 hours
for i in range(48):
    check_price()
    time.sleep(1800)
```

**Email alert function:**
```python
def send_mail():
    server = smtplib.SMTP_SSL('smtp.gmail.com', 465)
    server.login('your@gmail.com', 'app_password')   # use App Password, not login password
    msg = "Subject: Price Alert!\n\nProduct dropped below target price."
    server.sendmail('your@gmail.com', 'your@gmail.com', msg)
    server.quit()
```

> ⚠️ Never hardcode passwords. Use a `.env` file + `python-dotenv`.

---

## 🔴 Python — Advanced: ETL Pipeline (REST API → MySQL)
> **Repo:** `epl-standings-2024-25`

**Problem:** Sports bar Wi-Fi overloaded by staff context-switching. **Solution:** Pull EPL standings from API → store in MySQL → display on screens.

**Pipeline:**
```
API Football → Python (Extract → Transform → Load) → MySQL
```

```python
import os, json, requests, pandas as pd
from mysql import connector
from dotenv import load_dotenv

load_dotenv()

# --- EXTRACT ---
url = "https://api-football-v1.p.rapidapi.com/v3/standings"
headers = {"x-rapidapi-key": os.getenv("API_KEY"), "x-rapidapi-host": os.getenv("API_HOST")}
response = requests.get(url, headers=headers, params={"league": 39, "season": 2024})
standings_list = response.json()["response"][0]["league"]["standings"][0]

# --- TRANSFORM ---
rows = []
cols = ['season','position','team_id','team','played','won','draw','lost',
        'goals_for','goals_against','goal_diff','points','form']

for club in standings_list:
    rows.append((2024, club['rank'], club['team']['id'], club['team']['name'],
                 club['all']['played'], club['all']['win'], club['all']['draw'],
                 club['all']['lose'], club['all']['goals']['for'],
                 club['all']['goals']['against'], club['goalsDiff'],
                 club['points'], club['form']))

df = pd.DataFrame(rows, columns=cols)

# --- LOAD ---
db = connector.connect(host=os.getenv("MYSQL_HOST"), user=os.getenv("MYSQL_USER"),
                       password=os.getenv("MYSQL_PASSWORD"), database=os.getenv("MYSQL_DATABASE"))
cur = db.cursor()

UPSERT_SQL = """
INSERT INTO standings (season,position,team_id,team,played,won,draw,lost,
    goals_for,goals_against,goal_diff,points,form)
VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s) as src
ON DUPLICATE KEY UPDATE
    position=src.position, played=src.played, points=src.points, form=src.form;
"""
cur.executemany(UPSERT_SQL, list(df.itertuples(index=False, name=None)))
db.commit()
```

**Data quality checks:**
1. Must have exactly 20 teams
2. All cells populated
3. Numeric fields are integers

[↑ Back to Top](#top)

---

---

# 🐼 Pandas

---

## 🟢 Pandas — Beginner: Core Patterns
> **Repo:** `pandas-learning-journey`

### Reading Files

```python
import pandas as pd

df = pd.read_csv("file.csv")
df = pd.read_csv("file.txt", sep='\t')
df = pd.read_json("data.json")
df = pd.read_excel("workbook.xlsx", sheet_name='Sheet1')

pd.set_option('display.max_rows', 235)

df.shape        # (rows, columns)
df.head(7)      # first 7 rows
df.tail(10)     # last 10 rows
df.loc[224]     # row by label
df.iloc[224]    # row by position
```

---

### Filtering & Ordering

```python
df[df['Rank'] <= 10]                              # condition filter
df[df['Country'].isin(['Bangladesh','Brazil'])]   # list filter
df[df['Country'].str.contains('United')]          # string filter

df.sort_values('Rank', ascending=False)
df.sort_values(by=['Country','Rank'], ascending=[False, True])
```

---

### Indexes

```python
df = pd.read_csv("file.csv", index_col='Country')  # set on load
df.reset_index(inplace=True)                        # back to default
df.set_index('Country', inplace=True)               # set after

df.loc['Albania']    # by label
df.iloc[1]           # by position (always integer)

# Multi-level index
df.set_index(['Country','Continent'], inplace=True)
df.loc['South America', 'Argentina']
```

> 💡 `loc` = labels · `iloc` = integers — always.

---

### Group By & Aggregation

```python
df.groupby('Base Flavor').mean(numeric_only=True)

df.groupby('Base Flavor').agg({
    'Flavor Rating':  ['mean','max','count','sum'],
    'Texture Rating': ['mean','max','count','sum']
})

df.groupby(['Base Flavor','Liked']).describe()
```

---

### Merge, Join & Concat

```python
# MERGE — SQL-style join on columns
df1.merge(df2, how='inner', on='key')   # matched rows only
df1.merge(df2, how='left')              # all of df1
df1.merge(df2, how='outer')             # all rows from both

# JOIN — index-based
df1.set_index('ID').join(df2.set_index('ID'), how='outer')

# CONCAT — stack DataFrames
pd.concat([df1, df2], join='outer', axis=1)
```

---

### Data Cleaning

```python
df.drop_duplicates(inplace=True)
df.drop(columns='Not_Useful_Column', inplace=True)
df["Last_Name"] = df["Last_Name"].str.strip("123._/")
df["Phone_Number"] = df["Phone_Number"].str.replace('[^a-zA-Z0-9]','', regex=True)

# Split one column into multiple
df[["Street","State","Zip"]] = df["Address"].str.split(',', n=2, expand=True)

df = df.fillna('')
df.reset_index(drop=True, inplace=True)
df.to_csv("cleaned.csv", index=False)
```

---

### EDA

```python
df.info()                    # dtypes + non-null counts
df.describe()                # stats per column
df.isnull().sum()            # nulls per column
df.nunique()                 # unique values per column
df.corr(numeric_only=True)   # correlation matrix

import seaborn as sns, matplotlib.pyplot as plt
sns.heatmap(df.corr(numeric_only=True), annot=True)
plt.show()
```

---

### Visualization

```python
df.plot(kind='line')
df.plot.barh(stacked=True)
df.plot.scatter(x='col1', y='col2')
df.plot.hist(bins=10)
df.boxplot()
df.plot.area()
df.plot.pie(y='col')
plt.show()
```

[↑ Back to Top](#top)

---

---

# 🗄️ SQL Server (T-SQL)

---

## 🟢 SQL Server — Beginner
> **Repo:** `sql-learning-journey` Topics 1–16

### Filtering & Aggregation

```sql
-- Average score per country, skip zeros, only groups with avg > 430
SELECT AVG(score) AS total_score, country
FROM customers
WHERE score != 0
GROUP BY country
HAVING AVG(score) > 430
ORDER BY total_score DESC;

-- Top 5 customers by score
SELECT TOP 5 * FROM customers ORDER BY score DESC;
```

### DDL — CREATE, ALTER, DROP

```sql
CREATE TABLE persons (
    id INT NOT NULL,
    person_name VARCHAR(50) NOT NULL,
    birth_date DATE,
    CONSTRAINT pk_persons PRIMARY KEY (id)
)
ALTER TABLE persons ADD email VARCHAR(50) NOT NULL
ALTER TABLE persons DROP COLUMN phone
DROP TABLE persons
```

### DML — INSERT, UPDATE, DELETE, TRUNCATE

```sql
INSERT INTO customers (id, first_name, country, score)
VALUES (8,'Max','USA',NULL), (9,'Andreas','Germany',NULL)

UPDATE customers SET score = 0 WHERE score IS NULL
DELETE FROM customers WHERE id > 5
TRUNCATE TABLE persons   -- removes all rows, keeps structure
```

### Filtering Operators

```sql
WHERE score BETWEEN 350 AND 500
WHERE country IN ('USA','Germany')
WHERE first_name LIKE 'M%'      -- starts with M
WHERE first_name LIKE '%n'      -- ends with n
WHERE NOT country = 'USA'
```

### JOINs

```sql
-- INNER: matched rows only
SELECT c.first_name, o.order_id FROM customers c
INNER JOIN orders o ON c.id = o.customer_id

-- LEFT + IS NULL: customers with NO orders (anti-join)
SELECT c.first_name FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.customer_id IS NULL

-- CROSS: every combination
SELECT * FROM customers CROSS JOIN orders

-- Multi-table (4 tables)
SELECT o.OrderID, c.FirstName, p.Product, e.FirstName
FROM Sales.Orders o
LEFT JOIN Sales.Customers c  ON o.CustomerID   = c.CustomerID
LEFT JOIN Sales.Products  p  ON o.ProductID    = p.ProductID
LEFT JOIN Sales.Employees e  ON o.SalesPersonID = e.EmployeeID
```

### Set Operators

```sql
-- UNION: combine, remove duplicates
SELECT FirstName, LastName FROM Sales.Customers
UNION
SELECT FirstName, LastName FROM Sales.Employees

-- UNION ALL: keep duplicates (faster)
SELECT CustomerID FROM Sales.Orders UNION ALL
SELECT CustomerID FROM Sales.OrdersArchive

-- EXCEPT: in Employees but NOT in Customers
SELECT FirstName, LastName FROM Sales.Employees
EXCEPT
SELECT FirstName, LastName FROM Sales.Customers

-- INTERSECT: in BOTH tables
SELECT FirstName, LastName FROM Sales.Employees
INTERSECT
SELECT FirstName, LastName FROM Sales.Customers
```

### CASE WHEN — 4 Patterns

```sql
-- Pattern 1: value bucketing
CASE WHEN Sales > 50 THEN 'High' WHEN Sales > 20 THEN 'Medium' ELSE 'Low' END

-- Pattern 2: shorthand map
CASE Country WHEN 'Germany' THEN 'DE' WHEN 'USA' THEN 'US' ELSE 'N/A' END

-- Pattern 3: conditional COUNT inside GROUP BY
SUM(CASE WHEN Sales > 30 THEN 1 ELSE 0 END) AS HighSaleOrders

-- Pattern 4: CASE inside window function
CASE WHEN Score > AVG(Score) OVER() THEN 'Above Avg' ELSE 'Below Avg' END
```

---

## 🟡 SQL Server — Intermediate
> **Repo:** `sql-learning-journey` Topics 17–28

### Window Functions

```sql
-- Keep all rows, calculate at different partition levels
SELECT OrderID, Sales,
    SUM(Sales)  OVER()                          AS GrandTotal,
    SUM(Sales)  OVER(PARTITION BY CustomerID)   AS CustomerTotal,
    AVG(Sales)  OVER(PARTITION BY CustomerID)   AS CustomerAvg,
    RANK()      OVER(ORDER BY Sales DESC)       AS SalesRank
FROM Sales.Orders

-- Running total
SUM(Sales) OVER(PARTITION BY CustomerID ORDER BY OrderDate
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS RunningTotal

-- ROW_NUMBER vs RANK vs DENSE_RANK
ROW_NUMBER()  OVER(ORDER BY Sales DESC)  -- 1,2,3,4 — always unique
RANK()        OVER(ORDER BY Sales DESC)  -- 1,1,3   — skips after tie
DENSE_RANK()  OVER(ORDER BY Sales DESC)  -- 1,1,2   — no skip
```

### LAG, LEAD, FIRST_VALUE

```sql
-- Month-over-month change
SELECT MONTH(OrderDate) AS Mo,
    SUM(Sales) AS CurrentMonth,
    LAG(SUM(Sales)) OVER(ORDER BY MONTH(OrderDate)) AS PrevMonth,
    SUM(Sales) - LAG(SUM(Sales)) OVER(ORDER BY MONTH(OrderDate)) AS MoM_Change
FROM Sales.Orders GROUP BY MONTH(OrderDate)

-- Next order date per customer
LEAD(OrderDate) OVER(PARTITION BY CustomerID ORDER BY OrderDate) AS NextOrder

-- Lowest / highest per product
FIRST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales)      AS Lowest
FIRST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales DESC) AS Highest
```

### CTEs — Standalone, Chained & Recursive

```sql
-- Chained CTEs (max 3-5 per query — more = rethink structure)
WITH CTE_Sales AS (
    SELECT CustomerID, SUM(Sales) TotalSales FROM Sales.Orders GROUP BY CustomerID
),
CTE_Rank AS (
    SELECT CustomerID, TotalSales, RANK() OVER(ORDER BY TotalSales DESC) Rnk
    FROM CTE_Sales
)
SELECT c.FirstName, r.TotalSales, r.Rnk
FROM Sales.Customers c LEFT JOIN CTE_Rank r ON r.CustomerID = c.CustomerID

-- Recursive CTE: org chart with levels
WITH Hierarchy AS (
    SELECT EmployeeID, FirstName, ManagerID, 1 AS Level
    FROM Sales.Employees WHERE ManagerID IS NULL          -- top of tree (anchor)
    UNION ALL
    SELECT e.EmployeeID, e.FirstName, e.ManagerID, h.Level + 1
    FROM Sales.Employees e INNER JOIN Hierarchy h ON e.ManagerID = h.EmployeeID
)
SELECT * FROM Hierarchy ORDER BY Level
```

### Views & CTAS

```sql
-- View: saved query, no data stored
CREATE VIEW Sales.V_Monthly_Summary AS
SELECT DATETRUNC(month, OrderDate) AS Month, SUM(Sales) AS Total
FROM Sales.Orders GROUP BY DATETRUNC(month, OrderDate)

-- CTAS: physical snapshot table (faster for reporting than live queries)
SELECT DATENAME(month, OrderDate) AS Month, COUNT(OrderID) AS Total
INTO Sales.MonthlySnapshot
FROM Sales.Orders GROUP BY DATENAME(month, OrderDate)
```

---

## 🔴 SQL Server — Advanced: Indexing & Optimization
> **Repo:** `sql-learning-journey` Topics 28–36

### Index Types — When to Use Each

| Index Type | Use Case |
|---|---|
| **Clustered Rowstore** | Primary key, OLTP transaction tables |
| **Columnstore** | OLAP / data warehouse / large aggregation queries |
| **Non-Clustered** | Foreign keys, JOIN columns, WHERE filters |
| **Filtered** | Target a subset (e.g. `WHERE Country = 'USA'`) |
| **Unique** | Enforce uniqueness + improve lookup speed |
| **HEAP (no index)** | Staging tables — fastest inserts |

```sql
CREATE CLUSTERED INDEX idx_CustomerID ON Sales.DBCustomers(CustomerID)
CREATE NONCLUSTERED INDEX idx_LastName ON Sales.DBCustomers(LastName)
CREATE INDEX idx_CountryScore ON Sales.DBCustomers(Country, Score)  -- composite
CREATE UNIQUE NONCLUSTERED INDEX idx_Product ON Sales.Products(Product)
CREATE NONCLUSTERED INDEX idx_Country ON Sales.Customers(Country) WHERE Country='USA'  -- filtered
CREATE CLUSTERED COLUMNSTORE INDEX idx_CS ON Sales.Orders  -- OLAP
```

### Index Monitoring

```sql
-- Usage stats (seek/scan/lookup counts)
SELECT tbl.name, idx.name, s.user_seeks, s.user_scans, s.user_updates
FROM sys.indexes idx
JOIN sys.tables tbl ON idx.object_id = tbl.object_id
LEFT JOIN sys.dm_db_index_usage_stats s ON s.object_id = idx.object_id
ORDER BY tbl.name

-- Missing index recommendations
SELECT * FROM sys.dm_db_missing_index_details

-- Fragmentation levels
SELECT tbl.name, idx.name, s.avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(DB_ID(),NULL,NULL,NULL,'LIMITED') s
JOIN sys.tables tbl ON s.object_id = tbl.object_id
JOIN sys.indexes idx ON idx.object_id = s.object_id AND idx.index_id = s.index_id
ORDER BY s.avg_fragmentation_in_percent DESC

-- < 10% = no action | 10-30% = REORGANIZE | > 30% = REBUILD
ALTER INDEX idx_Name ON Sales.Customers REORGANIZE
ALTER INDEX idx_Name ON Sales.Customers REBUILD
```

### Top 10 Query Optimization Tips

```sql
-- Tip 1: SELECT only what you need (no SELECT *)
-- Tip 2: Use INNER JOIN when possible (fastest)
-- Tip 3: Filter before joining large tables (subquery pre-filter)
SELECT c.FirstName, o.OrderID FROM Sales.Customers c
INNER JOIN (SELECT OrderID, CustomerID FROM Sales.Orders WHERE OrderStatus='Delivered') o
ON c.CustomerID = o.CustomerID

-- Tip 4: Use EXISTS instead of IN for large tables
SELECT * FROM Sales.Orders o
WHERE EXISTS (SELECT 1 FROM Sales.Customers c WHERE c.CustomerID=o.CustomerID AND c.Country='USA')

-- Tip 5: UNION ALL + DISTINCT instead of UNION
SELECT DISTINCT CustomerID FROM (
    SELECT CustomerID FROM Sales.Orders
    UNION ALL SELECT CustomerID FROM Sales.OrdersArchive
) t

-- Tip 6: Pre-aggregate before joining
SELECT c.CustomerID, o.OrderCount FROM Sales.Customers c
INNER JOIN (SELECT CustomerID, COUNT(OrderID) OrderCount FROM Sales.Orders GROUP BY CustomerID) o
ON c.CustomerID = o.CustomerID
```

---

## 🔴 SQL Server — Advanced: Stored Procedures & Triggers

```sql
-- Stored Procedure with parameters, IF ELSE, error handling
ALTER PROCEDURE GetCustomerSummary @Country NVARCHAR(50) = 'USA'
AS BEGIN
    BEGIN TRY
        DECLARE @TotalCustomers INT, @AvgScore FLOAT;

        IF EXISTS (SELECT 1 FROM Sales.Customers WHERE Score IS NULL AND Country = @Country)
        BEGIN
            UPDATE Sales.Customers SET Score = 0 WHERE Score IS NULL AND Country = @Country;
        END

        SELECT @TotalCustomers = COUNT(1), @AvgScore = AVG(Score)
        FROM Sales.Customers WHERE Country = @Country;

        PRINT 'Total: ' + CAST(@TotalCustomers AS NVARCHAR);
    END TRY
    BEGIN CATCH
        PRINT 'Error: ' + ERROR_MESSAGE();
        PRINT 'Line: '  + CAST(ERROR_LINE() AS NVARCHAR);
    END CATCH
END
GO
EXEC GetCustomerSummary @Country = 'Germany'

-- DML Trigger: auto-log new employee inserts
CREATE TRIGGER trg_AfterInsert ON Sales.Employees AFTER INSERT AS BEGIN
    INSERT INTO Sales.EmployeeLogs (EmployeeID, LogMessage, LogDate)
    SELECT EmployeeID, 'New Employee: ' + CAST(EmployeeID AS VARCHAR), GETDATE()
    FROM INSERTED
END
```

---

## 🔴 SQL Server — Project: Excel → SQL Server → Power BI
> **Repo:** `top-10-uk-youtubers-2025`

**Pipeline:** Kaggle CSV → SQL Server (clean + view) → Power BI (dashboard)

```sql
-- Data cleaning in SQL Server
SELECT
    CAST(SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) - 1) AS VARCHAR(100)) AS channel_name,
    total_subscribers,
    total_views,
    total_videos
FROM top_uk_youtubers_24

-- Create view for Power BI to connect to
CREATE VIEW view_uk_youtubers_2024 AS
SELECT
    CAST(SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) - 1) AS VARCHAR(100)) AS channel_name,
    total_subscribers,
    total_views,
    total_videos
FROM top_uk_youtubers_24

-- Data quality checks
SELECT COUNT(*) FROM view_uk_youtubers_2024             -- row count
SELECT COUNT(*) FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'view_uk_youtubers_2024'             -- column count
```

**DAX in Power BI:**
```dax
Total Subscribers (M)    = VAR million = 1000000 RETURN SUM(view_uk_youtubers_2024[total_subscribers]) / million
Avg Views per Video (M)  = VAR million = 1000000 RETURN AVERAGE(view_uk_youtubers_2024[avg_views_per_video]) / million
Subscriber Engagement    = DIVIDE([Total Subscribers], [Total Videos])
Views per Subscriber     = DIVIDE([Total Views], [Total Subscribers])
```

[↑ Back to Top](#top)

---

---

# 🐬 MySQL

---

## 🔴 MySQL — Project: Data Cleaning & EDA
> **Repo:** `mysql-datacleaning-eda`

**Dataset:** Global tech layoffs 2020–2023

### Cleaning Pipeline (11 Steps)

```sql
-- Step 1: Create staging table (never touch raw data)
CREATE TABLE layoffs_staging LIKE layoffs;
INSERT INTO layoffs_staging SELECT * FROM layoffs;

-- Step 2: Remove duplicates using CTE + ROW_NUMBER
WITH cte AS (
    SELECT *,
        ROW_NUMBER() OVER(PARTITION BY company, industry, total_laid_off,
                          percentage_laid_off, date) AS row_num
    FROM layoffs_staging
)
DELETE FROM layoffs_staging2 WHERE row_num > 1;

-- Step 3: Standardize — TRIM whitespace, unify categories
UPDATE layoffs_staging SET company = TRIM(company);
UPDATE layoffs_staging SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';

-- Step 4: Fix date column type
UPDATE layoffs_staging
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
ALTER TABLE layoffs_staging MODIFY COLUMN `date` DATE;

-- Step 5: Handle NULLs
UPDATE layoffs_staging t1
JOIN layoffs_staging t2 ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL AND t2.industry IS NOT NULL;

-- Step 6: Remove rows with no useful data
DELETE FROM layoffs_staging WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

-- Step 7: Drop temp columns
ALTER TABLE layoffs_staging DROP COLUMN row_num;
```

### EDA Queries

```sql
-- Top 5 companies by layoffs per year (CTE pattern)
WITH company_year AS (
    SELECT company, YEAR(date) AS yr, SUM(total_laid_off) AS total
    FROM layoffs_staging2
    GROUP BY company, YEAR(date)
),
ranked AS (
    SELECT *, DENSE_RANK() OVER(PARTITION BY yr ORDER BY total DESC) AS rnk
    FROM company_year WHERE yr IS NOT NULL
)
SELECT * FROM ranked WHERE rnk <= 5;

-- Same pattern for industry, country, location
```

### Key Insights (2020–2023)

| Year | Companies Most Affected |
|---|---|
| 2020 | Uber, Booking.com, Airbnb — travel/pandemic |
| 2021 | Bytedance, Katerra, Zillow — startup instability |
| 2022 | Meta, Amazon, Cisco — Big Tech corrections |
| 2023 | Google, Microsoft, Ericsson, Dell — continued contraction |

> US dominated layoffs every year. SF Bay Area was the #1 location affected every year from 2020–2023.

[↑ Back to Top](#top)

---

---

# 🐘 PostgreSQL

---

## 🟡 PostgreSQL — Intermediate: Job Market Analysis
> **Repo:** `postgresql-data-analysis`

5 analytical queries answering real career questions for Data Analysts.

```sql
-- Q1: Top 10 highest-paying remote DA roles
SELECT jf.job_id, jf.job_title, jf.salary_year_avg, cd.name AS company_name
FROM job_postings_fact jf
LEFT JOIN company_dim cd ON jf.company_id = cd.company_id
WHERE job_title_short = 'Data Analyst'
AND job_location = 'Anywhere'
AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC LIMIT 10;

-- Q2: Skills for top-paying roles (CTE pattern)
WITH top_jobs AS (
    SELECT jf.job_id, jf.job_title, jf.salary_year_avg, cd.name
    FROM job_postings_fact jf
    LEFT JOIN company_dim cd ON jf.company_id = cd.company_id
    WHERE job_title_short = 'Data Analyst' AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL ORDER BY salary_year_avg DESC LIMIT 10
)
SELECT tpj.*, sd.skills
FROM top_jobs tpj
INNER JOIN skills_job_dim jd ON tpj.job_id = jd.job_id
INNER JOIN skills_dim sd ON jd.skill_id = sd.skill_id;

-- Q3: Most in-demand skills (top 5)
SELECT sd.skills, COUNT(jd.job_id) AS job_count
FROM job_postings_fact jf
INNER JOIN skills_job_dim jd ON jf.job_id = jd.job_id
INNER JOIN skills_dim sd ON jd.skill_id = sd.skill_id
WHERE jf.job_title_short = 'Data Analyst'
GROUP BY sd.skills ORDER BY job_count DESC LIMIT 5;

-- Q4: Skills by salary
SELECT sd.skills, ROUND(AVG(salary_year_avg), 2) AS avg_salary
FROM job_postings_fact jf
INNER JOIN skills_job_dim jd ON jf.job_id = jd.job_id
INNER JOIN skills_dim sd ON jd.skill_id = sd.skill_id
WHERE jf.job_title_short = 'Data Analyst' AND jf.salary_year_avg IS NOT NULL
GROUP BY sd.skills ORDER BY avg_salary DESC LIMIT 25;

-- Q5: Optimal skills (high demand + high pay, remote, HAVING > 30)
SELECT sd.skill_id, sd.skills,
    COUNT(jd.job_id) AS job_count,
    ROUND(AVG(salary_year_avg), 2) AS avg_salary
FROM job_postings_fact jf
INNER JOIN skills_job_dim jd ON jf.job_id = jd.job_id
INNER JOIN skills_dim sd ON jd.skill_id = sd.skill_id
WHERE jf.job_title_short = 'Data Analyst'
AND jf.salary_year_avg IS NOT NULL AND jf.job_work_from_home = true
GROUP BY sd.skill_id, sd.skills
HAVING COUNT(jd.job_id) > 30
ORDER BY avg_salary DESC, job_count DESC LIMIT 25;
```

**Key PostgreSQL difference from SQL Server:** `job_work_from_home = true` (boolean literal, no quotes).

---

## 🔴 PostgreSQL — Project: SQL Server Migration
> **Repo:** `sql-server-to-postgres-migration`

**Problem:** E-commerce company on SQL Server — expensive licensing, slow queries, needs cloud (AWS + PostgreSQL).  
**Scope:** Migrate 4 tables (Categories, Suppliers, Customers, Products) with 1,055,008 rows, zero data loss.

### Type Mapping (SQL Server → PostgreSQL)

| SQL Server | PostgreSQL |
|---|---|
| `int` | `INTEGER` |
| `datetime` | `TIMESTAMP` |
| `bit` | `BOOLEAN` |
| `nvarchar` | `TEXT` |
| `money` | `NUMERIC(19,4)` |
| `decimal` | `NUMERIC` |

### Migration Pipeline

```python
# 1. Pre-migration checks (SQL Server side)
sql_cursor.execute("SELECT COUNT(*) FROM Customers")  # baseline row counts
sql_cursor.execute("SELECT COUNT(*) FROM Customers WHERE CustomerName IS NULL")  # NULLs
sql_cursor.execute("SELECT COUNT(*) FROM Products WHERE UnitPrice < 0")  # negatives
sql_cursor.execute("SELECT COUNT(*) FROM Customers WHERE CreatedDate > GETDATE()")  # future dates

# 2. Schema extraction
pd.read_sql("SELECT COLUMN_NAME, DATA_TYPE, IS_NULLABLE FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name = 'Customers'", sql_conn)

# 3. Create tables in PostgreSQL (auto-detect primary key)
for idx, row in schema.iterrows():
    if idx == 0 and col.endswith('id') and 'int' in sql_type.lower():
        column_definitions.append(f"{col} SERIAL PRIMARY KEY")

# 4. Migrate with bulk upsert
from psycopg2.extras import execute_values
execute_values(pg_cursor, insert_query, data_tuples, page_size=1000)
pg_conn.commit()

# 5. Post-migration validation
pg_cursor.execute(f"SELECT COUNT(*) FROM {pg_table}")
# Compare: pg_count == sql_count → SUCCESS
```

**Table migration order matters:** Categories → Suppliers → Customers → Products (Products has foreign keys to the first three — must load dependencies first).

> ⚠️ Never use f-strings in production SQL queries — use parameterized queries to prevent SQL injection.

[↑ Back to Top](#top)

---

---

# 📊 Excel

---

## 🟢 Excel — Beginner: Formulas Reference
> **Repo:** `excel-formula-mastery`

### Core Lookup Formulas

```excel
=XLOOKUP(C5,'Data Staff'!$C$5:$C$24,'Data Staff'!$D$5:$D$24)
=VLOOKUP(A3,$A$17:$D$20,2,FALSE)
=HLOOKUP(F3,$G$16:$J$19,2,FALSE)
=INDEX($E$30:$G$32, MATCH(E20,...), MATCH(D20,...))   -- 2D lookup
=XLOOKUP(B9&C7, $C$13:$C$27&$D$13:$D$27, $G$13:$G$27)  -- multi-criteria
```

### Conditional & SWITCH

```excel
=IF(J5>=1250000, J5*0.3%, 0)
=IFS(score>=90,"A", score>=80,"B", score>=70,"C", TRUE,"F")
=SWITCH(RIGHT(C5,1), "S","Single", "M","Married")
=SWITCH(TRUE, I5>1500000,4, I5>1000000,3, I5>500000,2, TRUE,1)  -- tiered brackets
```

### Text Functions

```excel
=LEFT(C5,3)        =RIGHT(F5,3)       =MID(B4,3,6)
=CONCAT(D5,G5)     =UPPER(A1)         =PROPER(A1)      =LOWER(A1)
=TRIM(A1)          =LEN(A1)
```

### Aggregation

```excel
=SUM(E4:E8)    =AVERAGE(E4:E8)    =MAX(E4:E8)    =MIN(E4:E8)
=COUNTIF(B3:B7,"LULUS")
=SUMIF($G$3:$G$12, 1, $H$3:$H$12)
=SUMIFS(Table3[Gross Order], Table3[Vendor],"Vendor I", Table3[Date],">=1/1/2020")
```

### Advanced — Array & Dynamic

```excel
=SUM((F3:F26-G3:G26)*H3:H26)                          -- array: revenue = (sell-cost)*qty
=SUM(IF((H3:H26>=101)*(H3:H26<=250),1,0))             -- count in numeric range
="Q"&ROUNDUP(MONTH(A2)/3,0)&" "&YEAR(A2)              -- fiscal quarter label
=UNIQUE(jobs[job_title_short])                         -- distinct values (spills)
=SORT(A2:B11, 2, -1)                                  -- sort by col 2 descending
=FILTER(J2#, (NOT(ISNUMBER(SEARCH("and",J2#))+ISNUMBER(SEARCH(",",J2#))))*(J2#<>0))
```

### Date Arithmetic

```excel
=DATEDIF(start, end, "D")    -- days difference
=DATEDIF(start, end, "M")    -- months difference
=DATEADD(month, 3, A1)       -- add 3 months (Power Query M)
=TEXT(A1,"mmmm yyyy")        -- "January 2023"
=EOMONTH(A1, 0)              -- last day of same month
```

---

## 🟢 Excel — Beginner: Bike Buyers Dashboard
> **Repo:** `excel-bikebuyers-report`

```
Raw Data → Working Sheet → Pivot Tables → Dashboard
```

```excel
-- Age bracket formula (nested IF)
=IF(L2>54,"Old",IF(L2>31,"Middle Age",IF(L2<31,"Adolescent","Invalid")))
```

**Steps:** Copy data → remove duplicates → spell out abbreviations (M→Male, F→Female) → create age bracket → build PivotTables → create charts → copy to dashboard → add slicers.

---

## 🟡 Excel — Intermediate: Event Attendees Dashboard
> **Repo:** `excel-event-report`

```
Raw → EDA_Table → Clean_Table → Pivot Tables → Dashboard
```

**Key formulas:**

```excel
-- Unique ID for duplicate detection (concatenate all key fields)
=[@Name]&[@Email]&[@Country]&[@Session]&[@[Arrival Time]]&[@Seat]&[@[Food Preference]]&[@Role]

-- Flag duplicates
=IF(COUNTIF([Temp ID],[@[Temp ID]])>1,"Duplicate","Unique")

-- Flag missing values
=IF(OR([@Name]="",[@Email]="",[@Country]="",[@Session]="",[@Seat]=""),"Missing","OK")

-- Standardize time format
=TEXT(E2:E551,"hh:mm AM/PM")

-- Role binary flags (for summing by role)
=IF([@Role]="VIP",1,0)        -- IsVIP
=IF([@Role]="Speaker",1,0)    -- IsSpeaker

-- Dashboard scorecard
=COUNTA(Clean_Table[Name])    -- total attendees
```

**Dashboard questions answered:** Total attendees · Countries · Session attendance · Role breakdown · Missing values

---

## 🟡 Excel — Intermediate: Volunteers Campaign Dashboard
> **Repo:** `excel-campaign-report`

```excel
-- Duplicate detection key
=[@Email]&[@CampaignID]&[@SignupDate]

-- Campaign name lookup
=XLOOKUP([@CampaignID], CampaignTable[CampaignID], CampaignTable[CampaignName])

-- Attendance simplification
=IF([@AttendanceStatus]="No show","Did Not Attend","Attend")

-- Data quality summary
=COUNTA(VolunteerTable4[VolunteerID])
=COUNTIF(VolunteerTable4[DuplicateFlag],"Duplicate")
=COUNTA(UNIQUE(VolunteerTable4[CampaignID]))

-- Dashboard scorecard (GETPIVOTDATA)
=GETPIVOTDATA("VolunteerID",'Pivot Tables'!$A$3)
=GETPIVOTDATA("EmailStatus",'Pivot Tables'!$A$8,"EmailStatus","Missing")
```

---

## 🔴 Excel — Advanced: Salary Dashboard
> **Repo:** `excel-data-analytics`

Full interactive dashboard — dropdowns drive all formulas and charts live.

```excel
-- Name the input cells first (Formulas → Define Name)
-- title, country, type → used in all formulas below

-- Core: conditional median (Excel has no MEDIANIFS)
=MEDIAN(IF(
    (jobs[job_title_short]=title) *
    (jobs[salary_year_avg]<>0) *
    (jobs[job_country]=country) *
    (ISNUMBER(SEARCH(type,jobs[job_schedule_type]))),
    jobs[salary_year_avg]
))
-- Enter with Ctrl+Shift+Enter in legacy Excel; plain Enter in Excel 365

-- Job count KPI
=COUNTIFS(jobs[job_title_short],C2, jobs[job_schedule_type],C3,
          jobs[job_country],C4, jobs[salary_year_avg],"<>")

-- XLOOKUP for KPI cards
=XLOOKUP(title, Title!$D$2:$D$11, Title!$E$2:$E$11, "No Result")

-- Dual-series chart (highlight selected item)
=IF($D2<>title, $E2, NA())   -- grey bars
=IF($D2=title, $E2, NA())    -- accent bar (NA() = bar invisible, not zero)

-- Clean schedule type list (remove combined entries)
=FILTER(J2#,
    (NOT(ISNUMBER(SEARCH("and",J2#)) + ISNUMBER(SEARCH(",",J2#)))) * (J2#<>0))

-- Platform display (remove "via " prefix)
=SUBSTITUTE(Platform!$C$2, "via", "")

-- Sheet protection
-- 1. Ctrl+A → Locked  2. Select 3 dropdowns → Unlocked  3. Review → Protect Sheet
```

---

## 🔴 Excel — Advanced: Project Analysis with Power Query + Power Pivot
> **Repo:** `excel-powerpivot-dashboard` / `excel-data-analytics`

### Power Query Steps

```
data_jobs_salary:
  Replace "via " → "" in job_via
  Change job_posted_date: Decimal → Date/Time
  Add Column: job_posted_month (from date)
  Add Column: salary_hour_adjusted = salary_hour_avg * 2080
  Add Column: job_id (Index from 0)

data_jobs_skills:
  Remove [ ] ' from job_skills
  Split Column by comma → Each occurrence → Into Rows
  Keep: job_skills, job_title_short, job_id
  Unpivot → rename value column: job_skills
  Trim + Capitalize Each Word

Custom Column (M language):
  = if [salary_year_avg] <> null then [salary_year_avg] else [salary_hour_adjusted]
```

### Power Pivot DAX Measures

```dax
Skill Count       := COUNT(data_jobs_skills[job_skills])
Job Count         := DISTINCTCOUNT(data_jobs_salary[job_id])
Skills per Job    := DIVIDE([Skill Count], [Job Count])
Median Salary     := MEDIAN(data_jobs_salary[salary_year_avg])
Skill Likelihood  := DIVIDE([Skill Count], [Job Count])

Median Salary US     := CALCULATE([Median Salary], data_jobs_salary[job_country]="United States")
Median Salary Non-US := CALCULATE([Median Salary], data_jobs_salary[job_country]<>"United States")

Median Salary - Skills :=
    CALCULATE([Median Salary],
        CROSSFILTER(data_jobs_salary[job_id], data_jobs_skills[job_id], Both))
-- CROSSFILTER needed: enables bidirectional filter between salary and skills tables
```

### Key Findings

| # | Finding |
|---|---|
| 1 | More skills = higher salary — Senior Data Engineer avg 8.26 skills |
| 2 | US salary premium real but uneven — ML Engineer: $150k US vs $101k non-US |
| 3 | SQL and Python dominate skill demand across all data roles |
| 4 | Python correlates with highest DA salary ($97k), Excel lowest ($84.5k) |

[↑ Back to Top](#top)

---

---

# 📈 Tableau

---

## 🟡 Tableau — Intermediate: Airbnb Market Analysis
> **Repo:** `tableau-airbnb`

**Business questions:** Where are the expensive areas? When to list? What affects rent? Competition landscape?

```
Step 1: Load Listings + Calendar tables
Step 2: Inner Join on id
Step 3: Filter null Zipcodes
Step 4: Sheet 1 — Price by Zipcode (bar chart, AVG price, sort desc)
Step 5: Sheet 2 — Price per Zipcode (filled map, color by zipcode, label = AVG price)
Step 6: Sheet 3 — Revenue for Year (line chart, WEEK(Date) vs SUM(Price))
Step 7: Sheet 4 — Avg Price per Bedrooms (convert Bedrooms to Dimension, bar chart)
Step 8: Sheet 5 — Distinct Count of Bedroom Listings (table, exclude 0/null)
Step 9: Dashboard — combine all 5 sheets, Automatic size
```

**Key insights:**
- Zipcode 98134 = highest avg rent ($206.60/night)
- January = lowest revenue month; December = peak
- More bedrooms = higher price (directly proportional)
- 1-bedroom: 1,811 hosts (very competitive) → 6-bedroom: only 5 hosts

---

## 🔴 Tableau — Project: Daikibo Industrials (Deloitte Case Study)
> **Repo:** `tableau-excel-deloitte`

**Task 1 — Tableau: Machine Downtime Analysis**

```
Calculated Field:
=IF [Status] = "unhealthy" THEN 10 ELSE 0 END
-- 10 = 10 minutes of downtime per unhealthy message

Charts:
1. Bar: Down Time per Factory (factory on X, SUM(Unhealthy) on Y)
2. Bar: Down Time per Device Type
3. Dashboard: Chart 1 acts as filter action → Chart 2 updates dynamically

Insight: Seiko factory has highest downtime (Laser Welder)
         Shenzhen second (Laser Cutter)
```

**Task 2 — Excel: Gender Pay Equality Classification**

```excel
=IF([@[Equality Score]]=0,"Ideal",
  IF(OR([@[Equality Score]]<-20,[@[Equality Score]]>20),"Highly Discriminative",
    IF(OR([@[Equality Score]]<-10,[@[Equality Score]]>10),"Unfair",
      "Fair")))
```

| Score Range | Classification |
|---|---|
| = 0 | ✅ Ideal |
| -10 to 10 | 🟡 Fair |
| -20 to -10 or 10 to 20 | 🟠 Unfair |
| < -20 or > 20 | 🔴 Highly Discriminative |

[↑ Back to Top](#top)

---

---

# 💡 Power BI

---

## 🟡 Power BI — Intermediate: Survey Dashboard
> **Repo:** `powerbi-survey-report`

**Dataset:** 630 data professional survey respondents.

### Power Query Transformations (9 Steps)

```
Step 1: Get Data → Excel → Power Query Editor
Step 2: Remove useless columns
Step 3: Split Q1 (Job Title) by delimiter ( → delete right split
Step 4: Split Q5 (Programming Language) by delimiter : → delete right split
Step 5: Duplicate Q3 (Salary) → split by digit-to-non-digit
Step 6: Replace k→blank, -→blank, +→225 → change type to Whole Number
Step 7: Add Custom Column: Average Salary
    = ([Q3 Copy.1] + [Q3 Copy.2]) / 2
Step 8: Remove Q3 copy columns
Step 9: Split Q4 + Q11 by ( → remove remainders → Close & Apply
```

### Dashboard Visuals

| Visual | Fields | Insight |
|---|---|---|
| Card | Unique ID (Count) | 630 survey takers |
| Card | Q10 Age (Average) | Avg age = 30 |
| Stacked Bar | Q1 Job Title × Avg Salary | Data Scientists earn most |
| Stacked Column | Q5 Language × Count by Role | Python dominates |
| Tree Map | Q11 Country | Mostly US respondents |
| Gauge | Q6 Work/Life Balance (Avg) | Slightly above average |
| Gauge | Q6 Salary (Avg) | Slightly below average |
| Doughnut | Q7 Difficulty | Mostly "neither easy nor difficult" |

---

## 🔴 Power BI — Project: Top UK YouTubers
> **Repo:** `top-10-uk-youtubers-2025`

**Pipeline:** Kaggle CSV → SQL Server (clean + view) → Power BI (dashboard + Excel analysis)

**DAX Measures:**
```dax
Total Subscribers (M)       = DIVIDE(SUM([total_subscribers]), 1000000)
Total Views (B)             = DIVIDE(SUM([total_views]), 1000000000)
Avg Views per Video (M)     = DIVIDE([Total Views], [Total Videos]) * 1000000
Subscriber Engagement Rate  = DIVIDE([Total Subscribers], [Total Videos])
Views per Subscriber        = DIVIDE([Total Views], [Total Subscribers])
```

**SQL validation before Power BI:**
```sql
-- Net profit calculation per channel
DECLARE @conversionRate FLOAT = 0.02
DECLARE @productCost    MONEY = 5.0
DECLARE @campaignCost   MONEY = 50000.0

SELECT channel_name,
    rounded_avg_views_per_video * @conversionRate                        AS potential_product_sales,
    rounded_avg_views_per_video * @conversionRate * @productCost         AS potential_revenue,
    (rounded_avg_views_per_video * @conversionRate * @productCost) - @campaignCost AS net_profit
FROM view_uk_youtubers_2024
ORDER BY net_profit DESC
```

[↑ Back to Top](#top)

---

---

# 🟢 Beginner

*Quick index of all beginner-level content:*

| Topic | Repo | Key Concepts |
|---|---|---|
| Python fundamentals | `python-learning-journey` | Variables, loops, functions, type conversion |
| Pandas basics | `pandas-learning-journey` | Read, filter, sort, group, merge, clean, plot |
| SQL basics | `sql-learning-journey` (1–16) | SELECT, WHERE, GROUP BY, HAVING, JOINs, CASE WHEN |
| Excel formulas | `excel-formula-mastery` | XLOOKUP, SWITCH, IF, SUMIF, array formulas |
| Excel dashboard | `excel-bikebuyers-report` | PivotTables, slicers, charts, age brackets |

[↑ Back to Top](#top)

---

# 🟡 Intermediate

| Topic | Repo | Key Concepts |
|---|---|---|
| Python web scraping | `python-webscraping-wikipedia` | BeautifulSoup, requests, DataFrame, CSV export |
| Pandas EDA | `pandas-learning-journey` | `.info()`, `.describe()`, heatmaps, `.corr()` |
| SQL window functions | `sql-learning-journey` (17–28) | RANK, ROW_NUMBER, LAG, LEAD, running totals |
| SQL CTEs | `sql-learning-journey` (24) | Standalone, chained, recursive |
| PostgreSQL analysis | `postgresql-data-analysis` | Multi-join analysis, HAVING filters |
| Excel event dashboard | `excel-event-report` | Duplicate detection, COUNTIF, COUNTA, doughnut chart |
| Excel campaign dashboard | `excel-campaign-report` | XLOOKUP, GETPIVOTDATA, attendance flags |
| Tableau Airbnb | `tableau-airbnb` | Join tables, map chart, bar + line charts, dashboard |
| Power BI survey | `powerbi-survey-report` | Power Query ETL, gauges, average salary card |

[↑ Back to Top](#top)

---

# 🔴 Advanced / Projects

| Project | Repos | Stack | What It Does |
|---|---|---|---|
| SQL Data Warehouse | `sql-datawarehouse-advanced-analytics` | SQL Server | Medallion architecture, ETL, star schema, BI analytics |
| UK YouTubers Dashboard | `top-10-uk-youtubers-2025` | SQL Server + Power BI | Kaggle → SQL clean → Power BI dashboard + ROI analysis |
| MySQL Layoffs EDA | `mysql-datacleaning-eda` | MySQL | Full cleaning pipeline + CTE-based EDA |
| EPL API Pipeline | `epl-standings-2024-25` | Python + MySQL | REST API → Pandas → MySQL upsert |
| DB Migration | `sql-server-to-postgres-migration` | Python + SQL Server + PostgreSQL | 1M+ row migration with validation + visualisation |
| Excel Analytics | `excel-data-analytics` / `excel-powerpivot-dashboard` | Excel + Power Query + Power Pivot + DAX | Multi-sheet analytics, Power Pivot data model |
| Deloitte Case Study | `tableau-excel-deloitte` | Tableau + Excel | Machine downtime analysis + pay equality classification |
| Amazon Price Tracker | `python-webscraping-amazon` | Python | Scrape + log + email alert on a schedule |

[↑ Back to Top](#top)

---

## 🔑 Key Concepts by Difficulty

| Concept | Level | Tool |
|---|---|---|
| Variables, loops, functions | 🟢 Beginner | Python |
| filter/sort/groupby | 🟢 Beginner | Pandas |
| SELECT, WHERE, GROUP BY, HAVING | 🟢 Beginner | SQL |
| XLOOKUP, SWITCH, IF array | 🟢 Beginner | Excel |
| Web scraping with BeautifulSoup | 🟡 Intermediate | Python |
| Window functions (RANK, LAG, LEAD) | 🟡 Intermediate | SQL Server |
| CTEs (standalone + chained) | 🟡 Intermediate | SQL Server / PostgreSQL |
| Power Query ETL | 🟡 Intermediate | Excel / Power BI |
| Dashboard design with slicers | 🟡 Intermediate | Tableau / Power BI |
| Recursive CTEs | 🔴 Advanced | SQL Server |
| Index strategy (clustered/columnstore) | 🔴 Advanced | SQL Server |
| Stored procedures + error handling | 🔴 Advanced | SQL Server |
| REST API → DataFrame → DB load | 🔴 Advanced | Python + MySQL |
| Cross-platform DB migration | 🔴 Advanced | Python + SQL Server + PostgreSQL |
| Power Pivot data model + DAX | 🔴 Advanced | Excel |
| Medallion architecture (Bronze/Silver/Gold) | 🔴 Advanced | SQL Server |
| CROSSFILTER in DAX | 🔴 Advanced | Power BI |

[↑ Back to Top](#top)

---

---

# 📊 Excel — Continued: Intermediate, Advanced & Expert Formulas
> **Repo:** `excel-formula-mastery`

---

## 🟡 Excel Intermediate — Sections 1–4

### Section 1 — Array Formulas, Date Logic & SUMIFS

```excel
-- Conditional sum
=SUMIF(range, "<200", sum_range)

-- Array: revenue = (sell price − cost) × quantity
=SUM((F3:F26 - G3:G26) * H3:H26)

-- Array: count rows where sell price equals cost
=SUM(IF(F3:F26 = G3:G26, 1, 0))

-- Array: count values within a numeric range
=SUM(IF((H3:H26>=101)*(H3:H26<=250), 1, 0))

-- Derive fiscal quarter label from date
="Q"&ROUNDUP(MONTH(A2)/3, 0)&" "&YEAR(A2)

-- Multi-criteria date-range SUMIFS
=SUMIFS(Table3[Gross Order],
    Table3[Vendor],  "Vendor I",
    Table3[Item],    "Item A",
    Table3[Date],    ">=1/1/2020",
    Table3[Date],    "<=1/31/2020")

-- Week-on-week growth rate
=(D7 - C7) / C7
```

---

### Section 2 — Data Cleaning, IFS & Text Formulas

```excel
-- URL string replacement
=SUBSTITUTE([@[OLD IMAGE_URL]], "thumb", "detail")

-- Three-tier shipping cost logic
=IFS([@[Sale Price]]>49, 0, AND(...), value, TRUE, 10.59)

-- Multi-condition qualification check
=IFS(D11=0,"Qualified", B11<15,"Qualified", C11<3,"Qualified", TRUE,"Not Qualified")

-- Full name formatting with initial
=PROPER(CONCAT(A7," ",B7," ",LEFT(C7,1),"."))

-- Quarter label from date
="Q"&ROUNDUP(MONTH(R2)/3, 0)&" "&YEAR(R2)

-- Team lead to operations manager mapping
=IFS([@TL]="TL 1","OM 1", [@TL]="TL 2","OM 1", ..., TRUE,"invalid")
```

---

### Section 3 — Dynamic Arrays: UNIQUE, FILTER, IFERROR + SMALL

```excel
-- Legacy dynamic filter (pre-365): extract matching rows one by one
=IFERROR(INDEX(..., SMALL(IF(Data!B$2:B$82=$B$9, ROW(...)-ROW(...)+1), ROW(1:1))), "")

-- Excel 365: distinct list (spills down automatically)
=UNIQUE(Data!B2:B82)

-- Excel 365: spill filtered results matching a criterion
=FILTER(Data!D2:D82, Data!B2:B82=$B$9)
```

> 💡 The legacy `IFERROR+INDEX+SMALL+IF` pattern achieves the same as `FILTER()` in older Excel versions — it's worth knowing both.

---

### Section 4 — Table-Aware Formulas & MoM Analysis

```excel
-- Movie attribute lookup with structured reference
=XLOOKUP([@MOVIE], Data!$A$2:$A$17, Data!$B$2:$B$17)

-- Row average across a 7-month span
=AVERAGE(Table2[@[Jul-21]:[Jan-22]])

-- Row min/max within the same span
=MIN(Table2[@[Jul-21]:[Jan-22]])
=MAX(Table2[@[Jul-21]:[Jan-22]])

-- Month-on-month growth rate
=[@[Jan-22]] / [@[Dec-21]] - 1

-- Benchmark comparison within column
=IF([@Average] > AVERAGE([Average]), "Above Average", "Below Average")
```

---

## 🔴 Excel Advanced — Sections 1–4

### Section 1 — Conditional Formatting, Goal Seek & Multi-Criteria Lookup

```excel
-- Accept if EITHER condition is met
=IF(OR($H$6>=500000, $H$7>=25000), "Accept", "Reject")

-- Accept only if BOTH conditions are met
=IF(AND($H$6>=500000, $H$7>=25000), "Accept", "Reject")

-- Break-even formulas
-- Margin = Price − Cost
-- Units needed = (Fixed Cost + Target Profit) / Margin

-- Two-criteria INDEX/MATCH (array)
=INDEX($F$10:$F$21,
    MATCH(1, ($B$10:$B$21=$B$6) * ($C$10:$C$21=C5), 0))

-- Two-criteria XLOOKUP with boolean array
=XLOOKUP(1,
    ($B$10:$B$21=$B$7) * ($C$10:$C$21=C5),
    $F$10:$F$21, "Not Found", 0)
```

---

### Section 2 — Date Intelligence & Tiered Airline Commission

```excel
-- Extract weekday abbreviation from date
=TEXT(B7, "ddd")

-- Weekend flag
=OR(J6="Sat", J6="Sun")

-- Date range membership check
=AND(B6>=DATE(2015,10,1), B6<=DATE(2015,10,15))

-- Per-airline multi-tier commission calculation
=IFS(E6="Airlines 1", IF(K6, F6*0.04, 0),
     ...,
     E6="Airlines 5", IF(F6<50000000, F6*0.01, ...))

-- Total airline 2 revenue per date
=SUMIFS($F$6:$F$647, $E$6:$E$647,"Airlines 2", $B$6:$B$647, B6)

-- Payment method fee schedule
=SWITCH(I6, "Method 1",0, "Method 2",H6*0.03, "Method 3",4000, "Method 4",3000, 0)
```

---

### Section 3 — Financial Modeling, LARGE/SMALL & Proration

```excel
-- Compound growth projection up to year limit
=IF(E14<=$C$11, D15*(1+$C$6), "")

-- Tax calculation within projection range
=IF(D$14<=$C$11, D$15*$C$7, "")

-- Rank-based value extraction
=LARGE($F$6:$F$17, $H7)      -- Nth largest
=SMALL($F$6:$F$17, H15)      -- Nth smallest

-- Reverse lookup: value → name
=INDEX($B$6:$B$17, MATCH(I7, $F$6:$F$17, 0))
=XLOOKUP(I15, $F$6:$F$17, $B$6:$B$17, , 0)

-- Proration — Full Month: recognise revenue proportionally from start date
=IF($D6<F$5, 0,
  IF($D6>EOMONTH(F$5,0), $C6,
    $C6 * (DAY($D6) / DAY(EOMONTH(F$5,0)))))

-- Proration — Partial Month: recognise only days within the month
=IF($D6<F$5, 0,
  IF($D6<=EOMONTH(F$5,0),
    $C6 * (DAY($D6) - DAY(F$5) + 1) / DAY(EOMONTH(F$5,0)),
    $C6))
```

---

### Section 4 — Large Dataset Lookup, Tax Classification & SUMIFS

```excel
-- Last-match XLOOKUP (handles duplicate keys — returns last match)
=XLOOKUP(Test!B2, DTBS[CUSTOMER CODE], DTBS[CUSTOMER NAME], , , -1)

-- Classic last-match LOOKUP trick
=LOOKUP(2, 1/(DTBS[CUSTOMER CODE]=Test!B2), Database!$C$2:$C$54448)

-- All unique tax types for a customer
=TEXTJOIN(", ", TRUE, UNIQUE(FILTER(DTBS[Tax], DTBS[CUSTOMER CODE]=B2)))

-- Detect mixed PKP/PTKP tax status
=IF(AND(COUNTIFS(...,"PKP")>0, COUNTIFS(...,"PTKP")>0), "Both", TEXTJOIN(...))

-- Proportional VAT for mixed-status customers
=IF(D2="PKP", (G2+H2)*0.1,
  IF(D2="PTKP", 0,
    IF(D2="Both", (G2+H2) * (COUNTIFS(...,"PKP")/COUNTIF(...))*0.1, 0)))

-- Total box quantity by customer name
=SUMIFS(DTBS[BOX QTY], DTBS[CUSTOMER NAME], Test!C2)
```

---

## 🔵 Excel Expert — HR Recruitment Revenue Modeling

> The most complex Excel work in the collection — multi-table payroll, channel-weighted revenue, wildcard MATCH inside SUMPRODUCT, and proportional VAT.

```excel
-- Format phone number with + prefix
="+"&XLOOKUP($O4, $E$2:$E$3002, $H$2:$H$3002, "Not Found")

-- Generate anonymized email from name
=LOWER(LEFT(XLOOKUP(...), 4))&"***@"&IF(RAND()>0.5,"jossmail.com","yuhuumail.com")

-- Age via DATEDIF + XLOOKUP
=DATEDIF(XLOOKUP($O4, $E$2:$E$3002, $F$2:$F$3002), $R4, "Y")

-- Composite bonus rate: tenure + performance score
=IF(DATEDIF($R4,$S4,"Y")>=3, 0.025, IF(...)>=1, 0.015, 0)) + IF($V4>=50, 0.04, IF(...))

-- Count candidates hired before a date
=COUNTIF($R:$R, "<"&DATE(2021,3,15))

-- Revenue per placement × closing rate
=Z11 * VLOOKUP("To be a Sales & Marketing Associate", $J$6:$L$9, 3, FALSE)

-- Total expected revenue for a role
=SUMPRODUCT(($P$3:$P$1500="To be a Sales & Marketing Associate") *
            ($W$3:$W$1500) *
            VLOOKUP(...))

-- Channel-weighted net revenue after churn
=SUMPRODUCT(($P="Sales Assoc") *
            ((channel="LinkedIn")*$L$3 + (channel="Jobseeker")*$L$4) *
            VLOOKUP(...) *
            (1-$W))

-- Wildcard MATCH inside SUMPRODUCT for tiered SMS charges
=SUMPRODUCT(($P="Sales Assoc") *
            IFERROR(INDEX($L$11:$L$16, MATCH(LEFT($T,5)&"*", $J$11:$J$16, 0)), 0))

-- Monthly source-filtered applicant count
=COUNTIFS($Q,"LinkedIn", $P,"Sales Assoc", $S,">=5/1/2021", $S,"<6/1/2021")

-- Monthly channel revenue with date filter
=SUMPRODUCT(($Q="LinkedIn") * ($P="Sales Assoc") *
            ($S>=DATE(2021,5,1)) * ($S<DATE(2021,6,1)) *
            VLOOKUP(...) * (1-$W))

-- Distinct sorted performance score list
=UNIQUE(SORT(FILTER($V$4:$V$1500, $V$4:$V$1500<>"")))

-- Count high-scoring male candidates
=SUMPRODUCT(($V>=50) * (XLOOKUP($O, $E$3:$E$3002, $G$3:$G$3002,"")="Male"))
```

### Excel Formula Concept Map

| Concept | Functions Used |
|---|---|
| **Text Manipulation** | `LEFT`, `RIGHT`, `MID`, `CONCAT`, `SUBSTITUTE`, `TRIM`, `UPPER`, `PROPER`, `LOWER`, `TEXTJOIN` |
| **Lookup & Reference** | `XLOOKUP`, `VLOOKUP`, `HLOOKUP`, `INDEX`, `MATCH`, `LOOKUP` (last-match) |
| **Conditional Logic** | `IF`, `IFS`, `SWITCH`, `AND`, `OR`, `SWITCH(TRUE,...)` tiered brackets |
| **Aggregation** | `SUM`, `SUMIF`, `SUMIFS`, `SUMPRODUCT`, `AVERAGE`, `COUNT`, `COUNTIF`, `COUNTIFS` |
| **Date & Time** | `DATE`, `DATEDIF`, `DAYS`, `EOMONTH`, `MONTH`, `YEAR`, `ROUNDUP`, `TEXT` |
| **Dynamic Arrays** | `FILTER`, `UNIQUE`, `SORT`, `SMALL`, `LARGE` |
| **Error Handling** | `IFERROR` |
| **Financial Modeling** | Proration (full + partial month), Break-even, MoM growth, Bonus tiers, Proportional VAT |
| **Legacy Arrays** | `IFERROR+INDEX+SMALL+IF` (pre-365 dynamic filter) |

[↑ Back to Top](#top)

---

---

# 🗄️ SQL Server — Project: Data Warehouse
> **Repo:** `sql-datawarehouse-advanced-analytics`

---

## Architecture: Medallion (Bronze → Silver → Gold)

```
Bronze (Raw Ingest)  →  Silver (Cleaned)  →  Gold (Analytics-Ready)
     ↑                        ↑                        ↑
BULK INSERT from CSV     Stored Procedure          Star Schema Views
```

**Source systems:** CRM (3 tables) + ERP (3 tables) — 6 CSV files total.

---

## Bronze Layer — Raw Ingestion

```sql
-- Stored procedure: truncate + BULK INSERT for all 6 tables
CREATE OR ALTER PROCEDURE bronze.load_bronze AS
BEGIN
    DECLARE @start_time DATETIME, @end_time DATETIME,
            @batch_start DATETIME, @batch_end DATETIME;
    BEGIN TRY
        SET @batch_start = GETDATE();

        -- CRM: Customer Info
        TRUNCATE TABLE bronze.crm_cust_info;
        BULK INSERT bronze.crm_cust_info
        FROM 'D:\datasets\source_crm\cust_info.csv'
        WITH (FIRSTROW=2, FIELDTERMINATOR=',', TABLOCK);

        -- CRM: Product Info
        TRUNCATE TABLE bronze.crm_prd_info;
        BULK INSERT bronze.crm_prd_info
        FROM 'D:\datasets\source_crm\prd_info.csv'
        WITH (FIRSTROW=2, FIELDTERMINATOR=',', TABLOCK);

        -- ERP: Customer (with NAS prefix issue)
        TRUNCATE TABLE bronze.erp_cust_az12;
        BULK INSERT bronze.erp_cust_az12
        FROM 'D:\datasets\source_erp\CUST_AZ12.csv'
        WITH (FIRSTROW=2, FIELDTERMINATOR=',', TABLOCK);

        SET @batch_end = GETDATE();
        PRINT 'Total Duration: ' + CAST(DATEDIFF(second,@batch_start,@batch_end) AS NVARCHAR) + 's';
    END TRY
    BEGIN CATCH
        PRINT 'Error: ' + ERROR_MESSAGE();
        PRINT 'Number: ' + CAST(ERROR_NUMBER() AS NVARCHAR(10));
        PRINT 'State: '  + CAST(ERROR_STATE()  AS NVARCHAR(10));
    END CATCH
END
```

---

## Silver Layer — Data Cleansing

Key transformations per table:

```sql
-- crm_cust_info: deduplicate + trim + normalize codes to readable labels
INSERT INTO silver.crm_cust_info (cst_id, cst_key, cst_firstname,
    cst_lastname, cst_marital_status, cst_gndr, cst_create_date)
SELECT
    cst_id,
    cst_key,
    TRIM(cst_firstname),
    TRIM(cst_lastname),
    CASE UPPER(TRIM(cst_marital_status))
        WHEN 'S' THEN 'Single' WHEN 'M' THEN 'Married' ELSE 'n/a' END,
    CASE UPPER(TRIM(cst_gndr))
        WHEN 'F' THEN 'Female' WHEN 'M' THEN 'Male' ELSE 'n/a' END,
    cst_create_date
FROM (
    SELECT *,
        ROW_NUMBER() OVER(PARTITION BY cst_id ORDER BY cst_create_date DESC) flag_last
    FROM bronze.crm_cust_info WHERE cst_id IS NOT NULL
) t WHERE flag_last = 1;   -- keep only most recent record per customer

-- crm_prd_info: derive cat_id + prd_end_dt using LEAD()
INSERT INTO silver.crm_prd_info
SELECT
    prd_id,
    REPLACE(SUBSTRING(prd_key,1,5),'-','_') cat_id,       -- extract category from key
    SUBSTRING(prd_key,7,LEN(prd_key))       prd_key,       -- extract product key
    prd_nm,
    ISNULL(prd_cost, 0)                     prd_cost,
    CASE UPPER(TRIM(prd_line))
        WHEN 'M' THEN 'Mountain' WHEN 'R' THEN 'Road'
        WHEN 'S' THEN 'Other Sales' WHEN 'T' THEN 'Touring' ELSE 'n/a' END,
    CAST(prd_start_dt AS DATE),
    CAST(LEAD(prd_start_dt) OVER(PARTITION BY prd_key ORDER BY prd_start_dt) - 1 AS DATE)
FROM bronze.crm_prd_info;
-- LEAD()-1 derives the end date as "one day before next version starts"

-- crm_sales_details: convert INT date (YYYYMMDD) → DATE + fix broken sales amounts
INSERT INTO silver.crm_sales_details
SELECT
    sls_ord_num, sls_prd_key, sls_cust_id,
    CASE WHEN sls_order_dt<=0 OR LEN(sls_order_dt)!=8 THEN NULL
         ELSE CAST(CAST(sls_order_dt AS VARCHAR) AS DATE) END sls_order_dt,
    -- same pattern for ship_dt and due_dt
    CASE WHEN sls_sales IS NULL OR sls_sales<=0 OR sls_sales!=sls_quantity*ABS(sls_price)
         THEN sls_quantity * ABS(sls_price) ELSE sls_sales END sls_sales,
    sls_quantity,
    CASE WHEN sls_price IS NULL OR sls_price<=0
         THEN ABS(sls_sales)/NULLIF(sls_quantity,0) ELSE sls_price END sls_price
FROM bronze.crm_sales_details;

-- erp_cust_az12: strip NAS prefix + null out invalid birthdates + normalize gender
INSERT INTO silver.erp_cust_az12 (cid, bdate, gen)
SELECT
    CASE WHEN cid LIKE 'NAS%' THEN SUBSTRING(cid,4,LEN(cid)) ELSE cid END,
    CASE WHEN bdate<'1926-01-01' OR bdate>GETDATE() THEN NULL ELSE bdate END,
    CASE WHEN UPPER(TRIM(gen)) IN ('F','FEMALE') THEN 'Female'
         WHEN UPPER(TRIM(gen)) IN ('M','MALE')   THEN 'Male' ELSE 'n/a' END
FROM bronze.erp_cust_az12;

-- erp_loc_a101: standardize country names + remove dashes from cid
INSERT INTO silver.erp_loc_a101 (cid, cntry)
SELECT
    REPLACE(cid,'-',''),
    CASE WHEN TRIM(cntry)='DE'               THEN 'Germany'
         WHEN TRIM(cntry) IN ('US','USA')    THEN 'United States'
         WHEN TRIM(cntry) IS NULL OR cntry='' THEN 'n/a'
         ELSE TRIM(cntry) END
FROM bronze.erp_loc_a101;
```

---

## Gold Layer — Star Schema Views

```sql
-- Dimension: Customers (integrates CRM + ERP birthdate + ERP country)
CREATE VIEW gold.dim_customers AS
SELECT
    ROW_NUMBER() OVER(ORDER BY ci.cst_id) AS customer_key,  -- surrogate key
    ci.cst_id       AS customer_id,
    ci.cst_key      AS customer_number,
    ci.cst_firstname AS first_name,
    ci.cst_lastname  AS last_name,
    la.cntry         AS country,
    ci.cst_marital_status AS marital_status,
    CASE WHEN ci.cst_gndr != 'n/a' THEN ci.cst_gndr
         ELSE COALESCE(ca.gen, 'n/a') END AS gender,        -- CRM is master; fallback to ERP
    ca.bdate         AS birthdate,
    ci.cst_create_date AS create_date
FROM silver.crm_cust_info ci
LEFT JOIN silver.erp_cust_az12 ca ON ci.cst_key = ca.cid
LEFT JOIN silver.erp_loc_a101  la ON ci.cst_key = la.cid;

-- Dimension: Products (only current versions — prd_end_dt IS NULL)
CREATE VIEW gold.dim_products AS
SELECT
    ROW_NUMBER() OVER(ORDER BY pn.prd_start_dt, pn.prd_key) AS product_key,
    pn.prd_id    AS product_id,
    pn.prd_key   AS product_number,
    pn.prd_nm    AS product_name,
    pn.cat_id    AS category_id,
    pc.cat       AS category,
    pc.subcat    AS subcategory,
    pc.maintenance,
    pn.prd_cost  AS product_cost,
    pn.prd_line  AS product_line,
    pn.prd_start_dt AS start_date
FROM silver.crm_prd_info pn
LEFT JOIN silver.erp_px_cat_g1v2 pc ON pn.cat_id = pc.id
WHERE pn.prd_end_dt IS NULL;  -- current products only (no historical versions)

-- Fact: Sales (uses surrogate keys — not raw IDs — for clean star schema joins)
CREATE VIEW gold.fact_sales AS
SELECT
    sd.sls_ord_num  AS order_number,
    pr.product_key,                -- FK → dim_products
    cu.customer_key,               -- FK → dim_customers
    sd.sls_order_dt AS order_date,
    sd.sls_ship_dt  AS shipping_date,
    sd.sls_due_dt   AS due_date,
    sd.sls_sales    AS sales_amount,
    sd.sls_quantity AS quantity,
    sd.sls_price    AS price
FROM silver.crm_sales_details sd
LEFT JOIN gold.dim_products  pr ON sd.sls_prd_key = pr.product_number
LEFT JOIN gold.dim_customers cu ON sd.sls_cust_id = cu.customer_id;
```

---

## BI Analytics Queries (Gold Layer)

```sql
-- Change over time: yearly sales trend
SELECT
    DATETRUNC(year, order_date) order_year,
    SUM(sales_amount)            total_sales,
    COUNT(DISTINCT customer_key) total_customers,
    SUM(quantity)                total_quantity
FROM gold.fact_sales
WHERE order_date IS NOT NULL
GROUP BY DATETRUNC(year, order_date)
ORDER BY 1;

-- Cumulative: running total + moving average price
SELECT order_year, total_sales,
    SUM(total_sales) OVER(ORDER BY order_year) running_total,
    AVG(avg_price)   OVER(ORDER BY order_year) moving_avg_price
FROM (
    SELECT DATETRUNC(year,order_date) order_year,
           SUM(sales_amount) total_sales, AVG(price) avg_price
    FROM gold.fact_sales WHERE order_date IS NOT NULL
    GROUP BY DATETRUNC(year,order_date)
) t;

-- YoY performance: each product vs its own historical average + prior year
WITH yearly_sales AS (
    SELECT YEAR(s.order_date) yr, p.product_name, SUM(s.sales_amount) total_sales
    FROM gold.fact_sales s LEFT JOIN gold.dim_products p ON s.product_key=p.product_key
    WHERE order_date IS NOT NULL GROUP BY YEAR(s.order_date), p.product_name
)
SELECT yr, product_name, total_sales,
    AVG(total_sales)  OVER(PARTITION BY product_name) avg_sales,
    total_sales - AVG(total_sales) OVER(PARTITION BY product_name) diff_avg,
    CASE WHEN total_sales > AVG(total_sales) OVER(PARTITION BY product_name) THEN 'Above Average'
         WHEN total_sales < AVG(total_sales) OVER(PARTITION BY product_name) THEN 'Below Average'
         ELSE 'Avg' END avg_change,
    LAG(total_sales) OVER(PARTITION BY product_name ORDER BY yr) py_sales,
    CASE WHEN total_sales > LAG(total_sales) OVER(PARTITION BY product_name ORDER BY yr) THEN 'Increase'
         WHEN total_sales < LAG(total_sales) OVER(PARTITION BY product_name ORDER BY yr) THEN 'Decrease'
         ELSE 'No Change' END py_change
FROM yearly_sales ORDER BY product_name, yr;

-- Part-to-whole: category % of total sales
WITH cat_sales AS (
    SELECT p.category, SUM(s.sales_amount) total_sales
    FROM gold.fact_sales s LEFT JOIN gold.dim_products p ON s.product_key=p.product_key
    GROUP BY p.category
)
SELECT category, total_sales,
    SUM(total_sales) OVER() overall_sales,
    CONCAT(ROUND(CAST(total_sales AS FLOAT)/SUM(total_sales) OVER()*100, 2),'%') pct
FROM cat_sales ORDER BY total_sales DESC;

-- Customer segmentation: VIP / Regular / New
WITH customer_sales AS (
    SELECT c.customer_key, SUM(s.sales_amount) total_sales,
        DATEDIFF(month, MIN(s.order_date), MAX(s.order_date)) lifespan
    FROM gold.fact_sales s LEFT JOIN gold.dim_customers c ON s.customer_key=c.customer_key
    GROUP BY c.customer_key
)
SELECT customer_status, COUNT(customer_key) total_customers
FROM (
    SELECT customer_key, total_sales, lifespan,
        CASE WHEN lifespan>=12 AND total_sales>5000  THEN 'VIP'
             WHEN lifespan>=12 AND total_sales<=5000 THEN 'Regular'
             ELSE 'New' END customer_status
    FROM customer_sales
) t GROUP BY customer_status ORDER BY COUNT(customer_key) DESC;
```

[↑ Back to Top](#top)

---

---

# 📊 Excel — Continued: Salary Dashboard Build
> **Repo:** `excel-data-analytics` — Section 4: Advanced Features

---

## Section 4 — Advanced Excel Features

### Forecast Sheet Fix

> If you see an error when creating a Forecast Sheet — **delete the existing blue line chart first**. The forecast orange line can then render correctly.

### Workbook Calculation Mode

> Some Excel features (especially Power Pivot and data model operations) reset calculation to Manual.
> Always check after heavy operations: `Formulas → Calculation Options → Automatic`

### Data Tables (What-If Analysis)

```
Data → What-If Analysis → Data Table
  Row input cell:    horizontal variable (e.g. price)
  Column input cell: vertical variable (e.g. units sold)
```

### Data Analysis Toolpak

Enable: `File → Options → Add-ins → Analysis ToolPak → Go → ✅`

| Tool | Use Case |
|---|---|
| **Descriptive Statistics** | Summary stats (mean, median, std dev, min, max) in one output block |
| **Histogram** | Frequency distribution with custom bins |
| **Moving Average** | Smooth a time series (also available from PivotTables) |

[↑ Back to Top](#top)

---

---

# ✅ Complete Project Index

| # | Repo | Tool(s) | Level | Type |
|---|---|---|---|---|
| 1 | `python-learning-journey` | Python | 🟢 Beginner | Learning |
| 2 | `pandas-learning-journey` | Pandas | 🟢 Beginner | Learning |
| 3 | `excel-formula-mastery` | Excel | 🟢→🔵 All Levels | Learning |
| 4 | `excel-bikebuyers-report` | Excel | 🟢 Beginner | Project |
| 5 | `sql-learning-journey` | SQL Server | 🟢→🔴 All Levels | Learning |
| 6 | `excel-event-report` | Excel | 🟡 Intermediate | Project |
| 7 | `excel-campaign-report` | Excel | 🟡 Intermediate | Project |
| 8 | `python-webscraping-wikipedia` | Python | 🟡 Intermediate | Project |
| 9 | `python-webscraping-amazon` | Python | 🟡 Intermediate | Project |
| 10 | `postgresql-data-analysis` | PostgreSQL | 🟡 Intermediate | Learning + Analysis |
| 11 | `tableau-airbnb` | Tableau | 🟡 Intermediate | Project |
| 12 | `powerbi-survey-report` | Power BI | 🟡 Intermediate | Project |
| 13 | `mysql-datacleaning-eda` | MySQL | 🔴 Advanced | Project |
| 14 | `tableau-excel-deloitte` | Tableau + Excel | 🔴 Advanced | Project |
| 15 | `excel-data-analytics` | Excel + Power Query + Power Pivot | 🔴 Advanced | Project |
| 16 | `excel-powerpivot-dashboard` | Excel + DAX | 🔴 Advanced | Project |
| 17 | `top-10-uk-youtubers-2025` | SQL Server + Power BI | 🔴 Advanced | Project |
| 18 | `epl-standings-2024-25` | Python + MySQL | 🔴 Advanced | Project |
| 19 | `sql-server-to-postgres-migration` | Python + SQL Server + PostgreSQL | 🔴 Advanced | Project |
| 20 | `sql-datawarehouse-advanced-analytics` | SQL Server | 🔴 Advanced | Project |

---

## 🗺️ Suggested Learning Path

```
Start here if you're new to data analytics:

MONTH 1 — Foundations
  → python-learning-journey      (Python basics)
  → sql-learning-journey (1–16)  (SQL basics)
  → excel-formula-mastery Basic  (Excel formulas)

MONTH 2 — Data Manipulation
  → pandas-learning-journey      (Pandas: read, clean, EDA, visualize)
  → sql-learning-journey (17–28) (Window functions, CTEs)
  → excel-bikebuyers-report      (First dashboard)

MONTH 3 — Analysis & Dashboards
  → excel-event-report           (Data cleaning + quality checks)
  → excel-campaign-report        (Volunteer analysis + XLOOKUP)
  → python-webscraping-wikipedia (Web scraping pipeline)
  → postgresql-data-analysis     (5 career questions in SQL)
  → tableau-airbnb               (Tableau fundamentals)
  → powerbi-survey-report        (Power BI + Power Query)

MONTH 4 — Advanced Projects
  → mysql-datacleaning-eda       (Full MySQL clean + EDA)
  → excel-data-analytics         (Power Query + Power Pivot + DAX)
  → tableau-excel-deloitte       (Client-style project)
  → top-10-uk-youtubers-2025     (End-to-end: SQL → Power BI)

MONTH 5 — Engineering & Architecture
  → python-webscraping-amazon    (Automated tracker + email alerts)
  → epl-standings-2024-25        (REST API → MySQL pipeline)
  → sql-server-to-postgres-migration (DB migration + validation)
  → sql-datawarehouse-advanced-analytics (Medallion architecture)
  → excel-formula-mastery Advanced/Expert (Financial modeling)
```

[↑ Back to Top](#top)
