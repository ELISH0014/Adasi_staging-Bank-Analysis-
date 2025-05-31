### Customer Transaction & Account Behaviour Analysis (Adasi_staging-Bank-Analysis)

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
    total_deposit DESC;


2.  WITH transactions_per_month AS
 (
    SELECT 
        s.owner_id,
        DATE_FORMAT(s.transaction_date, '%Y-%m') AS month,
        COUNT(s.savings_id) AS monthly_transactions
    FROM 
        adashi_staging.savings_savingsaccount s
    WHERE s.transaction_date IS NOT NULL
    GROUP BY 
        s.owner_id, DATE_FORMAT(s.transaction_date, '%Y-%m')
),

avg_transactions_per_user AS (

    SELECT 
        owner_id,
        AVG(monthly_transactions) AS avg_per_month
    FROM 
        transactions_per_month
    GROUP BY 
        owner_id
),

categorized_users AS (
    
    SELECT 
        atpu.owner_id,
        CASE 
            WHEN atpu.avg_per_month > 10 THEN 'High'
            WHEN atpu.avg_per_month BETWEEN 3 AND 9 THEN 'Medium'
            ELSE 'Low'
        END AS frequency_category,
        atpu.avg_per_month
    FROM 
        avg_transactions_per_user atpu
)


SELECT 
    frequency_category,
    COUNT(owner_id) AS customer_count,
    ROUND(AVG(avg_per_month), 2) AS avg_transaction_per_month
FROM 
    categorized_users
GROUP BY 
    frequency_category
ORDER BY 
    FIELD(frequency_category, 'High', 'Medium', 'Low');]




3. [SELECT 
    p.id AS plan_id,
    s.owner_id,
    CASE 
        WHEN p.is_regular_savings = 1 THEN 'Savings'
        WHEN p.is_a_fund = 1 THEN 'Investment'
        ELSE 'Other'
    END AS type,
    MAX(s.transaction_date) AS last_transaction_date,
    DATEDIFF('2025-05-19', MAX(s.transaction_date)) AS inactivity_days
FROM 
    adashi_staging.savings_savingsaccount s
JOIN 
    adashi_staging.plans_plan p ON s.plan_id = p.id
WHERE 
    s.transaction_date IS NOT NULL
GROUP BY 
    p.id, s.owner_id, type
HAVING 
    -- No inflow (confirmed_amount > 0) in the last 1 year
    SUM(CASE 
            WHEN s.transaction_date BETWEEN '2024-05-19' AND '2025-05-19'
                 AND s.confirmed_amount > 0 
            THEN 1 
            ELSE 0 
        END) = 0
ORDER BY 
    inactivity_days DESC;]
    
    
#### Description
1. This query identifies high-value customers who have both a regular savings plan and a mutual fund (investment) plan, with at least one funded account in each category. For each qualified user, it returns the number of regular savings accounts, the number of mutual fund accounts, and the total amount deposited across all their plans. This insight helps the business understand customer engagement across multiple financial products and supports targeted cross-selling strategies.

2. This query calculates the average number of monthly savings transactions for each user and categorizes them into High, Medium, or Low based on their activity. It then returns the number of users in each category and their average transaction rate per month.

3. This query identifies inactive customer accounts by checking for any savings or investment transactions (with confirmed amounts) in the past year. It groups records by plan and account owner, classifies the plan type (Savings, Investment, or Other), and calculates the number of days since the last transaction. Only accounts with no confirmed inflow in the last year are included.



 Tables Used:
- [adashi_staging.users_customuser]
- [adashi_staging.savings_savingsaccount]
-  [ adashi_staging.plans_plan ]





 RESULTS 

1 . **Customer Lifetime Value (CLV) Estimation Summary**

This process estimates the total predicted revenue from a customer throughout their relationship with the business. It identifies high-value users to enhance targeted marketing and retention efforts. The output includes the account owner’s unique ID, full name, number of savings and mutual fund transactions, and total deposits across all accounts.



2.** Customer Transaction Frequency Segmentation**

This analysis segments users based on the average number of savings transactions they perform monthly. By calculating transaction frequency per user and categorizing them into High, Medium, or Low activity levels, the business can identify engagement patterns. Combined with Customer Lifetime Value (CLV) insights, this helps prioritize retention and marketing strategies toward highly active and valuable customers. The output shows each frequency category, the number of users in each, and their average monthly transaction rate.


3. **Inactive Account Detection Report**
 The output highlights dormant savings and investment accounts, showing users who haven’t made any inflow transactions in over a year. This helps the business identify disengaged customers for potential re-engagement strategies or account cleanup efforts. The results are sorted by the longest inactivity period, prioritizing the most inactive users.

 Conclusion 
