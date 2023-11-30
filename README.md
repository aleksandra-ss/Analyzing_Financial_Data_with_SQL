## Financial Analysis in SQL

**Author:** Aleksandra Solak <br />
**Email:**  aleksandrasolak@yahoo.com <br />
**LinkedIn:** linkedin.com/in/aleksandra-solak <br />

This project relies on meticulous analysis to validate key financial metrics, answer pertinent business questions, and enable informed decision-making.


### Structure of project
 
**1. Relational Database Design:** <br />
Crafted a relational database, interconnecting tables to form the backbone of financial exploration. <br />

**2. Data Cleaning:** <br />
Implemented data cleaning procedures to ensure data integrity and set the stage for financial analysis. <br />

**3. Exploratory Data Analysis:** <br />
Achieved a comprehensive understanding of financial dynamics by performing detailed financial analyses, answering critical business questions, and extracting actionable insights and providing recommendations.


### Exploratory Data Analysis


   **1. What are the total expenses and the status of transactions for each client on each transaction date?**
   <details><summary>Click to expand a query </summary> <br />
    
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

   </details>
   <details><summary>Click to expand an output </summary> <br />

|transaction_date|client_id|total_expenses|status_of_transaction|
|----------------|---------|--------------|---------------------|
|...|...|...|...|
|2023-07-31|9|854.00|Approved|
|2023-07-31|5|1055.00|Approved,Pending|
|2023-07-30|1|267.00|Pending|
|...|...|...|...|

   </details>
   
**Analysis of total expenses and transaction statuses across clients and transaction dates offers valuable insights for strategic decision-making:**
- Client 5 consistently incurs higher expenses, warranting a closer examination of their financial activities. This could inform targeted engagement strategies or personalized services. <br />
- The majority of transactions are approved, but there's a notable cluster of pending transactions on July 31st. Investigating the reasons behind this concentration could optimize the approval process and minimize delays.  <br />
- Dates with unusually high or low total expenses must be identified. For example, on June 5th, the total expense was higher, indicating a peak in financial activity. Understanding what causes such peaks can help with resource allocation. <br />

    
   - What is the total expense and running total of transaction amounts for each company within the date range of July 1, 2023, to July 31, 2023?
   - Which project has the highest and lowest expenses?
   - What is the revenue status of clients based on their total revenue for the month of July?
   - What is the monthly revenue and percentage of revenue for each project, broken down by month?
   - What is the daily difference in total revenue for each date in the sales transactions data?
   - Financial summary for each client, including total expenses, total revenue, profit, and profit margin, with a breakdown by company.








