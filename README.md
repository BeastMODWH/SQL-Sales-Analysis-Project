# SQL-Sales-Analysis-Project

## Project Overview
This project demonstrates the use of SQL to query and analyze a dataset of 9,800 retail sales records (Superstore Dataset). The goal was to extract actionable business insights regarding revenue generation and regional performance.

## Tools Used
*   **SQLite Online** (Database environment)
*   **SQL** (Data querying and aggregation)

## Data Preparation
*   Imported a raw CSV file into a SQL database.
*   Renamed raw, unformatted columns (e.g., `c1`, `c2`) to meaningful business headers (e.g., `Order_ID`, `Region`, `Sales`).
*   Deleted the redundant header row from the dataset to ensure accurate mathematical calculations.

## Business Questions & Insights

### 1. What is the Total Revenue?
**Query:**
```sql
SELECT SUM(Sales) AS Total_Revenue 
FROM train;
Result: The company generated $2,261,536.78 in total sales across the dataset.

2. Which Region Generates the Most Revenue?

Query:

sql
SELECT Region, SUM(Sales) AS Total_Revenue
FROM train
GROUP BY Region
ORDER BY Total_Revenue DESC;
Result: The West region is the top performer with $710,219.68, followed by the East region. The South region is the lowest performer.

3. Which Regions have a High Average Order Value?

Query:

sql
SELECT Region, AVG(Sales) AS Average_Order_Value
FROM train
GROUP BY Region
HAVING AVG(Sales) > 200
ORDER BY Average_Order_Value DESC;
Result: Used the HAVING clause to filter aggregated data. It revealed that the South region, despite having the lowest total revenue, actually has the highest average order value ($243.52). This indicates they sell fewer items, but higher-value items.

Key Learnings

Mastered the difference between WHERE (filtering rows) and HAVING (filtering aggregated groups).
Learned how to clean raw text data using ALTER TABLE and DELETE commands.
Gained experience using GROUP BY and ORDER BY to summarize thousands of rows into clear, actionable business metrics.

### 4. Time Series Analysis & Handling Dirty Data
**Query:**
sql
SELECT 
    CASE 
        WHEN strftime('%m', SUBSTR(Order_Date, 7, 4) || '-' || SUBSTR(Order_Date, 1, 2) || '-' || SUBSTR(Order_Date, 4, 2)) IS NULL 
        THEN 'Invalid Date Format'
        ELSE strftime('%m', SUBSTR(Order_Date, 7, 4) || '-' || SUBSTR(Order_Date, 1, 2) || '-' || SUBSTR(Order_Date, 4, 2))
    END AS Month_Number,
    ROUND(SUM(CAST(Sales AS REAL)), 2) AS Total_Revenue
FROM train
GROUP BY Month_Number
ORDER BY Total_Revenue DESC;

Result: While grouping revenue by month, I encountered a NULL value that represented over $1.3 million in sales. I investigated the raw data and discovered inconsistent date formats (some were DD/MM/YYYY, causing strftime to fail on impossible months like '25'). I used a CASE statement to isolate these invalid dates into an "Invalid Date Format" category, ensuring valid monthly revenue totals were accurate and no data was silently lost.

SELECT 
    Product_Name, 
    ROUND(SUM(CAST(Sales AS REAL)), 2) AS Total_Revenue
FROM train
GROUP BY Product_Name
ORDER BY Total_Revenue DESC
LIMIT 10;

Result: Used the CAST() function to ensure the Sales column was treated as a numeric value before aggregation, then used LIMIT 10 to identify the highest revenue-generating products for inventory prioritization.

text

4. Scroll down and click **Commit changes**.

---

### Final Step: Update Your CV (The SQL Section)

You can now confidently add the SQL project to your CV. Here is the updated, polished text for your CV:

**SQL Sales Data Analysis** | *SQL (SQLite), Data Aggregation, Data Cleaning*
*   Analyzed a dataset of 9,800 retail sales records using SQL to answer key business questions and extract actionable insights.
*   Cleaned raw data by renaming unformatted columns and removing redundant header rows to ensure mathematical accuracy.
*   Utilized `GROUP BY` and `SUM()` to identify the West region as the top revenue driver ($710k+), and employed `HAVING` clauses to filter aggregated data.
*   Identified and resolved data quality issues by using `CASE` statements to catch invalid date formats, preventing the loss of $1.3 million in sales data during time-series analysis.

<img width="814" height="828" alt="image" src="https://github.com/user-attachments/assets/f7d46381-9317-44af-aa58-b031d7dc7021" />

