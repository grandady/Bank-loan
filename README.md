# Data Exploration and Visualization

## Liberty Bank Loan

##  Table of Contents
- [Business Task](#business-task)
- [Methodology](#methodology)
- [Data Exploration](#data-exploration)
- [Visualization](#visualization)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
- [References](#references)
***

## Liberty  Bank Loan

Liberty Bank is a reputable financial institution that empowers individuals and businesses through innovative banking solutions. With a focus on customer service and community growth, it offers a variety of loans, accounts, and investment options. The bank emphasizes financial inclusion and responsible lending to ensure the well-being of its clients and foster economic stability.


## Business Tasks

To effectively monitor and assess our bank's lending activities and performance, we must create a comprehensive Bank Loan Report. This report will provide critical insights into key loan-related metrics and their changes over time, supporting data-driven decisions, tracking the health of our loan portfolio, and identifying trends to inform our lending strategies.


## Key Performance Indicators (KPIs) Requirements:

1. Total Loan Applications: Calculate the total number of loan applications received during a specified period. Monitor the Month-to-Date (MTD) Loan Applications and track Month-over-Month (MoM) changes.

2. Total Funded Amount: Determine the total funds disbursed as loans. Monitor the MTD Total Funded Amount and analyze MoM changes in this metric.

3. Total Amount Received: Track the total amount received from borrowers to assess cash flow and loan repayment. Analyze the MTD Total Amount Received and observe MoM changes.

4. Average Interest Rate: Calculate the average interest rate across all loans. Monitor the MTD average interest rate and track MoM variations to gain insights into the overall cost of our lending portfolio.

5. Average Debt-to-Income Ratio (DTI): Evaluate the average DTI for our borrowers to gauge their financial health. Compute the MTD average DTI and track MoM fluctuations.

This structured report will equip us with the necessary information to ensure our lending practices are effective and sustainable.

## Data Source

Financial Loan data: The primary data used for this analysis"Financial_loan.csv" file, contains detailed information about each sale made by the company

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

6.  Sub-Grade: Further refines risk assessment within a grade. Banks tailor interest rates accordingly.

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

### Loan Application Metrics

**Total Loan Application**

Objective: Tracking total loan applications helps Liberty Bank understand loan demand, identify trends, and make informed decisions about marketing and resource allocation. It also evaluates the effectiveness of loan products and campaigns.

````sql
SELECT COUNT(*) AS Total_Application
	FROM [dbo].[bank_loan];

````

**Month-to-Date Total Loan Applicant**

Objective: Tracking Month-to-Date (MTD) loan applications provides real-time insights into current trends, enabling Liberty Bank to monitor performance and make timely adjustments to enhance loan offerings and customer engagement..

````sql
 SELECT COUNT(*) AS MTD_Total_Loan_Applications
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);
````

**Month to Month Total Loan Application percentage growth**

Objective: Tracking Month-over-Month (MoM) total loan application percentage growth helps Liberty Bank measure changes in loan applications, identify growth trends, and assess marketing effectiveness.

````sql
WITH previous_mtd AS (
    SELECT COUNT(*) AS PMTD_Total_Loan_Applications
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) - 1, 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
),
mtd AS (
    SELECT COUNT(*) AS MTD_Total_Loan_Applications
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0)
)
SELECT 
    previous_mtd.PMTD_Total_Loan_Applications,
    mtd.MTD_Total_Loan_Applications,
    CASE 
        WHEN mtd.MTD_Total_Loan_Applications = 0 THEN 0
        ELSE ((mtd.MTD_Total_Loan_Applications - previous_mtd.PMTD_Total_Loan_Applications) * 100.0 / previous_mtd.PMTD_Total_Loan_Applications)
    END AS Percentage_Change
FROM previous_mtd, mtd;

````

### Money Loaned Metrics

**Total Money Loaned to Applicant**

Objective: This metric is essential for understanding the overall lending volume and the bank's financial commitment to borrowers. It provides insights into loan portfolio growth and helps assess the bank's lending capacity and risk exposure

````sql
SELECT SUM(loan_amount) AS Total_Application
	FROM [dbo].[bank_loan];

````

**Month to Date Total Loaned to  applicant**

Objective: Tracking the Month-to-Date (MTD) Total Amount Loaned to Applicants provides real-time insights into current lending activity, helping Liberty Bank monitor performance and adjust strategies to meet financial targets and borrower needs.

````sql
SELECT SUM(loan_amount) AS MTD_Total_Loan_Applications
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````

**Month to Month Total Loan Amount percentage growth**

Objective: Tracking Month-over-Month (MoM) Total Loan Amount percentage growth measures changes in the total loan amount disbursed each month, helping Liberty Bank identify trends, evaluate strategy effectiveness, and optimize loan distribution.

````sql
WITH previous_mtd AS (
    SELECT SUM(loan_amount) AS PMTD_Total_Loan_Amount
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) - 1, 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0) --- previous Month
),
mtd AS (
    SELECT SUM(loan_amount) AS MTD_Total_Loan_Amount
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0)
)
SELECT 
    previous_mtd.PMTD_Total_Loan_Amount,
    mtd.MTD_Total_Loan_Amount,
    CASE 
        WHEN mtd.MTD_Total_Loan_Amount = 0 THEN 0
        ELSE ((mtd.MTD_Total_Loan_Amount - previous_mtd.PMTD_Total_Loan_Amount) * 100.0 / previous_mtd.PMTD_Total_Loan_Amount)
    END AS Percentage_Change
FROM previous_mtd, mtd;

````

### Payment Received


**Total Payment Received from Applicants**

 Objective: Tracking Total Payments Received from Applicants measures the inflow of funds from borrowers, helping Liberty Bank monitor cash flow, assess loan repayment effectiveness, and ensure financial stability.
 
````sql

SELECT SUM(total_payment) AS Total_Application
	FROM [dbo].[bank_loan];

````

**Month-Date Total Payment Received from Applicants**

Objectives: Tracking Month-to-Date (MTD) Total Payments Received from Applicants provides current insights into repayment trends, helping Liberty Bank monitor cash flow and evaluate collection strategy effectiveness in real time.

````sql
SELECT SUM(total_payment) AS MTD_Total_Money_Recieved
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````

**Month to Month Total Money Received  percentage growth***

Objective: Tracking Month-over-Month (MoM) Total Money Received percentage growth helps Liberty Bank measure changes in repayments, identify trends, and evaluate collection strategy effectiveness.

````sql
 WITH previous_mtd AS (
    SELECT SUM(total_payment) AS PMTD_Total_Money_Recieved
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) - 1, 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0) --- previous Month
),
mtd AS (
    SELECT SUM(total_payment) AS MTD_Total_Money_Recieved
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0)
)
SELECT 
    previous_mtd.PMTD_Total_Money_Recieved,
    mtd.MTD_Total_Money_Recieved,
    CASE 
        WHEN mtd.MTD_Total_Money_Recieved = 0 THEN 0
        ELSE ((mtd.MTD_Total_Money_Recieved - previous_mtd.PMTD_Total_Money_Recieved) * 100.0 / previous_mtd.PMTD_Total_Money_Recieved)
    END AS Percentage_Change
FROM previous_mtd, mtd;

````

### Interest Rate

** Average Interest Rate **

Objective: Tracking the Average Interest Rate helps Liberty Bank understand borrowing costs for customers, monitor loan product competitiveness, and assess pricing strategies to ensure profitability and attract borrowers.

````sql
SELECT AVG(int_rate) * 100 AS Avg_int
	FROM [dbo].[bank_loan]

````
**Month-to-Date Average Interest Rate**

Objective: Tracking the Month-to-Date (MTD) Average Interest Rate helps Liberty Bank monitor the current borrowing costs for customers, evaluate interest rate strategies, and make adjustments to remain competitive and profitable.

````sql
 SELECT AVG(int_rate)*100 AS MTD_Average_interest_Rate
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````
**Month to Month Average interest Rate growth**

Objective: Tracking Month-over-Month (MoM) Average Interest Rate growth helps Liberty Bank measure changes in average interest rates monthly, identify trends, and evaluate interest rate strategy effectiveness.

````sql
WITH previous_mtd AS (
    SELECT AVG(int_rate) AS PMTD_Average_interest_Rate
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) - 1, 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0) --- previous Month
),
mtd AS (
    SELECT AVG(int_rate) AS MTD_Average_interest_Rate
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0)
)
SELECT 
    previous_mtd.PMTD_Average_interest_Rate,
    mtd.MTD_Average_interest_Rate,
    CASE 
        WHEN mtd.MTD_Average_interest_Rate = 0 THEN 0
        ELSE ((mtd.MTD_Average_interest_Rate - previous_mtd.PMTD_Average_interest_Rate) * 100.0 / previous_mtd.PMTD_Average_interest_Rate)
    END AS Percentage_Change
FROM previous_mtd, mtd;

### Debt to Income Rate

** Average Debt to Income Rate**

Objective: Tracking the Debt-to-Income (DTI) Ratio helps Liberty Bank assess borrowers' ability to repay debts, ensuring responsible lending and protecting both the bank and borrowers' financial health.

````sql
SELECT AVG(dti) * 100 AS Avg_dti
	FROM [dbo].[bank_loan]

````
**Month to Date Debt to Income Rate**

Objective: Tracking the Month-to-Date (MTD) Debt-to-Income (DTI) Ratio helps Liberty Bank evaluate borrowers' current financial health, making informed lending decisions to mitigate risk and ensure responsible lending.

````sql
  SELECT AVG(dti)*100 AS MTD_Debt_to_Income_Rate
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````
**Month to Month Debt to Income Rate growth**
Objective: Tracking Month-over-Month (MoM) Debt-to-Income (DTI) Ratio growth helps Liberty Bank measure changes in borrowers' debt burden relative to income, identify trends in financial health, and evaluate lending policy effectiveness to mitigate risk and ensure responsible lending.

````sql
WITH previous_mtd AS (
    SELECT AVG(dti) AS PMTD_Debt_to_Income_Rate
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) - 1, 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0) --- previous Month
),
mtd AS (
    SELECT AVG(dti) AS MTD_Debt_to_Income_Rate
    FROM [portfolio].[dbo].[bank_loan]
    WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
      AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0,(SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0)
)
SELECT 
    previous_mtd.PMTD_Debt_to_Income_Rate,
    mtd.MTD_Debt_to_Income_Rate,
    CASE 
        WHEN mtd.MTD_Debt_to_Income_Rate = 0 THEN 0
        ELSE ((mtd.MTD_Debt_to_Income_Rate - previous_mtd.PMTD_Debt_to_Income_Rate) * 100.0 / previous_mtd.PMTD_Debt_to_Income_Rate)
    END AS Percentage_Change
FROM previous_mtd, mtd;

````




an
