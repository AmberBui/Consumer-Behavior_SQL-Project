# Customer Personality Analysis using SQL

## Objectives

-	Customer Segmentation: Classify customers into meaningful groups based on their demographics and behaviors.

-	Spending Patterns: Analyze how different customer segments spend across various product categories.

-	Trend Identification: Identify trends in purchasing behavior and responses to marketing campaigns.

-	Marketing Optimization: Use segmentation insights to improve targeting and personalization of marketing campaigns.

## Dataset

**Dataset Link**: [Customer Personality Analysis](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis/data)

**Source**: Kaggle

**Dataset** Provider: Aakash Sharma

**Date Accessed**: 07.01.2025

- Customer demographics (age, education, income, etc.)

- Purchase history (amount spent on different product categories)

- Marketing campaign responses

- Customer interactions with the company (web, store, catalog purchases, complaints, etc.)


## 1. Data Cleaning

 **Objective:** Identify missing values.
```sql
   FROM customers
   WHERE Income is null 
        OR year_birth is null
```	

A total of 24 rows lack an income value. These rows will be eliminated from the dataset.
 ```sql
     DELETE customers
     WHERE Income is null
```


 **Objective:** Find and remove duplicates 
``` sql
    SELECT ID, COUNT(*)
    FROM Customers
    GROUP BY ID
    HAVING COUNT(*) > 1
```
## 2.Data Analysis 

### A. Customer Segmentation & Spending Behavior 

```sql
SELECT Year_Birth
FROM Customers
ORDER BY 1 DESC
```
The oldest birth year is 1893, and the youngest is 1996, with the analysis year being 2025, indicating that the data is outdated.


**Objective:** Segment by age group, income, and analyze purchasing behaviors.

```sql
WITH CustomerSegments AS (
    SELECT 
        ID,
        CASE
            WHEN Year_Birth > 1980 THEN 'Millennials'
            WHEN Year_Birth BETWEEN 1965 AND 1980 THEN 'Gen X'
            ELSE 'Baby Boomers' 
        END AS Age_Group,
        CASE 
            WHEN Income < 30000 THEN 'Low Income'
            WHEN Income BETWEEN 30000 AND 70000 THEN 'Middle Income'
            ELSE 'High Income' 
        END AS Income_Group
    FROM customers
)
SELECT 
    Category,
    Age_Group,
    Income_Group,
    SUM(Spending) AS TotalSpending
FROM (
    SELECT 
        'Wines' AS Category, 
        C.ID, 
        CS.Age_Group, 
        CS.Income_Group, 
        C.MntWines AS Spending
    FROM customers C
    JOIN CustomerSegments CS ON C.ID = CS.ID
    UNION ALL
    SELECT 
        'Fruits', 
        C.ID, 
        CS.Age_Group, 
        CS.Income_Group, 
        C.MntFruits
    FROM customers C
    JOIN CustomerSegments CS ON C.ID = CS.ID
    UNION ALL
    SELECT 
        'MeatProducts', 
        C.ID, 
        CS.Age_Group, 
        CS.Income_Group, 
        C.MntMeatProducts
    FROM customers C
    JOIN CustomerSegments CS ON C.ID = CS.ID
    UNION ALL
    SELECT 
        'Fish Products', 
        C.ID, 
        CS.Age_Group, 
        CS.Income_Group, 
        C.MntFishProducts
    FROM customers C
    JOIN CustomerSegments CS ON C.ID = CS.ID
    UNION ALL
    SELECT 
        'Sweet Products', 
        C.ID, 
        CS.Age_Group, 
        CS.Income_Group, 
        C.MntSweetProducts
    FROM customers C
    JOIN CustomerSegments CS ON C.ID = CS.ID
    UNION ALL
    SELECT 
        'Gold Products', 
        C.ID, 
        CS.Age_Group, 
        CS.Income_Group, 
        C.MntGoldProds
    FROM customers C
    JOIN CustomerSegments CS ON C.ID = CS.ID
) AS ProductSpending
GROUP BY Category, Age_Group, Income_Group
ORDER BY TotalSpending DESC
```

### B. Campaign Effectiveness 

**Objective:** Measure the success of different marketing campaigns.
```sql
SELECT 
    'Campaign 1' AS Campaign,
    COUNT(CASE WHEN AcceptedCmp1 = 1 THEN 1 END) AS TotalAccepted,
    COUNT(*) AS TotalCustomers,
    (COUNT(CASE WHEN AcceptedCmp1 = 1 THEN 1 END) * 100.0 / COUNT(*)) AS AcceptanceRate
FROM 
    customers
UNION ALL
SELECT 
    'Campaign 2',
    COUNT(CASE WHEN AcceptedCmp2 = 1 THEN 1 END),
    COUNT(*),
    (COUNT(CASE WHEN AcceptedCmp2 = 1 THEN 1 END) * 100.0 / COUNT(*))
FROM 
    customers
UNION ALL
SELECT 
    'Campaign 3',
    COUNT(CASE WHEN AcceptedCmp3 = 1 THEN 1 END),
    COUNT(*),
    (COUNT(CASE WHEN AcceptedCmp3 = 1 THEN 1 END) * 100.0 / COUNT(*))
FROM 
    customers
UNION ALL
SELECT 
    'Campaign 4',
    COUNT(CASE WHEN AcceptedCmp4 = 1 THEN 1 END),
    COUNT(*),
    (COUNT(CASE WHEN AcceptedCmp4 = 1 THEN 1 END) * 100.0 / COUNT(*))
FROM 
    customers
UNION ALL
SELECT 
    'Campaign 5',
    COUNT(CASE WHEN AcceptedCmp5 = 1 THEN 1 END),
    COUNT(*),
    (COUNT(CASE WHEN AcceptedCmp5 = 1 THEN 1 END) * 100.0 / COUNT(*))
FROM 
    customers
```
### C. Customer Retention Analysis

**Objective:** Investigate recency and loyalty through customers’ last purchase and campaign responses.
```sql
SELECT Recency, SUM(NumStorePurchases + NumWebPurchases + NumCatalogPurchases) AS Total_Purchases
FROM customers
GROUP BY Recency
ORDER BY Recency
```
## 3. Insights & Recommendations
**Customer Segmentation**

- High-Income Customers:
  + Spend significantly more across all categories, particularly on Wines and Meat Products.
  + Dominated by Gen X and Baby Boomers.
  
- Middle-Income Customers:
  + Moderate engagement with Wines, Meat Products, and Gold Products.
  + Gen X is the largest contributor in this group, followed by Baby Boomers.
  
- Low-Income Customers:
  + Spend far less overall, with minimal spending on high-value products like Wines or Gold Products.
  + Low spending across all product categories, with Sweet Products and Fruits being the most accessible.
  
**Spending Behavior Insights**

- Wines:
  + The highest revenue driver across all income and age groups.
  + Especially popular among Gen X and Baby Boomers.
  
- Meat Products:
  + Strong performance among high and middle-income groups, particularly Gen X.

- Fruits:
  + Low engagement among all demographics. -> Opportunity to market ready-to-eat or pre-packaged options for convenience.
   
- Sweet Products:
  + Weak performance among Millennials and low-income groups. -> Introduce healthier alternatives or smaller-sized products to boost sales.
  
- Gold Products:
  + Moderate performance but with potential to grow through targeted campaigns emphasizing luxury and exclusivity.

*Recommendations:*
  
- Strengthen customer retention and engagement in Wines and Meat Products for high-income and Gen X/Baby Boomer groups.
  
- Introduce innovative marketing strategies to improve performance in Gold Products, Fish Products, and Sweet Products.
  
- Tailor campaigns by demographic and income groups to maximize resonance and profitability.

**Marketing Campaign Insights**

- Campaign 4: Highest acceptance rate at 7.44%.
- Campaign 3: Close second with an acceptance rate of 7.39%.
- Campaign 1: Acceptance rate of 6.44%.
- Campaign 2: Very low acceptance rate of 1.36%.

*Recommendations:*
-  Leverage Successful Campaigns: Analyze the messaging, timing, and target audience of Campaigns 3 and 4. -> Replicate successful elements for future campaigns.
  
-  Review Campaign 2 to identify weaknesses in content, timing, or targeting. -> If improvements are unfeasible, consider discontinuing Campaign 2.
  
-  Personalized Campaigns: Develop tailored campaigns targeting specific demographics, like Millennials or low-income groups, focusing on value-for-money or convenience.

**Retention and Loyalty Insights**

- Correlation Between Recency and Loyalty: Customers with lower recency scores (more recent purchases) are more likely to make additional purchases.
- Customers with higher recency scores (longer time since last purchase) are at risk of churn.
  
*Recommendations:*
  
 - Use personalized emails or app notifications with special offers to re-engage these customers.
 - Offer loyalty discounts or incentives for their next purchase.
 - Introduce tiered rewards based on spending frequency and amount, encouraging repeat purchases.
 - Create a "We Miss You" campaign offering discounts or free trials to customers with the highest recency scores.

  ### 4. Conclusion

**Focus Areas:**
- Strengthen campaigns targeting Gen X and Baby Boomers for high-revenue categories like Wines and Meat Products.
- Improve the appeal of low-performing categories through innovation and tailored strategies.

**Growth Opportunities:**
- Leverage successful elements of Campaigns 3 and 4 while improving weaker campaigns.
- Engage and retain customers with high recency scores through targeted loyalty and retention strategies.

**Long-Term Strategy:**
- Tailor marketing efforts by segmenting customers based on demographics, income, and spending behavior.
- Ensure a balanced focus on retaining high-value customers and re-engaging low-spending or at-risk groups.




