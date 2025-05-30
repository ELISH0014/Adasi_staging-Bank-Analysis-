### Customer Transaction & Account Behaviour Analysis( Adasi_staging-Bank-Analysis)

### Project Overview 
This project is a practical SQL-based analysis focused on understanding customer transaction behavior, identifying inactive accounts, and estimating customer value.
The goal is to support data-driven decision-making for finance, operations, and marketing teams.

This analysis is based on simulated bank data and includes solving multiple real-world business questions. It demonstrates how to use SQL to extract insights from transactional and user data. The project covers:

1. Transaction Frequency Analysis
	•	Goal: Categorize customers based on how frequently they transact (monthly average).
	•	Frequency Categories:
	•	High Frequency: ≥10 transactions/month
	•	Medium Frequency: 3–9 transactions/month
	•	Low Frequency: ≤2 transactions/month
	•	Metrics Computed:
	•	Average transactions per customer per month
	•	Number of customers per frequency category

2. Account Inactivity Alert
	•	Goal: Identify all savings or investment accounts with no transactions in the last 365 days.
	•	Use case: Helps operations teams flag and re-engage inactive users.
	•	Output Includes:
	•	Plan ID, Owner ID, Account Type, Last Transaction Date, Days of Inactivity

3. Customer Lifetime Value (CLV) Estimation
	•	Goal: Estimate the customer lifetime value using transaction history.
	•	Insights Generated:
	•	Tenure (months since signup)
	•	Total transactions
	•	Estimated annualized CLV
	•	Ranking customers by CLV


### Tools 

- Excel & Googlesheets - Data cleaning , Automation.
   - Download-Here {https://drive.google.com/file/d/1__51EvatOK1ubG4oi0Im_VW2UWUChMHu/view?usp=drive_link}
- SQL- Data Analysis
- Tableau - Visualization

  #### Skills Demonstrated
  Data grouping, filtering, and aggregation
	•	Conditional logic using CASE WHEN
	•	Date calculations and time-based filters
	•	Customer segmentation and value prediction

##### INTRESTING CODES USED 

📁 Part of: Customer Transaction & Behavior Analysis Project
📅 Date: [24/05/2025]
👨‍💻 Author: ELIJAH UDONSAH
### CODES (SQL)
```sql	
[ SELECT 
    u.id AS owner_id,
    CONCAT(u.first_name, ' ', u.last_name) AS name,
    COUNT(CASE WHEN p.is_regular_savings = 1 AND s.amount > 0 THEN s.id END) AS savings_count,
    COUNT(CASE WHEN p.is_a_fund = 1 AND s.amount > 0 THEN s.id END) AS mutual_fund_count,
    SUM(CASE WHEN s.amount > 0 THEN s.amount + p.amount ELSE 0 END) AS total_deposit
FROM 
    adashi_staging.users_customuser u
JOIN 
    adashi_staging.savings_savingsaccount s ON u.id = s.owner_id
JOIN 
    adashi_staging.plans_plan p ON s.plan_id = p.id

GROUP BY 
    u.id, u.first_name, u.last_name

HAVING 
    COUNT(CASE WHEN p.is_regular_savings = 1 AND s.amount > 0 THEN s.id END) > 0
    AND COUNT(CASE WHEN p.is_a_fund = 1 AND s.amount > 0 THEN s.id END) > 0
ORDER BY 
    total_deposit DESC;]
    
    
#### Description:
This query identifies high-value customers who have both a regular savings plan and a mutual fund (investment) plan, with at least one funded account in each category. For each qualified user, it returns the number of regular savings accounts, the number of mutual fund accounts, and the total amount deposited across all their plans. This insight helps the business understand customer engagement across multiple financial products and supports targeted cross-selling strategies.

 Tables Used:
- [adashi_staging.users_customuser]
- [adashi_staging.savings_savingsaccount]
-  [ adashi_staging.plans_plan ]




 Output:
[Each row represents a customer who has at least one funded regular savings plan and one funded mutual fund plan.
The total_deposits field reflects the combined sum of all their deposits from both plan types.]



