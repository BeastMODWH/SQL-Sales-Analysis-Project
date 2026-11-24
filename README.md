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
**Query:**
```sql
SELECT SUM(Sales) AS Total_Revenue 
FROM train;

Result: The company generated $2,261,536.78 in total sales across the dataset.

2. Which Region Generates the Most Revenue?
SELECT Region, SUM(Sales) AS Total_Revenue
FROM train
GROUP BY Region
ORDER BY Total_Revenue DESC;

Result: The West region is the top performer with $710,219.68, followed by the East region. The South region is the lowest performer.

3. Which Regions have a High Average Order Value?
SELECT Region, AVG(Sales) AS Average_Order_Value
FROM train
GROUP BY Region
HAVING AVG(Sales) > 200
ORDER BY Average_Order_Value DESC;

Result: Used the HAVING clause to filter aggregated data. It revealed that the South region, despite having the lowest total revenue, actually has the highest average order value ($243.52). This indicates they sell fewer items, but higher-value items.

4. Top 10 Products by Revenue
SELECT 
    Product_Name, 
    ROUND(SUM(CAST(Sales AS REAL)), 2) AS Total_Revenue
FROM train
GROUP BY Product_Name
ORDER BY Total_Revenue DESC
LIMIT 10;

Result: Used the CAST() function to ensure the Sales column was treated as a numeric value before aggregation, then used LIMIT 10 to identify the highest revenue-generating products for inventory prioritization.

5. Time Series Analysis & Handling Dirty Data
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

Part 2: Customer Segmentation Analysis

Business Question

Which customer segment (Consumer, Corporate, Home Office) generates the highest total revenue and places the most unique orders?

SELECT 
    Segment,
    COUNT(DISTINCT Order_ID) AS Total_Orders,
    ROUND(SUM(CAST(Sales AS REAL)), 2) AS Total_Revenue
FROM train
GROUP BY Segment
ORDER BY Total_Revenue DESC;

Result & Insight

The data clearly shows that the Consumer segment is the primary revenue driver, generating $1,148,060.53 across 2,537 unique orders.

The Corporate segment generated $688,494.07 (1,491 orders).
The Home Office segment generated $424,982.18 (894 orders).
Business Value: If the company wants to increase revenue, they should focus their marketing budget and retention campaigns heavily on the Consumer segment, as they account for more than 50% of total revenue.
