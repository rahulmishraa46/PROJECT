# Supermarket Sales Data Analysis Assignment

## 📋 Problem Statement
To understand customer behavior, product performance, and sales patterns to optimize their operations. Your task is to perform an end-to-end data analysis of their sales records using Python.

### 📊 Dataset
**Source**: [Supermarket Sales Dataset](https://raw.githubusercontent.com/sushantag9/Supermarket-Sales-Data-Analysis/master/supermarket_sales%20-%20Sheet1.csv)  
**Records**: 1,000 transactions  
**Time Period**: Jan-Mar 2019  
**Columns**: 17 (Invoice ID, Branch, City, Customer type, Gender, Product line, Unit price, Quantity, Tax 5%, Total, Date, Time, Payment, COGS, Gross income, Rating)

## 🎯 Objectives
1. Perform comprehensive ETL (Extract, Transform, Load) process
2. Conduct exploratory data analysis (EDA)
3. Generate actionable business insights
4. Create visualizations to support findings
5. Build a complete analysis pipeline


1️⃣ Data Preparation (ETL)
[ ] a. Extract data from the provided URL:

Write a script to download or read the data from its source.

[ ] b. Handle datetime conversion:

Combine the Date and Time columns into a single DateTime column.

Ensure the new column is of a proper datetime data type.

[ ] c. Clean data:

Standardize text columns (e.g., to Title Case).

Develop a strategy for handling any missing or null values.

[ ] d. Calculate new metrics:

Extract the Hour of purchase from the Time or DateTime column.

Calculate the Gross Margin Percentage using the formula: (Gross income / Total) * 100.

[ ] e. Save cleaned data to multiple formats:

Save the final, cleaned DataFrame as a CSV file.

Save the final, cleaned DataFrame as a Parquet file.

Load the data into a DuckDB database file.

2️⃣ Exploratory Analysis
[ ] a. Total revenue and average transaction value:

Calculate the sum of the 'Total' column for total revenue.

Calculate the average of the 'Total' column for the average transaction value.

[ ] b. Most popular product line by quantity sold:

Group by 'Product line' and sum the 'Quantity'.

[ ] c. City with highest customer ratings:

Group by 'City' and calculate the average 'Rating'.

[ ] d. Hourly sales pattern (peak sales hours):

Analyze the distribution of sales across the 'Hour of purchase'.

[ ] e. Gender spending differences:

Compare the average transaction value between different genders.

3️⃣ Visualization Tasks
[ ] a. Sales distribution by product line:

Create an interactive Bar Chart showing total sales for each product line.

[ ] b. Payment method popularity:

Create an interactive Pie Chart to visualize the distribution of payment methods.

[ ] c. Hourly sales trend:

Create an interactive Line Chart to show how total sales vary by the hour of the day.

[ ] d. Relationship between quantity and total sales:

Create an interactive Scatter Plot with 'Quantity' on one axis and 'Total' on the other.

[ ] e. Rating distribution by customer type:

Create an interactive Box Plot to compare the distribution of 'Rating' for 'Member' vs. 'Normal' customers.

4️⃣ Business Insights
[ ] 1. Which branch has the highest gross income? What's the percentage difference?

Group by 'Branch', sum the 'gross income', and calculate the percentage difference from the other branches.

[ ] 2. What's the average rating for electronic accessories? How does it compare to other categories?

Filter for 'Electronic accessories', calculate its average rating, and compare it with the average ratings of other product lines.

[ ] 3. Which customer type (Member/Normal) generates more revenue?

Group by 'Customer type' and sum the 'Total' sales to determine which is more lucrative.

[ ] 4. What's the busiest day of the week for sales?

Extract the day of the week from the DateTime column and analyze sales data to find the peak day.

[ ] 5. Identify any correlation between unit price and quantity purchased.

Calculate the correlation coefficient between 'Unit price' and 'Quantity'.

5️⃣ Advanced Analysis (Bonus)
[ ] a. Customer segmentation:

Use clustering algorithms (e.g., K-Means) based on spending habits and frequency to identify high-value customers.

[ ] b. Time-series analysis:

Analyze the daily sales trend to identify patterns, seasonality, or growth over time.

[ ] c. Predictive modeling:

Build a simple regression model to estimate the relationship between the time-of-day and customer spending.

[ ] d. Anomaly detection:

Implement a method to identify unusual transactions (e.g., exceptionally high values or unusual product combinations) that could be errors or fraud.



## 📂 Submission Requirements
1. **Python Notebook** (`supermarket_analysis.ipynb`) containing:
   - Complete executable code
   - Comments explaining each step
   - Visualzations embedded in notebook

