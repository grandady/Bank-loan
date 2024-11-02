# Data Exploration

## Bank Loan

To effectively monitor and assess our bank's lending activities and performance, we need to create a comprehensive Bank Loan Report. This report will provide critical insights into key loan-related metrics and their changes over time, supporting data-driven decisions, tracking the health of our loan portfolio, and identifying trends to inform our lending strategies.

## Business Tasks

## Key Performance Indicators (KPIs) Requirements:

1. Total Loan Applications: Calculate the total number of loan applications received during a specified period. Monitor the Month-to-Date (MTD) Loan Applications and track Month-over-Month (MoM) changes.

2. Total Funded Amount: Determine the total amount of funds disbursed as loans. Monitor the MTD Total Funded Amount and analyze MoM changes in this metric.

3. Total Amount Received: Track the total amount received from borrowers to assess cash flow and loan repayment. Analyze the MTD Total Amount Received and observe MoM changes.

4. Average Interest Rate: Calculate the average interest rate across all loans. Monitor the MTD average interest rate and track MoM variations to gain insights into the overall cost of our lending portfolio.

5. Average Debt-to-Income Ratio (DTI): Evaluate the average DTI for our borrowers to gauge their financial health. Compute the MTD average DTI and track MoM fluctuations.

This structured report will equip us with the necessary information to ensure our lending practices are effective and sustainable.

## Data Source

Bank Loan data :The primary data used for this analysis"Bankloandata.csv" file, containg detailed information about each sale made by the company

## Methodology

- Data Importation: Import datasets into SQL Server for comprehensive analysis.

- Exploring Loan Data: Generate various analytics and insights based on loan application data and borrower profiles.

- Analyzing Loan Applications: Assess trends and patterns in loan applications over time.

- Tracking Funded Amounts: Evaluate the trends in the total funded amount and analyze Month-to-Date (MTD) and Month-over-Month (MoM) changes.

- Monitoring Amounts Received: Track the total amount received from borrowers and assess cash flow patterns.

- Calculating Average Interest Rates: Determine average interest rates across all loans, MTD, and MoM variations.

- Evaluating Average Debt-to-Income Ratio (DTI): Compute the average DTI for borrowers, MTD, and MoM fluctuations to gauge financial health.

- Advanced SQL Techniques: Utilize a range of SQL techniques including:

    - Basic SQL Queries for foundational analysis.

    - Sub Queries and Common Table Expressions (CTEs) to solve complex problems.

    - SQL Aggregate Functions to compute summary statistics.

    - SQL Window Functions for advanced calculations.

- Data Visualization in Tableau: Create intuitive visualizations and dashboards to represent findings and facilitate data-driven decision-making.

  ## Data Exploration
  ### Column Dictionary

1.  Loan ID: Unique identifier for each loan application or account. Banks use it to track and manage loans.

2.  Address State: Indicates the borrower's location. Banks use it for regional risk assessment and compliance.

3.  Employee Length: Provides insights into employment stability. Banks assess it to gauge default risk.

4.  Employee Title: Specifies the borrower's job title. Banks verify income sources and assess financial capacity.

5.  Grade: Represents credit risk classification. Banks use it to price loans and manage risk.

6.  Sub Grade: Further refines risk assessment within a grade. Banks tailor interest rates accordingly.

7.  Home Ownership: Indicates housing status. Banks assess collateral availability and borrower stability.

8.  Issue Date: Marks the loan's origination date. Banks track loan aging and calculate interest.

9.  Last Credit Pull Date: Records when the credit report was last accessed. Banks monitor creditworthiness.

10.  Last Payment Date: Marks the most recent payment. Banks assess payment behavior and delinquency.

11.  Loan Status: Indicates the current state of the loan. Banks monitor loan health and risk.

12.  Next Payment Date: Estimates the next payment date. Banks use it for cash flow forecasting.

13.  Purpose: Specifies the loan reason. Banks segment and customize loan offerings.

14.  Term: Defines the loan duration in months. Banks structure repayment and interest.

15.  Verification Status: Indicates if financial information is verified. Banks assess data accuracy.

16.  Annual Income: Reflects yearly earnings. Banks determine eligibility and creditworthiness.

17.  DTI (Debt-to-Income Ratio): Measures debt burden relative to income. Banks assess repayment capacity.

18.  Instalment: Fixed monthly payment amount. Banks structure loan terms and affordability.

19.  Interest Rate: Annual cost of borrowing. Banks price loans and manage profits.

20.  Loan Amount: Total borrowed sum. Banks determine loan size.
 

### Data Exploration

**Step 1: Yearly Sales Trend Analysis**
Objective: Analyze sales revenue trends over the years to identify growth patterns and any potential seasonality in sales.

````sql
SELECT YEAR(orderdate) AS Year, 
       SUM(sales) AS TotalSales
FROM [dbo].[sales_data_sample]
GROUP BY YEAR(orderdate)
ORDER BY Year;

````

**2.Monthly Sales Trend**
Objective: Examine monthly sales data to detect monthly patterns and trends.

````sql
SELECT YEAR(orderdate) AS Year, 
       MONTH(orderdate) AS Month, 
       SUM(sales) AS TotalSales
FROM [dbo].[sales_data_sample]
GROUP BY YEAR(orderdate), MONTH(orderdate)
ORDER BY Year, Month;
````

**3. Which month had the highest sales within a given year? What was the total revenue for that month?**

````sql
SELECT MONTH_ID, SUM(sales) AS Revenue, COUNT(ORDERNUMBER) AS Frequency
FROM [PortfolioDB].[dbo].[sales_data_sample]
WHERE YEAR_ID = 2004  -- Change year to see the rest
GROUP BY MONTH_ID
ORDER BY Revenue DESC;

````

**4. whats Products sell most in the month of November?**

````sql
SELECT MONTH_ID, PRODUCTLINE, SUM(sales) AS Revenue, COUNT(ORDERNUMBER) AS OrderCount
FROM [dbo].[sales_data_sample]
WHERE YEAR_ID = 2004 AND MONTH_ID = 11  --change year to see the rest
GROUP BY MONTH_ID, PRODUCTLINE
ORDER BY Revenue DESC;

````

**5. Grouping by PRODUCTLINE and sorting by revenue**

````sql
SELECT PRODUCTLINE, SUM(sales) AS Revenue
FROM [dbo].[sales_data_sample]
GROUP BY PRODUCTLINE
ORDER BY Revenue DESC;

````

**6. Grouping by CITY and sorting by revenue**

````sql
SELECT city, SUM(sales) AS Revenue
FROM [PortfolioDB].[dbo].[sales_data_sample]
GROUP BY city
ORDER BY Revenue DESC;

````
**7. Grouping by DEAlSIZE and sorting by revenue**

````sql
SELECT DEALSIZE, SUM(sales) AS Revenue
FROM [PortfolioDB].[dbo].[sales_data_sample]
GROUP BY DEALSIZE
ORDER BY Revenue DESC;

````
**8. Grouping by TERRITORY and sorting by revenue**

````sql
SELECT TERRITORY, SUM(sales) AS Revenue
FROM [PortfolioDB].[dbo].[sales_data_sample]
GROUP BY TERRITORY
ORDER BY Revenue DESC;

````

**9. Grouping by STATUS and sorting by revenue**

````sql
SELECT STATUS, SUM(sales) AS Revenue
FROM [PortfolioDB].[dbo].[sales_data_sample]
GROUP BY STATUS
ORDER BY Revenue DESC;

````
**10. Top Customers**

````sql
SELECT customername, SUM(sales) AS TotalSales, COUNT(ORDERLINENUMBER) AS PurchaseFrequency
FROM [dbo].[sales_data_sample]
GROUP BY customername
ORDER BY TotalSales DESC, PurchaseFrequency DESC;

````
**11. Customer Segmentation**

````sql
DROP TABLE IF EXISTS #rfm;

WITH rfm AS (
    SELECT CUSTOMERNAME,
           SUM(sales) AS MonetaryValue,
           AVG(sales) AS AvgMonetaryValue,
           COUNT(ORDERNUMBER) AS Frequency,
           MAX(ORDERDATE) AS last_order_date,
           (SELECT MAX(ORDERDATE) FROM [dbo].[sales_data_sample]) AS max_order_date,
           DATEDIFF(DD, MAX(ORDERDATE), (SELECT MAX(ORDERDATE) FROM [dbo].[sales_data_sample])) AS Recency
    FROM [PortfolioDB].[dbo].[sales_data_sample]
    GROUP BY CUSTOMERNAME
),
rfm_calc AS (
    SELECT r.*,
           NTILE(4) OVER (ORDER BY Recency DESC) AS rfm_recency,
           NTILE(4) OVER (ORDER BY Frequency) AS rfm_frequency,
           NTILE(4) OVER (ORDER BY MonetaryValue) AS rfm_monetary
    FROM rfm r
)
SELECT c.*, 
       rfm_recency + rfm_frequency + rfm_monetary AS rfm_cell,
       CAST(rfm_recency AS VARCHAR) + CAST(rfm_frequency AS VARCHAR) + CAST(rfm_monetary AS VARCHAR) AS rfm_cell_string
INTO #rfm
FROM rfm_calc c;

SELECT CUSTOMERNAME, 
       rfm_recency, 
       rfm_frequency, 
       rfm_monetary,
       CASE 
           WHEN rfm_cell_string IN ('111', '112', '121', '122', '123', '132', '211', '212', '114', '141') THEN 'lost_customers'
           WHEN rfm_cell_string IN ('133', '134', '143', '244', '334', '343', '344', '144') THEN 'slipping away, cannot lose'
           WHEN rfm_cell_string IN ('311', '411', '331') THEN 'new customers'
           WHEN rfm_cell_string IN ('222', '223', '233', '322') THEN 'potential churners'
           WHEN rfm_cell_string IN ('323', '333', '321', '422', '332', '432') THEN 'active'
           WHEN rfm_cell_string IN ('433', '434', '443', '444') THEN 'loyal'
       END AS rfm_segment
FROM #rfm;

````
** 12.the Two Most Frequently Purchased Product Pairings**

````sql
SELECT DISTINCT OrderNumber, 
    STUFF((
        SELECT ',' + PRODUCTCODE
        FROM [dbo].[sales_data_sample] p
        WHERE ORDERNUMBER IN (
            SELECT ORDERNUMBER
            FROM (
                SELECT ORDERNUMBER, COUNT(*) rn
                FROM [PortfolioDB].[dbo].[sales_data_sample]
                WHERE STATUS = 'Shipped'
                GROUP BY ORDERNUMBER
            ) m
            WHERE rn = 2
        )
        AND p.ORDERNUMBER = s.ORDERNUMBER
        FOR XML PATH('')
    ), 1, 1, '') AS ProductCodes
FROM [dbo].[sales_data_sample] s
ORDER BY ProductCodes DESC;

````




an
