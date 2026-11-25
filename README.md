# SQL Sales Data Analysis

## Project Overview
This project demonstrates the use of SQL to query and analyze a dataset of 9,800 retail sales records (Superstore Dataset). The goal was to extract actionable business insights regarding revenue generation, regional performance, customer segmentation, and data quality.

## Tools Used
*   **SQLite Online** (Database environment)
*   **SQL** (Data querying, aggregation, and data cleaning)

## Data Preparation
*   Imported a raw CSV file into a SQL database.
*   Renamed raw, unformatted columns (e.g., `c1`, `c2`) to meaningful business headers (e.g., `Order_ID`, `Region`, `Sales`).
*   Deleted the redundant header row from the dataset to ensure accurate mathematical calculations.

---

## Part 1: Revenue, Regional Performance & Data Quality

### 1. What is the Total Revenue?

### Query
```sql
SELECT SUM(Sales) AS Total_Revenue 
FROM train;
```
Result: The company generated $2,261,536.78 in total sales across the dataset.

### 2. Which Region Generates the Most Revenue?

### Query
```sql
SELECT Region, SUM(Sales) AS Total_Revenue
FROM train
GROUP BY Region
ORDER BY Total_Revenue DESC;
```
Result: The West region is the top performer with $710,219.68, followed by the East region. The South region is the lowest performer.

## 3. Which Regions have a High Average Order Value?

### Query
```sql
SELECT Region, AVG(Sales) AS Average_Order_Value
FROM train
GROUP BY Region
HAVING AVG(Sales) > 200
ORDER BY Average_Order_Value DESC;
```
Result: Used the HAVING clause to filter aggregated data. It revealed that the South region, despite having the lowest total revenue, actually has the highest average order value ($243.52). This indicates they sell fewer items, but higher-value items.

## 4. Top 10 Products by Revenue

### Query
```sql
SELECT 
    Product_Name, 
    ROUND(SUM(CAST(Sales AS REAL)), 2) AS Total_Revenue
FROM train
GROUP BY Product_Name
ORDER BY Total_Revenue DESC
LIMIT 10;
```
Result: Used the CAST() function to ensure the Sales column was treated as a numeric value before aggregation, then used LIMIT 10 to identify the highest revenue-generating products for inventory prioritization.

## 5. Time Series Analysis & Handling Dirty Data

### Query
```sql  
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
```
Result: While grouping revenue by month, I encountered a NULL value that represented over $1.3 million in sales. I investigated the raw data and discovered inconsistent date formats (some were DD/MM/YYYY, causing strftime to fail on impossible months like '25'). I used a CASE statement to isolate these invalid dates into an "Invalid Date Format" category, ensuring valid monthly revenue totals were accurate and no data was silently lost.

## Part 2: Customer Segmentation Analysis

## Business Question

Which customer segment (Consumer, Corporate, Home Office) generates the highest total revenue and places the most unique orders?

### Query
```sql
SELECT 
    Segment,
    COUNT(DISTINCT Order_ID) AS Total_Orders,
    ROUND(SUM(CAST(Sales AS REAL)), 2) AS Total_Revenue
FROM train
GROUP BY Segment
ORDER BY Total_Revenue DESC;
```
## Result & Insight

The data clearly shows that the Consumer segment is the primary revenue driver, generating $1,148,060.53 across 2,537 unique orders.

The Corporate segment generated $688,494.07 (1,491 orders).
The Home Office segment generated $424,982.18 (894 orders).
Business Value: If the company wants to increase revenue, they should focus their marketing budget and retention campaigns heavily on the Consumer segment, as they account for more than 50% of total revenue.

## Key Learnings

*   Mastered the difference between `WHERE` (filtering rows) and `HAVING` (filtering aggregated groups).
*   Learned how to clean raw text data using `ALTER TABLE` and `DELETE` commands.
*   Gained experience using `GROUP BY` and `ORDER BY` to summarize thousands of rows into clear, actionable business metrics.
*   Developed techniques to handle dirty data (invalid dates) using `CASE` statements to prevent data loss.
*   Learned how to aggregate distinct counts (`COUNT(DISTINCT)`) to avoid double-counting orders.

  ---

## Part 3: Shipping Efficiency Analysis

### Business Question
What is the average number of days it takes to ship an order for each `Ship_Mode` (Standard Class, First Class, etc.)?

### Query
```sql
SELECT 
    Ship_Mode,
    ROUND(AVG(
        julianday(SUBSTR(Ship_Date, 7, 4) || '-' || SUBSTR(Ship_Date, 1, 2) || '-' || SUBSTR(Ship_Date, 4, 2)) - 
        julianday(SUBSTR(Order_Date, 7, 4) || '-' || SUBSTR(Order_Date, 1, 2) || '-' || SUBSTR(Order_Date, 4, 2))
    ), 2) AS Avg_Days_To_Ship
FROM train
WHERE Ship_Date IS NOT NULL AND Order_Date IS NOT NULL
GROUP BY Ship_Mode
ORDER BY Avg_Days_To_Ship DESC;
```
### Result & Insight
*   **Same Day** shipping: **1.96 days**
*   **First Class** shipping: **62.84 days**
*   **Second Class** shipping: **97.00 days**
*   **Standard Class** shipping: **145.77 days**

**Data Quality Anomaly:** The average shipping times for Standard and Second Class are unrealistically high (over 90 days). This indicates a severe data quality issue in the source dataset where dates may have been mixed between US (MM/DD/YYYY) and UK (DD/MM/YYYY) formats, creating extreme outliers. 

**Business Action:** In a production environment, I would immediately flag this anomaly to the Data Engineering team. To handle this in the query, I would add a filter to exclude shipping times that are illogical (e.g., filtering for `BETWEEN 0 AND 10` days) to find the true average for First Class and Same Day, while investigating the Standard Class errors separately.
