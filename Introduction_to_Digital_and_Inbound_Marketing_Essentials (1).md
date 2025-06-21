# Supermarket Sales Data Analysis Assignment

## 📋 Problem Statement
You've been hired as a Data Analyst by a supermarket chain with 3 branches in Myanmar. The management wants to understand customer behavior, product performance, and sales patterns to optimize their operations. Your task is to perform an end-to-end data analysis of their sales records using Python.

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

## 💻 Tasks

### 1️⃣ Data Preparation (ETL)
```python
# [TODO] Write code to:
# a. Extract data from the provided URL
# b. Handle datetime conversion (Date + Time → DateTime)
# c. Clean data: Standardize text (Title case), handle missing values
# d. Calculate new metrics:
#    - Hour of purchase (from Time)
#    - Gross Margin Percentage (Gross income / Total)
# e. Save cleaned data to multiple formats: CSV, Parquet, and DuckDB database
```

### 2️⃣ Exploratory Analysis
```python
# [TODO] Calculate and report:
# a. Total revenue and average transaction value
# b. Most popular product line by quantity sold
# c. City with highest customer ratings
# d. Hourly sales pattern (peak sales hours)
# e. Gender spending differences
```

### 3️⃣ Visualization Tasks
```python
# [TODO] Create interactive visualizations:
# a. Sales distribution by product line (Bar chart)
# b. Payment method popularity (Pie chart)
# c. Hourly sales trend (Line chart)
# d. Relationship between quantity and total sales (Scatter plot)
# e. Rating distribution by customer type (Box plot)
```

### 4️⃣ Business Insights
```python
# [TODO] Answer these business questions:
# 1. Which branch has the highest gross income? What's the percentage difference?
# 2. What's the average rating for electronic accessories? How does it compare to other categories?
# 3. Which customer type (Member/Normal) generates more revenue?
# 4. What's the busiest day of week for sales?
# 5. Identify any correlation between unit price and quantity purchased?
```

### 5️⃣ Advanced Analysis (Bonus)
```python
# [TODO] Perform:
# a. Customer segmentation: Identify high-value customers
# b. Time-series analysis: Daily sales trend
# c. Predictive: Estimate relationship between time-of-day and spending
# d. Anomaly detection: Identify unusual transactions
```

## 📂 Submission Requirements
1. **Python Notebook** (`supermarket_analysis.ipynb`) containing:
   - Complete executable code
   - Comments explaining each step
   - Visualizations embedded in notebook

2. **Report PDF** (`analysis_report.pdf`) with:
   - Key findings (1 page)
   - Business recommendations (3-5)
   - Supporting visualizations

3. **Processed Data Files** in a `/data` folder:
   - `cleaned_sales.csv`
   - `sales_data.parquet`
   - `supermarket.db` (DuckDB database)

## ⏱️ Timeline
- **Due Date**: 7 days from assignment date
- **Late Submission**: 10% penalty per day

## 📝 Evaluation Criteria
| Criteria | Weight |
|----------|--------|
| Data Cleaning & Transformation | 25% |
| Analysis Completeness | 30% |
| Visualization Quality | 20% |
| Business Insights | 15% |
| Code Quality & Documentation | 10% |

## 💡 Tips for Success
1. Use modular code (functions for each task)
2. Handle datetime conversion carefully
3. Validate data quality at each step
4. Provide clear interpretations for each visualization
5. Focus on actionable insights rather than just statistics

## 🚀 Bonus Points
1. Implement error handling in data pipeline
2. Create interactive dashboard (Streamlit/Panel)
3. Perform statistical hypothesis testing
4. Implement machine learning model for sales prediction

---

**Note to Students**: This assignment simulates real-world data analysis tasks. Focus on delivering actionable insights that would help supermarket management make better decisions. Good luck! 💪
