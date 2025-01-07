# Customer Personality Analysis using SQL

## Objectives
•	Customer Segmentation: Classify customers into meaningful groups based on their demographics and behaviors.
•	Spending Patterns: Analyze how different customer segments spend across various product categories.
•	Trend Identification: Identify trends in purchasing behavior and responses to marketing campaigns.
•	Marketing Optimization: Use segmentation insights to improve targeting and personalization of marketing campaigns.

## Dataset
•	**Dataset Link**: [Customer Personality Analysis](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis/data)
**Source**: Kaggle
**Dataset** Provider: Aakash Sharma
**Date Accessed**: 07.01.2025
• Customer demographics (age, education, income, etc.)
• Purchase history (amount spent on different product categories)
• Marketing campaign responses
• Customer interactions with the company (web, store, catalog purchases, complaints, etc.)

## 1. Data Cleaning

 **Objective:** Identify missing values
```FROM customers
   WHERE Income is null 
      OR year_birth is null ```	


A total of 24 rows lack an income value. These rows will be eliminated from the dataset
 ``` DELETE customers
     WHERE Income is null```


 **Objective:** Find and remove duplicates 
``` SELECT ID, COUNT(*)
    FROM Customers
    GROUP BY ID
    HAVING COUNT(*) > 1```




