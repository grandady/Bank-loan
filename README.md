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

- **Total Loan Application**

**Objective:** Tracking total loan applications helps Liberty Bank understand loan demand, identify trends, and make informed decisions about marketing and resource allocation. It also evaluates the effectiveness of loan products and campaigns.

````sql
SELECT COUNT(*) AS Total_Application
	FROM [dbo].[bank_loan];

````

**Answer** 

The total number of loan applications received is 38,576 

- **Month-to-Date Total Loan Applicant**

**Objective:** Tracking Month-to-Date (MTD) loan applications provides real-time insights into current trends, enabling Liberty Bank to monitor performance and make timely adjustments to enhance loan offerings and customer engagement..

````sql
 SELECT COUNT(*) AS MTD_Total_Loan_Applications
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````

**Answer**

The Month-to-Date total number of loan applicants is 4,314

- **Month to Month Total Loan Application percentage growth**

**Objective:** Tracking Month-over-Month (MoM) total loan application percentage growth helps Liberty Bank measure changes in loan applications, identify growth trends, and assess marketing effectiveness.

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

**Answer**. 

The Month-to-Month total loan application percentage growth is 6.91%.


### Money Loaned Metrics

- **Total Money Loaned to Applicant**

**Objective:** This metric is essential for understanding the overall lending volume and the bank's financial commitment to borrowers. It provides insights into loan portfolio growth and helps assess the bank's lending capacity and risk exposure

````sql
SELECT SUM(loan_amount) AS Total_Application
	FROM [dbo].[bank_loan];

````

**Answer** 

The total amount of money loaned is $ 435,757,075.

- **Month to Date Total Loaned to  applicant**

**Objective:** Tracking the Month-to-Date (MTD) Total Amount Loaned to Applicants provides real-time insights into current lending activity, helping Liberty Bank monitor performance and adjust strategies to meet financial targets and borrower needs.

````sql
SELECT SUM(loan_amount) AS MTD_Total_Loan_Applications
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````

**Answer** 

The Month-to-Date total amount loaned is $ 53,981,425.

- Month to Month Total Loan Amount percentage growth**

**Objective:** Tracking Month-over-Month (MoM) Total Loan Amount percentage growth measures changes in the total loan amount disbursed each month, helping Liberty Bank identify trends, evaluate strategy effectiveness, and optimize loan distribution.

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

**Answer** The Month-to-Month total loan amount percentage growth is 13.04%.

### Payment Received


- **Total Payment Received from Applicants**

 **Objective:** Tracking Total Payments Received from Applicants measures the inflow of funds from borrowers, helping Liberty Bank monitor cash flow, assess loan repayment effectiveness, and ensure financial stability.
 
````sql

SELECT SUM(total_payment) AS Total_Application
	FROM [dbo].[bank_loan];

````

**Answer**

The total amount of payment received is $ 473,070,933.

- **Month-Date Total Payment Received from Applicants**

**Objectives:** Tracking Month-to-Date (MTD) Total Payments Received from Applicants provides current insights into repayment trends, helping Liberty Bank monitor cash flow and evaluate collection strategy effectiveness in real time.

````sql
SELECT SUM(total_payment) AS MTD_Total_Money_Recieved
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````

**Answer**

The Month-to-Date total payment received is $ 58,074,380.

- **Month to Month Total Money Received  percentage growth***

**Objective:** Tracking Month-over-Month (MoM) Total Money Received percentage growth helps Liberty Bank measure changes in repayments, identify trends, and evaluate collection strategy effectiveness.

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

**Answer**

The Month-to-Month total money received percentage growth is 15.84%.

### Interest Rate

- **Average Interest Rate**

**Objective:** Tracking the Average Interest Rate helps Liberty Bank understand borrowing costs for customers, monitor loan product competitiveness, and assess pricing strategies to ensure profitability and attract borrowers.

````sql
SELECT AVG(int_rate) * 100 AS Avg_int
	FROM [dbo].[bank_loan]

````

**Answer**he average interest rate is 12.05%.

- **Month-to-Date Average Interest Rate**

**Objective:** Tracking the Month-to-Date (MTD) Average Interest Rate helps Liberty Bank monitor the current borrowing costs for customers, evaluate interest rate strategies, and make adjustments to remain competitive and profitable.

````sql
 SELECT AVG(int_rate)*100 AS MTD_Average_interest_Rate
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````

**Answer**

The Month-to-Date average interest rate is 12.36%.

- **Month to Month Average interest Rate growth**

**Objective:** Tracking Month-over-Month (MoM) Average Interest Rate growth helps Liberty Bank measure changes in average interest rates monthly, identify trends, and evaluate interest rate strategy effectiveness.

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


````
- **Answer**
-
- The Month-to-Month average interest rate growth stands at 3.47%


### Debt to Income Rate

**Average Debt to Income Rate**

**Objective:** Tracking the Debt-to-Income (DTI) Ratio helps Liberty Bank assess borrowers' ability to repay debts, ensuring responsible lending and protecting both the bank and borrowers' financial health.

````sql
SELECT AVG(dti) * 100 AS Avg_dti
	FROM [dbo].[bank_loan]

````
**Answer**

The average Debt-to-Income rate is 13.33%.

- **Month-to-Date Debt to Income Rate**

**Objective:** Tracking the Month-to-Date (MTD) Debt-to-Income (DTI) Ratio helps Liberty Bank evaluate borrowers' current financial health, making informed lending decisions to mitigate risk and ensure responsible lending.

````sql
  SELECT AVG(dti)*100 AS MTD_Debt_to_Income_Rate
FROM [portfolio].[dbo].[bank_loan]
WHERE issue_date >= DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])), 0)
  AND issue_date < DATEADD(MONTH, DATEDIFF(MONTH, 0, (SELECT MAX(issue_date) FROM [portfolio].[dbo].[bank_loan])) + 1, 0);

````
**Answer**

The Month-to-Date Debt-to-Income rate is 13.67%.

- **Month to Month Debt to Income Rate growth**

**Objective:** Tracking Month-over-Month (MoM) Debt-to-Income (DTI) Ratio growth helps Liberty Bank measure changes in borrowers' debt burden relative to income, identify trends in financial health, and evaluate lending policy effectiveness to mitigate risk and ensure responsible lending.

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
**Answer**

The Month-to-Month Debt-to-Income rate growth is 2.73%.

## GOOD AND BAD LOAN METRICS
good loan is characterized by a status of "fully paid" or "current," indicating that the borrower is meeting their repayment obligations promptly. Conversely, a bad loan is defined as having a status of "charged off," signifying that the lender has deemed the loan uncollectible and has written it off as a loss.


## Good Loan Metrics

- **Good loan Applicants**

**Objective:** Identifying a "good loan applicant" helps Liberty Bank minimize risks, maintain a healthy loan portfolio, and ensure responsible lending practices.
 

````sql
SELECT COUNT(*) AS Good_loan_Applicant
	FROM [dbo].[bank_loan]
	WHERE loan_status IN ('Fully Paid','Current');


````

**Answer**

The number of good loan applicants stands at 33,243.

- **Good loan Total Amount Loaned**

**Objective:** Tracking the Good Loan Total Amount Loaned helps Liberty Bank evaluate its lending success, identify trends in loan performance, and ensure a healthy loan portfolio.
 

````sql
SELECT SUM(loan_amount) AS Good_loan_Total_Amount_Loaned
	FROM [dbo].[bank_loan]
	WHERE loan_status IN ('Fully Paid','Current');

````

**Answer**

The total amount loaned to good loan applicants is $ 370,224,850.

- **Good loan Total Amount Received**

**Objective:** Tracking the Good Loan Total Amount Received helps Liberty Bank maintain cash flow and ensure financial stability.
 

````sql
SELECT SUM(total_payment) AS Good_loan_Total_payment
	FROM [dbo].[bank_loan]
	WHERE loan_status IN ('Fully Paid','Current');

````

**Answer**

The total amount received from good loan applicants is $ 435,786,170.

- **Good loan Average Interest Rate**

**Objective:** Tracking the Good Loan Average Interest Rate helps Liberty Bank set competitive and profitable borrowing costs.
 

````sql
SELECT Avg(int_rate)*100 AS Good_loan_Average_interest
	FROM [dbo].[bank_loan]
	WHERE loan_status IN ('Fully Paid','Current');

````

**Answer**

The average interest rate for good loan applicants is 11.76%.


- **Good loan Average Debt to Income Rate**

**Objective:** Tracking the Good Loan Average Debt-to-Income (DTI) Rate helps Liberty Bank assess reliable borrowers' financial health and ensure responsible lending.
 

````sql
SELECT Avg(dti)*100 AS Good_loan_Average_dti
	FROM [dbo].[bank_loan]
	WHERE loan_status IN ('Fully Paid','Current');

````

**Answer**

The average Debt-to-Income rate for good loan applicants is 13.22%.


## Bad Loan Metrics


- **Bad loan Applicants**

**Objective:** Tracking bad loan applicants helps Liberty Bank identify high-risk individuals and adjust lending criteria to minimize financial losses.
 

````sql
SELECT COUNT(*) AS Bad_loan_Applicant
	FROM [dbo].[bank_loan]
	WHERE loan_status NOT IN ('Fully Paid','Current');
 

````
**Answer** 

The number of bad loan applicants is 5,333.

- **Bad loan Total Amount Loaned**

**Objective:** Tracking the Bad Loan Total Amount Loaned helps Liberty Bank assess non-performing loans, understand financial impact, and develop strategies to mitigate losses.
 

````sql
SELECT SUM(loan_amount) AS Bad_loan_Total_Amount_Loaned
	FROM [dbo].[bank_loan]
	WHERE loan_status NOT IN ('Fully Paid','Current');


````

**Answer** 

The total amount loaned to bad loan applicants is $ 65,532,225.


- **Bad loan Total Amount Received**

**Objective:** Tracking the Bad Loan Total Amount Received helps Liberty Bank assess the effectiveness of recovery strategies and minimize losses.
 

````sql
SELECT SUM(total_payment) AS Bad_loan_Total_payment
	FROM [dbo].[bank_loan]
	WHERE loan_status NOT IN ('Fully Paid','Current');

````

**Answer** 

The total amount received from bad loan applicants is $ 37,284,763.


- **Bad loan Average Interest Rate**

**Objective:** Tracking the Bad Loan Average Interest Rate helps Liberty Bank understand and mitigate high-risk lending costs
 

````sql
SELECT Avg(int_rate)*100 AS Bad_loan_Average_interest
	FROM [dbo].[bank_loan]
	WHERE loan_status NOT IN ('Fully Paid','Current');

````

**Answer** 

The average interest rate for bad loan applicants is 13.88%.


- **Bad loan Average Debt to Income Rate**

**Objective:** Tracking the Bad Loan Average Debt-to-Income (DTI) Rate helps Liberty Bank manage risk by assessing high-risk borrowers' financial stability.
 

````sql
SELECT Avg(dti)*100 AS Bad_loan_Average_dti
	FROM [dbo].[bank_loan]
	WHERE loan_status NOT IN ('Fully Paid','Current');

````

**Answer** 

The average Debt-to-Income rate for bad loan applicants is 14.00%


## Loan Status

**Objective:** Tracking loan status helps Liberty Bank monitor the current state of each loan, whether it's active, paid off, delinquent, or in default. This allows the bank to make informed decisions, manage risk, allocate resources effectively, and maintain a healthy loan portfolio by identifying and addressing potential issues early.

````sql
SELECT
        loan_status,
        COUNT(id) AS LoanCount,
        SUM(total_payment) AS Total_Amount_Received,
        SUM(loan_amount) AS Total_Funded_Amount,
        AVG(int_rate * 100) AS Interest_Rate,
        AVG(dti * 100) AS DTI
    FROM
        [dbo].[bank_loan]
    GROUP BY
        loan_status

````

## State

**Objective:** Tracking the state is to categorize and analyze loans based on their geographic location. This helps Liberty Bank understand regional trends, tailor lending strategies to specific areas, and address localized risks or opportunities.

````sql
SELECT 
	address_state AS AD_STATE, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM [dbo].[bank_loan]
GROUP BY address_state
ORDER BY Total_Loan_Applications DESC

````


**Answer**

California has the highest total loan applications at 6,894, nearly double that of the second-highest state, New York, which has 3,701 applications. California also tops the charts with the highest total money loaned, amounting to 78,484,125, followed by New York at 42,077,050. Furthermore, California received the highest total amount at 83,901,234, with New York in second place at 46,108,181. On the other end of the spectrum, Maine has the lowest loan applications at 3, with a total money loaned of 9,200 and a total cash received of 10,808.



## Term

**Objective:** Tracking the loan term helps Liberty Bank understand the duration over which different loans are repaid. This enables the bank to manage cash flows, assess the risk associated with different loan durations, and tailor lending products to meet the needs of various customer segments.

````sql

SELECT 
	term AS Term, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM [dbo].[bank_loan]
GROUP BY term
ORDER BY Total_Loan_Applications DESC
````

**Answer**

There are two loan terms: 36 months and 60 months. The 36-month term had a total of 28,237 loan applications, while the 60-month term had 10,339 applications. In terms of total money loaned, the 36-month term accounted for 273,041,225, whereas the 60-month term amounted to 162,715,850. Regarding total money received, the 36-month term garnered 294,709,458, while the 60-month term received 178,361,475



## Purpose

**Objective:** Tracking the loan purpose helps Liberty Bank understand why borrowers are taking out loans. This insight allows the bank to tailor products to meet specific needs, evaluate the risk associated with different loan purposes, and develop targeted marketing and lending strategies.

````sql

SELECT 
	purpose AS purpose, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM [dbo].[bank_loan]
GROUP BY purpose
ORDER BY Total_Loan_Applications  DESC

````

**Answer**
The primary purpose for loan collection is debt consolidation, which accounts for the highest number of loan applicants at 18,214. The total money loaned for this purpose stands at 232,459,675, with a total of 253,801,871 received. The second most common loan purpose is credit card refinancing, with 4,998 applicants, 58,885,175 in total money loaned, and 65,214,084 in total money received. In contrast, renewable energy is the least common loan purpose, with only 94 applicants, 845,750 in total money loaned, and 898,931 in total money received.



## Visualization









## Recommendations


Given the data, here are some recommendations for Liberty Bank to improve its loan portfolio management:

1. Enhance Risk Assessment: Given the high average interest rates and debt-to-income ratios in bad loans, strengthening the risk assessment process could help in identifying potential defaults earlier.

2. Adjust Lending Criteria: Reevaluate and possibly tighten lending criteria for high-risk applicants to reduce the number of non-performing loans.

3. Focus on Debt Consolidation: Since debt consolidation is the most common loan purpose, consider offering more tailored and competitive products in this category to attract quality borrowers.

4. Regional Strategies: With California and New York leading in both loan applications and total money, it's clear these regions are crucial. Implement specific marketing and risk management strategies for these high-performing states.

5. Promote Renewable Energy Loans: Given that renewable energy loans are the least common, creating incentives or awareness campaigns could boost this segment and diversify the loan portfolio.

6. Optimize Loan Terms: Analyze the performance of the different loan terms (36 months vs. 60 months) to understand borrower preferences and risk profiles better. This could help in designing more attractive loan products.

7. Improve Recovery Strategies: Given the disparity between total money loaned and received in bad loans, enhance strategies for recovering bad debt to mitigate losses.

8. By implementing these strategies, Liberty Bank can not only minimize risks but also maximize opportunities within its loan portfolio


## Limitations

A few limitations to consider:

1. Data Quality: Inconsistent or inaccurate data can skew analysis, leading to misguided decisions. Ensuring data integrity is crucial.

2. Economic Factors: External economic conditions can impact loan performance, making it harder to predict and manage risk accurately.

3. Regulatory Changes: Changes in banking regulations or policies can affect loan practices and require adjustments to strategies.

4. Borrower Behavior: Unpredictable changes in borrower behavior can impact loan repayment, complicating risk assessment and mitigation efforts.

5. Technological Limitations: Reliance on technology for data analysis and risk assessment can be limited by the accuracy and capabilities of the tools and systems used.

By recognizing these limitations, Liberty Bank can take proactive measures to mitigate risks and improve its loan portfolio management.


## References


- Abadie, Alberto, and Guido W Imbens. 2016. "Matching on the Estimated Propensity Score." Econometrica 84 (2): 781-807.

- Allison, Paul D, and Richard P Waterman. 2002. "Fixed-Effects Negative Binomial Regression Models." Sociological Methodology 32 (1): 247-65.

- Anderson, J. R. 1990. The Adaptive Character of Thought. Lawrence Erlbaum.

- Bender, E. M., Gebru, T., McMillan-Major, A., \& Shmitchell, S. 2021. "On the Dangers of Stochastic Parrots: Can Language Models Be Too Big?" Proceedings of the 2021 ACM Conference on Fairness, Accountability, and Transparency, 610-623.

- Box, G. E. P. 1976. "Science and Statistics." In Robustness in Statistics, Elsevier.

- James, Gareth, Daniela Witten, Trevor Hastie, and Robert Tibshirani. 2013. An Introduction to Statistical Learning. Springer.

- Kluyver, Thomas, Benjamin Ragan-Kelley, Fernando Pérez, Brian Granger, Matthias Bussonnier, Jonathan Frederic, Kyle Kelley, et al. 2016. "Jupyter Notebooks: A Publishing Format for Reproducible Computational Workflows." In Positioning and Power in Academic Publishing: Players, Agents and Agendas: Proceedings of the 20th International Conference on Electronic Publishing, Vol. 87, Amsterdam: IOS Press.

- Leek, Jeffrey, and Roger Peng. 2015. "What Is the Question?" Science 347 (6228): 1314-15.

