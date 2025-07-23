# SQL - Ticket-Above-Recurring-Average
Know which customers frequently place orders above the average ticket for VIP campaigns

WITH cte_sales AS (
    SELECT 
        DC.FirstName + ' ' + DC.LastName AS FullName,
        F.SalesAmount,
        DC.EmailAddress
    FROM FactInternetSales F
    JOIN DimCustomer DC ON F.CustomerKey = DC.CustomerKey
    WHERE SalesAmount > (SELECT AVG(SalesAmount) FROM FactInternetSales) 
),
cte_rank AS (
    SELECT
        FullName,
        EmailAddress,
        COUNT(*) AS qtd_over_Avg,
        SUM(SalesAmount) AS sum_orders,
        ROW_NUMBER() OVER (ORDER BY COUNT(*) DESC) AS rn
    FROM cte_sales
    GROUP BY FullName, EmailAddress
)
SELECT *
FROM cte_rank
WHERE rn <= 10
ORDER BY qtd_over_Avg DESC;
