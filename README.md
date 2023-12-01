## Financial Analysis in SQL

**Author:** Aleksandra Solak <br />
**Email:**  aleksandrasolak@yahoo.com <br />
**LinkedIn:** www.linkedin.com/in/aleksandra-solak <br />

### Project Overview

This project is an in-depth investigation of financial data dynamics, combining data modeling, data cleaning, and exploratory data analysis (EDA). This analysis, viewed through the lens of EDA, reveals important aspects of financial activities, revealing stories embedded in trends and patterns.


### Structure of project
 
[**1. Relational Database Design:**](create_tables.txt) <br />
Crafted a relational database, interconnecting tables to form the backbone of financial exploration. <br />

[**2. Data Cleaning:**](data_cleaning.txt) <br />
Implemented data cleaning procedures to ensure data integrity and set the stage for financial analysis. <br />

**3. Exploratory Data Analysis:** <br />
Achieved a comprehensive understanding of financial dynamics by performing detailed financial analyses, answering critical business questions, and extracting actionable insights and providing recommendations.


### Exploratory Data Analysis
EDA entailed investigating financial data to answer key questions such as:
- What are the total expenses and the status of transactions for each client on each transaction date?
- What is the total expense and running total of transaction amounts for each company within the date range of July 1, 2023, to July 31, 2023?
- Which project has the highest and lowest expenses?
- What is the revenue status of clients based on their total revenue for the month of July?
- What is the monthly revenue and percentage of revenue for each project, broken down by month?
- Financial summary for each client, including total expenses, total revenue, profit, and profit margin, with a breakdown by company.

**1. What are the total expenses and the status of transactions for each client on each transaction date?**

   ##### SQL query:
```sql
SELECT t.transaction_date,
       p.client_id,
       SUM(t.transaction_amount) AS total_expences,
       GROUP_CONCAT(DISTINCT t.status) AS status_of_transaction 
FROM transactions t
LEFT JOIN projects p ON t.project_id = p.project_id
GROUP BY p.client_id, t.transaction_date
ORDER BY t.transaction_date;
```

##### Output:

|transaction_date|client_id|total_expenses|status_of_transaction|
|----------------|---------|--------------|---------------------|
|...|...|...|...|
|2023-07-31|9|854.00|Approved|
|2023-07-31|5|1055.00|Approved,Pending|
|2023-07-30|1|267.00|Pending|
|...|...|...|...|


- Client 5 consistently has high expenses, making it a key account for revenue generation. Further analysis can explore the nature of transactions with this client for strategic planning. <br />
- The majority of transactions are approved, but there's a notable cluster of pending transactions on July 31st. Investigating the reasons behind this concentration could optimize the approval process and minimize delays. <br />

    
**2. What is the total expense and running total of transaction amounts for each company within the date range of July 1, 2023, to July 31, 2023?**

   ##### SQL query:
```sql
SELECT DISTINCT (t.transaction_date),
       c.company_name,
       transaction_amount,
       SUM(t.transaction_amount) OVER(PARTITION BY company_name ORDER BY t.transaction_date ASC
       ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS transaction_running_total
FROM transactions t
JOIN projects p ON t.project_id = p.project_id
JOIN clients c ON p.client_id = c.client_id
WHERE t.transaction_date > '2023-06-01' AND t.transaction_date <= '2023-07-31'
ORDER BY c.company_name ASC;
```

##### Output:

|transaction_date|company_name|transaction_amount|transaction_running_total|
|----------------|---------|--------------|---------------------|
|2023-06-07|Apex Ally Creative|233.00|233.00|
|2023-06-09|Apex Ally Creative|433.00|666.00|
|2023-07-02|Apex Ally Creative|336.00|1002.00|
|...|...|...|...|

- Companies such as 'Apex Ally Creative' and 'Brand Zoom' have consistent spending patterns in June and July, whereas others have irregular patterns. This knowledge can have an impact on resource allocation strategies.

    
 **3. Which project has the highest and lowest expenses?**

   ##### SQL query:
```sql
WITH expenses_per_project AS (
    SELECT project_id,
           SUM(transaction_amount) AS total_expenses
    FROM transactions
    GROUP BY project_id
)
SELECT
    project_id,
    total_expenses
FROM (
    SELECT
        project_id,
        total_expenses,
        RANK() OVER (ORDER BY total_expenses ASC) AS rank_lowest,
        RANK() OVER (ORDER BY total_expenses DESC) AS rank_highest
    FROM expenses_per_project ) ranked_expenses
WHERE rank_lowest = 1 OR rank_highest = 1;
```

##### Output:

|project_id|total_expenses|
|----------------|---------|
|1009|3553.00|
|1006|1343.00|



 **4. What is the revenue status of clients based on their total revenue for the month of July?**

   ##### SQL query:
```sql
WITH revenue_report AS (
    SELECT c.company_name,
           SUM(st.revenue) AS total_revenue,
           EXTRACT(MONTH FROM st.date) AS month
    FROM sales_transactions st
    LEFT JOIN clients c ON st.client_id = c.client_id
    WHERE EXTRACT(MONTH FROM st.date) = 7
    GROUP BY c.company_name, EXTRACT(MONTH FROM st.date)
    ORDER BY total_revenue DESC
)
SELECT 
    *,
    CASE 
        WHEN total_revenue > 5000 THEN 'High revenue' 
        ELSE 'Low revenue' 
    END AS revenue_status
FROM revenue_report;
```

##### Output:

|company_name|total_revenue|month|revenue_status|
|----------------|---------|-----|--------------|
|Wave Strategies|9800.00|7|High revenue|
|Echo Marketing|7200.00|7|High revenue|
|Insight Craft|6700.00|7|High revenue|
|...|...|...|...|

- 'Wave Strategies', 'Echo Marketing', 'Insight Craft', 'Brand Zoom', 'Pulse Advertising', and 'Peak Dynamics' exhibit high revenue, ranging from '5400.00' to '9800.00'. Conversely, 'Nova Ad Agency', 'Blend Advertising', and 'Apex Ally Creative' fall into the low revenue range, with totals from '700.00' to '3800.00'. 


  
**5. What is the monthly revenue and percentage of revenue for each project, broken down by month?**

   ##### SQL query:
```sql
WITH monthly_rev AS (
SELECT
    p.project_id,
    EXTRACT(MONTH FROM st.date) AS month,
    SUM(st.revenue) AS monthly_revenue,
    ROUND((SUM(st.revenue)*100) / SUM(SUM(st.revenue)) OVER (PARTITION BY project_id),2) AS perc
FROM sales_transactions st
LEFT JOIN projects p
    ON st.client_id = p.client_id
GROUP BY p.project_id, EXTRACT(MONTH FROM st.date)
ORDER BY p.project_id, month
)
SELECT *
FROM monthly_rev;
```

##### Output:

|project_id|month|monthly_revenue|perc|
|----------|-----|---------------|----|
|1001|6|1500.00|18.29|
|1001|7|6700.00|81.71|
|1002|6|5300.00|84.13|
|...|...|...|...|

- Projects '1001', '1002', '1003', '1005', '1006', '1007', '1008', and '1009' experienced revenue shifts from June to July. '1004' and '1010' maintained consistent revenue throughout. Projects like '1006' and '1007' displayed significant revenue growth in July
   
**6. Financial summary for each client, including total expenses, total revenue, profit, and profit margin, with a breakdown by company.**

   ##### SQL query:
```sql
WITH client_summary AS (
    SELECT c.client_id,
           t.transaction_date, 
           c.company_name,
           SUM(t.transaction_amount) AS total_expenses,
           SUM(st.revenue) AS total_revenue,
           (SUM(st.revenue)) - (SUM(t.transaction_amount)) AS profit,
           ROUND((SUM(st.revenue) - SUM(t.transaction_amount)) / SUM(st.revenue) * 100 ,2) AS profit_margin
    FROM clients c
    LEFT JOIN sales_transactions st ON c.client_id = st.client_id
    LEFT JOIN projects p ON c.client_id = p.client_id
    LEFT JOIN transactions t ON t.project_id = p.project_id
    GROUP BY c.client_id, c.company_name, t.transaction_date
) 
SELECT cs.client_id,
       cs.transaction_date,
       cs.company_name,
       cs.total_expenses,
       cs.total_revenue,
       cs.profit, 
       cs.profit_margin
FROM client_summary cs
LEFT JOIN projects p ON p.client_id = cs.client_id
LEFT JOIN transactions t ON t.project_id = p.project_id
GROUP BY cs.client_id, cs.company_name, cs.transaction_date
ORDER BY cs.client_id;
```

##### Output:

|client_id|transaction_date|company_name|total_expenses|total_revenue|profit|profit_margin|
|----------|---------------|------------|--------------|-------------|------|-------------|
|1|2023-06-09|Wave Strategies|2070.00|18900.00|16830.00|89.05|
|1|2023-06-15|Wave Strategies|2388.00|18900.00|16512.00|87.37|
|1|2023-06-18|Wave Strategies|2016.00|18900.00|16884.00|89.33|
|1|2023-06-30|Wave Strategies|3384.00|18900.00|15516.00|82.10|
|1|2023-07-30|Wave Strategies|1602.00|18900.00|17298.00|91.52|
|2|2023-06-01|Insight Craft|1192.00|8200.00|7008.00|85.46|
|2|2023-06-05|Insight Craft|2384.00|16400.00|140160.00|85.46|
|2|2023-06-08|Insight Craft|1512.00|8200.00|6688.00|81.56|
|...|...|...|...|

##### This analysis offers a concise overview of the financial performance for each company across June and July:
- Wave Strategies showcased continual revenue growth, maintaining high profit margins.
- Other companies, such as Insight Craft and Nova Ad Agency, demonstrated stability, while Blend Advertising experienced fluctuations.
- Brands like Brand Zoom consistently maintained a profit margin above 90%, reflecting robust financial performance.









