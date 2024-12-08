# Walmart Sales Analysis

## Table of Contents
- [Project Overview](#project-overview)
- [Business Challenges](#business-challenges)
- [Project Objectives](#project-objectives)
- [Data Schema](#data-schema)
- [Insights Deep-Dive](#insights-deep-dive)
    - [Branch Performance](#branch-performance)
    - [Product Line Success](#product-line-success)
    - [Customer Trends](#customer-trends)
    - [Sales Strategies](#sales-strategies)
- [Key Insights](#key-insights)
- [Recommendations](#recommendations)

---

## Project Overview  
As a data analyst at Walmart, I led a comprehensive sales forecasting initiative designed to enhance predictive analytics across our retail operations. The project leveraged historical sales data from 45 stores, focusing on understanding and modeling complex sales dynamics.

---

## Business Challenges  
1. **Impact of Holiday Markdowns**:  
   Walmart’s promotional markdowns tied to key holidays significantly affect sales. These weeks are weighted five times higher in evaluation metrics, making it critical to accurately model their effects.

2. **Limited Historical Data**:  
   The analysis of markdowns and their impact during holiday weeks is challenging due to incomplete or limited historical data.

3. **Seasonal Variability**:  
   Fluctuating sales during holiday seasons require effective trend analysis to anticipate changes in sales patterns and customer behavior.

---

## Project Objectives  
The primary objectives of this project are to:  
- Identify underperforming stores and provide recommendations for improvement.  
- Highlight high-performing product categories and explore underperforming ones for optimization.  
- Analyze customer behavior patterns, including purchase timing, payment methods, and seasonal preferences.  
- Evaluate the effectiveness of holiday markdowns in boosting sales and their impact on department-wide performance.  
- Provide forecasting models for future sales predictions based on historical data.

---

## Data Schema  

### **Database**: `walmartSales`  
### **Table**: `sales`

| Column Name          | Data Type        | Description                                           |  
|----------------------|------------------|-------------------------------------------------------|  
| `invoice_id`         | `VARCHAR(30)`    | Unique identifier for each transaction.              |  
| `branch`             | `VARCHAR(5)`     | Store branch code.                                    |  
| `city`               | `VARCHAR(30)`    | City where the store is located.                     |  
| `customer_type`      | `VARCHAR(30)`    | "Member" or "Normal" customer type.                  |  
| `gender`             | `VARCHAR(30)`    | Customer gender.                                      |  
| `product_line`       | `VARCHAR(100)`   | Category of the purchased product.                   |  
| `unit_price`         | `DECIMAL(10,2)`  | Price per unit of the product.                       |  
| `quantity`           | `INT`            | Quantity of products purchased.                      |  
| `VAT`                | `FLOAT(6,4)`     | Value-added tax applied to the transaction.          |  
| `total`              | `DECIMAL(12,4)`  | Total cost including VAT.                            |  
| `date`               | `DATETIME`       | Transaction date.                                    |  
| `time`               | `TIME`           | Transaction time.                                    |  
| `payment`            | `VARCHAR(15)`    | Payment method (e.g., Cash, Credit Card, E-Wallet).  |  
| `cogs`               | `DECIMAL(10,2)`  | Cost of goods sold.                                  |  
| `gross_margin_pct`   | `FLOAT(11,9)`    | Gross margin percentage.                             |  
| `gross_income`       | `DECIMAL(12,4)`  | Gross income for the transaction.                    |  
| `rating`             | `FLOAT(2,1)`     | Customer rating for the product or service.          |  
---

## Insights Deep-Dive  
### Branch Performance  
```sql
-- Identify underperforming branches
SELECT 
    branch,
    city,
    SUM(total) AS total_revenue,
    COUNT(invoice_id) AS total_transactions,
    AVG(rating) AS avg_rating
FROM sales
GROUP BY branch, city
ORDER BY total_revenue ASC;
```
### Product Line Success
```sql
-- Best-selling product lines
SELECT 
    product_line,
    SUM(quantity) AS total_quantity,
    SUM(total) AS total_revenue
FROM sales
GROUP BY product_line
ORDER BY total_quantity DESC;

-- Product lines by VAT (Tax Percentage)
SELECT 
    product_line,
    AVG(tax_pct) AS avg_tax
FROM sales
GROUP BY product_line
ORDER BY avg_tax DESC;

-- Evaluate product lines as "Good" or "Bad"
WITH ProductLineAverage AS (
    SELECT 
        AVG(total) AS avg_total
    FROM sales
)
SELECT 
    product_line,
    AVG(total) AS product_avg,
    CASE 
        WHEN AVG(total) > (SELECT avg_total FROM ProductLineAverage) THEN 'Good'
        ELSE 'Bad'
    END AS performance
FROM sales
GROUP BY product_line
ORDER BY product_avg DESC;
```
### Customer Trends
```sql
-- Gender and product preferences
SELECT 
    gender,
    product_line,
    COUNT(*) AS purchase_count
FROM sales
GROUP BY gender, product_line
ORDER BY purchase_count DESC;

-- Customer type revenue contribution
SELECT 
    customer_type,
    SUM(total) AS total_revenue,
    COUNT(invoice_id) AS total_purchases
FROM sales
GROUP BY customer_type
ORDER BY total_revenue DESC;

-- Gender distribution per branch
SELECT 
    branch,
    gender,
    COUNT(*) AS gender_count
FROM sales
GROUP BY branch, gender
ORDER BY branch, gender_count DESC;
```
### Sales Strategies
```sql
-- Revenue by month
SELECT 
    month_name,
    SUM(total) AS total_revenue
FROM sales
GROUP BY month_name
ORDER BY FIELD(month_name, 'January', 'February', 'March');

-- Effectiveness of holiday promotions
SELECT 
    IsHoliday,
    SUM(total) AS holiday_revenue,
    AVG(total) AS avg_holiday_sales
FROM sales
GROUP BY IsHoliday
ORDER BY IsHoliday DESC;

-- Sales by time of day
SELECT 
    time_of_day,
    COUNT(*) AS total_sales,
    AVG(rating) AS avg_rating
FROM sales
GROUP BY time_of_day
ORDER BY total_sales DESC, avg_rating DESC;
```
## Key Insights

### Branch Performance  
- **Top Branch**: Naypyitaw (Branch C) leads with the highest revenue at **$110,490.78**.  
- **Underperforming Branches**: **Yangon (Branch A)** and **Mandalay (Branch B)** are underperforming, showing lower revenue.  

### Product Line Success  
- **Top Product Lines**: **Fashion Accessories** and **Food & Beverages** dominate both sales volume and revenue.  
- **Underperforming Product Line**: **Health & Beauty** shows consistently lower sales compared to other product categories.

### Customer Trends  
- **Revenue Contribution**: **Members** generate more revenue (**$163,625.10**) than **Normal** customers.  
- **Gender Preferences**: **Females** are more likely to purchase **Fashion Accessories**, while **Males** dominate purchases in **Health & Beauty**.

### Sales Strategies  
- **Seasonal Promotions**: **January** recorded the highest revenue at **$116,291.87**.  
- **Optimal Timing**: **Evening sales** consistently outperform morning and afternoon sales.

---

## Recommendations

### 1. **Maximize Product Offerings**  
- Expand inventory for **Fashion Accessories** and **Food & Beverages** to meet the growing demand.  
- Reevaluate **Health & Beauty** product lines for repositioning or bundling strategies to increase sales.

### 2. **Optimize Branch Strategies**  
- Focus marketing and promotional efforts on improving the performance of **Yangon (Branch A)** and **Mandalay (Branch B)**.  
- Leverage **evening sales** by offering targeted promotions during peak hours.

### 3. **Enhance Customer Engagement**  
- Implement **gender-targeted promotions** that align with customer preferences, such as **Fashion Accessories** for **Females** and **Health & Beauty** for **Males**.  
- Strengthen **loyalty programs** for **Member** customers to encourage repeat purchases and increase overall revenue.
