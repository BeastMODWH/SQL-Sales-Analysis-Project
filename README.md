# SQL-Sales-Analysis-Project
# SQL Sales Data Analysis

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
