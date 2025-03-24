# Bank Loan Analysis & Dashboard (SQL + Tableau)

## Project Overview

**Project Title**: Bank Loan Analysis

## Objectives

1. **Data Cleaning & Preparation**: Verifyed data quality and completeness using PostgreSQL.
2. **Loan Analysis**: Validated loan performance KPIs (e.g., good loan & bad loan, average interest rate, DTI ratio, total funded amount).
3. **Tableau Dashboard Development**: Built interactive dashboards to visualize important KPIs.

### Create table

```sql
CREATE TABLE loan(id int primary key, address_state varchar, application_type varchar, emp_length varchar, 
		emp_title varchar, grade varchar, home_ownership varchar, issue_date date, last_credit_pull_date date, 
		last_payment_datedate, loan_status varchar, next_payment_date date, member_id int, purpose varchar, 
		sub_grade varchar, term varchar, verification_status varchar, annual_income float, dti float, installment float, 
		int_rate float, loan_amount int, total_acc int, total_payment int);
```

### Change date format to match
```sql
SET datestyle = 'DMY';
```

### Calculate Total Loan Applications
```sql
SELECT COUNT(id) AS Total_Loan_Applications FROM loan;
```

SELECT COUNT(id) AS MTD_Total_Loan_applications FROM loan
WHERE issue_date >= '2021-11-01' AND issue_date < '2021-12-01';

### Amount Funded
```sql
SELECT SUM(loan_amount) AS MTD_Total_Funded_Amount FROM loan
WHERE issue_date >= '2021-12-01' AND issue_date < '2021-12-31';
```

### Amount Received
1. Month to Date (MTD) Amount Received
```sql
SELECT SUM(total_payment) AS MTD_Total_Amount_Received FROM loan
WHERE issue_date >= '2021-12-01' AND issue_date < '2021-12-31';
```
2. Pre Month to Date (PMTD) Amount Received
```sql
SELECT SUM(total_payment) AS PMTD_Total_Amount_Received FROM loan
WHERE issue_date >= '2021-11-01' AND issue_date < '2021-12-01';
```

### Average Interest Rate
```sql
SELECT ROUND(AVG(int_rate::NUMERIC)*100, 2) AS MTD_Average_Interest_Rate FROM loan
WHERE issue_date >= '2021-11-01' AND issue_date < '2021-12-01';
```

### Average Debt to Income Ratio
```sql
SELECT ROUND(AVG(dti)::NUMERIC * 100, 2) AS MTD_Avg_Debt_To_Income FROM loan
WHERE issue_date >= '2021-12-01' AND issue_date < '2021-12-31';
```

### Good Loan KPIs
1) Good Loan Application Percentage
```sql
SELECT (COUNT(CASE WHEN loan_status = 'Fully Paid' or loan_status = 'Current' THEN id END)) * 100 /
		COUNT(id) AS Good_Loan_Percentage FROM loan;
```
2) Good Loan Application
```sql
SELECT COUNT(id) AS Good_Loan_Applications from loan WHERE loan_status = 'Fully Paid' or loan_status = 'Current';
```
3) Good Loan Funded Amount
```sql
SELECT SUM(loan_amount) AS Good_Loan_Funded_Amount FROM loan WHERE loan_status = 'Fully Paid' OR loan_status = 'Current';
```
4) Good Loan Total Received Amount
```sql
SELECT SUM(total_payment) AS Good_Loan_Received_Amount FROM loan WHERE loan_status = 'Fully Paid' OR loan_status = 'Current';
```

### Bad Loan KPIs
1) Bad Loan Percentage
```sql
SELECT (COUNT(CASE WHEN loan_status = 'Charged Off' THEN id END)) * 100 /
		count(id) AS Bad_Loan_Percentage FROM loan;
```
2) Bad Loan Applications
```sql
SELECT COUNT(id) AS Bad_Loan_Applications FROM loan WHERE loan_status = 'Charged Off';
```
3) Bad Loan Funded Amount
```sql
SELECT SUM(loan_amount) AS Bad_Loan_Funded_Amount FROM loan WHERE loan_status = 'Charged Off';
```
4) Bad Loan Amount Received
```sql
SELECT SUM(total_payment) AS Bad_Loan_Funded_Amount FROM loan WHERE loan_status = 'Charged Off';
```

### KPIs Summary by Month
```sql
SELECT
		EXTRACT (MONTH FROM issue_date) AS Month_Number,
		TO_CHAR(issue_date, 'Month') AS Month_Name,
		COUNT(id) AS Total_Loan_Applications,
		SUM(loan_amount) AS Total_Funded_Amount,
		SUM(total_payment) AS Total_Received_Amount
FROM loan
GROUP BY Month_Number, Month_Name
ORDER BY Month_Number;
```

### KPIs Summary by Region
```sql
SELECT
		address_state AS Region,
		COUNT(id) AS Total_Loan_Applications,
		SUM(loan_amount) AS Total_Funded_Amount,
		SUM(total_Payment) AS Total_Received_Amount
FROM loan
GROUP BY Region
ORDER BY Total_Funded_Amount DESC;
```

### KPIs Summary by Loan Term
```sql
SELECT
		term AS Loan_Term,
		COUNT(id) AS Total_Loan_Applications,
		SUM(loan_amount) AS Total_Funded_Amount,
		SUM(total_Payment) AS Total_Received_Amount
FROM loan
GROUP BY Loan_Term
ORDER BY Total_Funded_Amount DESC;
```

### KPIs Summary by Employees 
```sql
SELECT
		emp_length,
		COUNT(id) AS Total_Loan_Applications,
		SUM(loan_amount) AS Total_Funded_Amount,
		SUM(total_Payment) AS Total_Received_Amount
FROM loan
GROUP BY emp_length
ORDER BY emp_length;
```

### KPIs Summary by Loan Purpose 
```sql
SELECT
		purpose,
		COUNT(id) AS Total_Loan_Applications,
		SUM(loan_amount) AS Total_Funded_Amount,
		SUM(total_Payment) AS Total_Received_Amount
FROM loan
GROUP BY purpose
ORDER BY Total_Loan_Applications DESC;
```

### KPIs Summary by Home Ownership
```sql
SELECT
		home_ownership,
		COUNT(id) AS Total_Loan_Applications,
		SUM(loan_amount) AS Total_Funded_Amount,
		SUM(total_Payment) AS Total_Received_Amount
FROM loan
GROUP BY home_ownership
ORDER BY Total_Loan_Applications DESC;
```

### Note: Project inspired by publicly available Bank Loan datasets and self-driven learning resources.
