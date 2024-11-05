# Customer Segmentation for a Subscription Service

## Project Summary
This project involves analyzing customer data for a subscription service to identify segments, track subscription types, and assess cancellations and renewals. The final deliverable is a Power BI dashboard that highlights customer trends.

## Tools Used
- Microsoft Excel
- SQL
- Power BI

## Table of Contents
1. [Data Cleaning](#data-cleaning)
   - [Excel Data Cleaning](#excel-data-cleaning)
   - [SQL Data Cleaning](#sql-data-cleaning)
   - [Power BI Data Cleaning](#power-bi-data-cleaning)
2. [Analysis](#analysis)
   - [Excel Analysis](#excel-analysis)
   - [SQL Analysis](#sql-analysis)
   - [Power BI Dashboard](#power-bi-dashboard)
3. [Results](#results)

---

## Data Cleaning

### Excel Data Cleaning

1. **Remove Duplicates**:
   - Removed duplicates based on `Customer ID` and `Subscription Start Date`.

2. **Handle Missing Values**:
   - Filled missing values in `Revenue` with average values and replaced blanks in `Subscription Type` with "Unknown".

3. **Data Type Validation**:
   - Confirmed `Subscription Duration` as numeric and `Subscription Start Date` as a date.

### SQL Data Cleaning

1. **Schema Setup with Constraints**:
   - Set up table constraints to ensure data consistency.

2. **Handle Null Values**:
   - Replaced missing `Revenue` values with average revenue by subscription type.

3. **Remove Duplicates**:
   - Used CTE to identify and delete duplicates.

4. **Standardize Values**:
   - Standardized `SubscriptionType` values.

### Power BI Data Cleaning

1. **Inspect and Handle Missing Values**:
   - Replaced missing values using **Power Query Editor**.

2. **Standardize Values**:
   - Ensured all text data had consistent case formatting.

---

## Analysis

### Excel Analysis

1. **Pivot Tables**:

Table 1: Subscription Distribution
```excel
- Rows: Subscription_Type
- Values: 
  * Count of Customer_ID
  * Sum of Monthly_Revenue
```
Table 2: Regional Performance
```excel
- Rows: Region
- Values:
  * Count of Customer_ID
  * Average of Monthly_Revenue
  * Sum of Monthly_Revenue
```

Table 3: Cancellation Patterns
```excel
- Rows: Region
- Columns: Subscription_Type
- Values: 
  * Count of Status (filter for 'CANCELED')
  * % of Total Cancellations
```
2. **Formulas**:
   - Calculated average subscription duration: **12 months**.
 1. Total Customers
 ```excel
   =COUNTDISTINCT(Customer_ID)
```
2. Active Subscribers
 ```excel
   =COUNTIFS(Status, "ACTIVE")
```

3. Churn Rate
```excel
    =COUNTIFS(Status, "CANCELED") / COUNTDISTINCT(Customer_ID)
```

5. Average Subscription Duration
```excel
    =AVERAGE(Subscription_Duration_Months)
```

6. Total Monthly Revenue
```excel
    =SUMPRODUCT(Monthly_Revenue, --(Status="ACTIVE"))
```

A. Subscription Distribution Pie Chart
```excel  
   - Insert → Pie Chart
   - Data from Subscription Distribution pivot table
```
B. Regional Performance Bar Chart
 ``` excel
   - Insert → Column Chart
   - Data from Regional Performance pivot table
```
C. Monthly Revenue Trend Line Chart
 ```excel
   - Insert → Line Chart
   - X-axis: Months
   - Y-axis: Sum of Monthly Revenue
```
D. Cancellation Patterns Heat Map
```excel   
   - Use conditional formatting on pivot table data
```
### SQL Analysis

A. Import Data to SQL Server:

Open SQL Server Management Studio
Right-click on Databases → New Database → "CustomerSubscriptions"
Right-click on the new database → Tasks → Import Data
Choose Excel as source and follow the wizard

1. Regional Customer Count
```sql
SELECT 
    Region,
    COUNT(*) as CustomerCount
FROM CustomerSubscriptions
GROUP BY Region
ORDER BY CustomerCount DESC;
```
2. Popular Subscription Types
```sql
SELECT 
    Subscription_Type,
    COUNT(*) as SubscriberCount,
    COUNT(*) * 100.0 / (SELECT COUNT(*) FROM CustomerSubscriptions) as Percentage
FROM CustomerSubscriptions
GROUP BY Subscription_Type
ORDER BY SubscriberCount DESC;
```

 3. Early Cancellations
```sql
SELECT 
    Customer_ID,
    Subscription_Type,
    Region,
    Subscription_Duration_Months
FROM CustomerSubscriptions
WHERE Status = 'CANCELED'
    AND Subscription_Duration_Months <= 6;
```
4. Revenue Analysis
```sql
SELECT 
    Subscription_Type,
    COUNT(*) as Subscribers,
    SUM(Monthly_Revenue) as TotalMonthlyRevenue,
    AVG(Monthly_Revenue) as AverageRevenue
FROM CustomerSubscriptions
WHERE Status = 'ACTIVE'
GROUP BY Subscription_Type
ORDER BY TotalMonthlyRevenue DESC;
```

5. Regional Cancellation Rates
```sql
SELECT TOP 3
    Region,
    COUNT(*) as CancellationCount,
    COUNT(*) * 100.0 / SUM(COUNT(*)) OVER() as CancellationPercentage
FROM CustomerSubscriptions
WHERE Status = 'CANCELED'
GROUP BY Region
ORDER BY CancellationCount DESC;
```

## Power BI DashBoard

### Power BI Analysis
- Import Data:
- Open Power BI Desktop.
- Click on Get Data and select CSV or the appropriate data source to import the cleaned dataset (cleaned_customer_data.csv).
-----
- Data Preparation:
  - Once imported, go to the Transform Data section to clean and prepare your data further if needed.
  - Ensure the following columns are formatted correctly:
  - Dates (e.g., subscription_start_date, cancellation_date) should be in Date format.
  - Categorical columns (e.g., region, subscription_type) should be in Text format.

- Create Calculated Columns:
  
  - Add calculated columns to derive new insights. For example:
    
- Subscription Duration:
  
  - DAX Functions:
    
```power bi
Subscription Duration = DATEDIFF([subscription_start_date], TODAY(), MONTH)
```
Cancellation Status:
DAX Functions:
``` power bi
Cancellation Status = IF(ISBLANK([cancellation_date]), "Active", "Canceled")
```
#### Visualizations:
- Create a variety of visualizations to represent your data effectively:

#### Sales Overview:
- Use a Card visual to display total revenue.
- Create a Clustered Column Chart to show total active subscriptions vs. canceled subscriptions.

#### Top-Performing Products:
- Use a Bar Chart to show total revenue by subscription type.
- Include a Pie Chart to illustrate the proportion of customers by subscription type.

#### Regional Breakdown:
- Create a Map visualization to visualize the distribution of customers across different regions.
- Use a Stacked Column Chart to display the number of cancellations by region.

#### Slicers for Interactive Analysis:
- Add slicers for:
- Subscription Type: Allow users to filter the visualizations based on the type of subscription.
- Region: Enable filtering by geographic location.
- Cancellation Status: Provide an option to view active or canceled subscriptions.
  
#### Dashboard Layout:
- Arrange the visuals in a cohesive layout for easy readability.
- Ensure that the dashboard flows logically, with related visuals grouped together.
- Final Touches:
- Add titles, data labels, and tooltips to enhance the user experience.
- Consider using bookmarks for guiding users through key insights if desired.
- Save and Export:
- Save the Power BI file as customer_segmentation.pbix.

## Results
#### Excel Analysis
- Customer Data Patterns:

 - Pivot Tables: Identification of common subscription patterns, including the distribution of customers across different subscription types, seasonal trends in sign-ups, and cancellation rates over time.
Average Subscription Duration:

 - A calculated average subscription duration will provide insights into how long customers typically remain subscribed, indicating customer loyalty and satisfaction levels.
Most Popular Subscription Types:

 - A report detailing which subscription types have the highest number of customers, helping to identify offerings that are most attractive to your target market.
Additional Reports:

#### SQL Analysis Results
 - Customer Distribution by Region:

 - The total number of customers from each region, allowing the business to understand geographic distribution and identify potential markets for growth.
 - Popular Subscription Types:

 - Identification of the subscription type with the highest number of customers, which can inform marketing and product development strategies.
- Cancellations within 6 Months:

 - A list of customers who canceled their subscriptions within six months, which can be analyzed to understand churn reasons and improve customer retention efforts.
- Average Subscription Duration:

 - A computed average duration for all customers, offering a benchmark for assessing customer retention.
- Long-term Subscribers:

 - Identification of customers with subscriptions longer than 12 months, which can indicate brand loyalty and success in retaining valuable customers.
- Total Revenue by Subscription Type:

 - A breakdown of revenue generated by each subscription type, helping to identify the most profitable offerings.
- Regions with Highest Cancellations:

 - A report on the top three regions by subscription cancellations, highlighting areas that may need targeted retention efforts.
- Active vs. Canceled Subscriptions:

 - A total count of active and canceled subscriptions, providing a clear picture of overall business health and customer retention.
   
#### Power BI Dashboard Analysis Results
 - Visualizations of Customer Segments:

 - A dashboard displaying various customer segments based on demographic and subscription data, facilitating targeted marketing efforts.
- Cancellation Trends:

 - Interactive visualizations showing trends in subscription cancellations over time, which can help identify critical periods for customer retention strategies.
- Subscription Type Trends:

 - Insights into how different subscription types perform over time, including trends in renewals and cancellations, allowing for data-driven decision-making in subscription management.
- Interactive Analysis:
   - The inclusion of slicers for users to filter data dynamically, enabling deeper dives into specific segments or time frames for more granular insights.
![Screenshot (70)](https://github.com/user-attachments/assets/d8dc9464-800b-4963-88e8-83f01a33003a)
![Screenshot (71)](https://github.com/user-attachments/assets/faeb5b4a-6021-46be-87e8-fa893b87e866)

#### Overall Insights
- By combining these analyses, you can derive actionable insights that may include:
- Target markets for new subscription offerings or marketing campaigns.
- Understanding factors contributing to customer churn and strategies to mitigate them.
- Insights into pricing strategies based on popular subscription types and revenue analysis.
- Recommendations for improving customer retention and satisfaction based on average subscription duration and cancellation trends.
- 
![Screenshot (72)](https://github.com/user-attachments/assets/6782a358-dfa8-4342-96b7-c39573fc0b57)
![Screenshot (73)](https://github.com/user-attachments/assets/94b65a47-435e-4053-83a7-764a2a7c29ae)
![Screenshot (74)](https://github.com/user-attachments/assets/29c17365-7a72-4025-860c-4dd37c0f0c51)
![Screenshot (75)](https://github.com/user-attachments/assets/a768747d-8cd8-41b2-bf93-2b7a4021571c)
![Screenshot (76)](https://github.com/user-attachments/assets/821ae0b0-1ff7-412a-b45a-e3136a804924)
![Screenshot (77)](https://github.com/user-attachments/assets/3ca951e4-9674-487f-b050-a0bbf959d367)
![Screenshot (78)](https://github.com/user-attachments/assets/413361b0-d5de-4065-9df9-d17bc682a415)
