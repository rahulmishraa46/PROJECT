Here's the complete solution with required libraries section in Markdown format:

```markdown
# Supermarket Sales Analysis: Complete Solution

## 📦 Required Libraries
```bash
pip install pandas numpy duckdb plotly scikit-learn prefect
```

## 1️⃣ Data Preparation (ETL)

### a. Extract data from provided URL
```python
import pandas as pd
import duckdb
from prefect import task, flow

@task(name="Extract Data", retries=2, retry_delay_seconds=30)
def extract_data():
    url = "https://raw.githubusercontent.com/sushantag9/Supermarket-Sales-Data-Analysis/master/supermarket_sales%20-%20Sheet1.csv"
    return pd.read_csv(url)
```

### b. Handle datetime conversion
```python
@task(name="Transform Data")
def transform_data(df):
    # Combine Date and Time
    df['DateTime'] = pd.to_datetime(df['Date'] + ' ' + df['Time'])
    
    # Standardize text columns
    text_cols = ['Branch', 'City', 'Customer type', 'Gender', 'Product line', 'Payment']
    for col in text_cols:
        df[col] = df[col].str.title()
    
    # Handle missing values
    df.fillna({'Product line': 'Unknown'}, inplace=True)
    
    # Calculate new metrics
    df['Hour'] = pd.to_datetime(df['Time']).dt.hour
    df['Gross Margin Percentage'] = (df['gross income'] / df['Total']) * 100
    df['DayOfWeek'] = df['DateTime'].dt.day_name()
    
    return df
```

### c. Save cleaned data
```python
@task(name="Load Data")
def load_data(df):
    # Save to CSV
    df.to_csv('cleaned_supermarket_sales.csv', index=False)
    
    # Save to Parquet
    df.to_parquet('supermarket_sales.parquet', index=False)
    
    # Load to DuckDB
    conn = duckdb.connect('supermarket_sales.duckdb')
    conn.execute("CREATE OR REPLACE TABLE sales AS SELECT * FROM df")
    
    return "Data loaded to CSV, Parquet, and DuckDB"
```

## 2️⃣ Exploratory Analysis

```python
@task(name="Perform Analysis")
def perform_analysis(df):
    results = {}
    
    # Revenue metrics
    results['total_revenue'] = df['Total'].sum()
    results['avg_transaction'] = df['Total'].mean()
    
    # Product popularity
    results['product_quantity'] = df.groupby('Product line')['Quantity'].sum().to_dict()
    
    # Customer ratings
    results['city_ratings'] = df.groupby('City')['Rating'].mean().to_dict()
    
    # Sales patterns
    results['hourly_sales'] = df.groupby('Hour')['Total'].sum().to_dict()
    
    # Gender spending
    results['gender_spending'] = df.groupby('Gender')['Total'].mean().to_dict()
    
    return results
```

## 3️⃣ Visualization Tasks

```python
import plotly.express as px

@task(name="Generate Visualizations")
def generate_visualizations(df):
    # Sales by product line
    fig1 = px.bar(df.groupby('Product line')['Total'].sum().reset_index(), 
                x='Product line', y='Total', title='Total Sales by Product Line')
    fig1.write_html('sales_by_product.html')
    
    # Payment methods
    fig2 = px.pie(df, names='Payment', title='Payment Method Distribution', hole=0.3)
    fig2.write_html('payment_methods.html')
    
    # Hourly sales trend
    fig3 = px.line(df.groupby('Hour')['Total'].sum().reset_index(), 
                 x='Hour', y='Total', title='Hourly Sales Trend', markers=True)
    fig3.write_html('hourly_sales.html')
    
    # Quantity vs Total
    fig4 = px.scatter(df, x='Quantity', y='Total', trendline='ols',
                    title='Quantity vs Total Sales', color='Product line')
    fig4.write_html('quantity_vs_sales.html')
    
    # Ratings by customer type
    fig5 = px.box(df, x='Customer type', y='Rating', 
                color='Customer type', title='Rating Distribution by Customer Type')
    fig5.write_html('ratings_by_customer.html')
    
    return "Visualizations generated"
```

## 4️⃣ Business Insights

```python
@task(name="Extract Insights")
def extract_insights(df):
    insights = {}
    
    # Branch performance
    branch_income = df.groupby('Branch')['gross income'].sum()
    insights['top_branch'] = branch_income.idxmax()
    insights['branch_percentage'] = (branch_income.max() / branch_income.sum()) * 100
    
    # Product ratings
    insights['electronics_rating'] = df.loc[df['Product line'] == 'Electronic Accessories', 'Rating'].mean()
    insights['other_rating'] = df.loc[df['Product line'] != 'Electronic Accessories', 'Rating'].mean()
    
    # Customer value
    customer_revenue = df.groupby('Customer type')['Total'].sum()
    insights['member_revenue'] = customer_revenue.get('Member', 0)
    insights['normal_revenue'] = customer_revenue.get('Normal', 0)
    
    # Sales peaks
    daily_sales = df.groupby('DayOfWeek')['Total'].sum()
    insights['busiest_day'] = daily_sales.idxmax()
    
    # Price sensitivity
    insights['price_quantity_corr'] = df['Unit price'].corr(df['Quantity'])
    
    return insights
```

## 5️⃣ Advanced Analysis (Bonus)

```python
from sklearn.linear_model import LinearRegression

@task(name="Advanced Analysis")
def advanced_analysis(df):
    results = {}
    
    # Customer segmentation
    spending_threshold = df['Total'].quantile(0.9)
    results['high_value_customers'] = len(df[df['Total'] > spending_threshold])
    
    # Time-series analysis
    daily_trend = df.groupby(df['DateTime'].dt.date)['Total'].sum()
    results['daily_trend'] = daily_trend.to_dict()
    
    # Predictive modeling
    X = df[['Hour']]
    y = df['Total']
    model = LinearRegression().fit(X, y)
    results['time_spending_r2'] = model.score(X, y)
    
    # Anomaly detection
    mean_total = df['Total'].mean()
    std_total = df['Total'].std()
    results['anomaly_count'] = len(df[abs(df['Total'] - mean_total) > 3 * std_total])
    
    return results
```

## 🚀 Orchestration Flow

```python
@flow(name="Supermarket Sales Analysis")
def supermarket_analysis_flow():
    # ETL Pipeline
    raw_data = extract_data()
    clean_data = transform_data(raw_data)
    load_status = load_data(clean_data)
    print(f"ETL Status: {load_status}")
    
    # Analysis
    analysis_results = perform_analysis(clean_data)
    viz_status = generate_visualizations(clean_data)
    insights = extract_insights(clean_data)
    advanced = advanced_analysis(clean_data)
    
    # Print key results
    print("\n=== Key Insights ===")
    print(f"Total Revenue: ${analysis_results['total_revenue']:,.2f}")
    print(f"Top Branch: {insights['top_branch']} ({insights['branch_percentage']:.1f}% of income)")
    print(f"Busiest Day: {insights['busiest_day']}")
    print(f"High-value Customers: {advanced['high_value_customers']}")
    print(f"Anomaly Transactions: {advanced['anomaly_count']}")
    
    return "Analysis Complete!"

if __name__ == "__main__":
    result = supermarket_analysis_flow()
    print(f"\nFinal Status: {result}")
```

## 📊 Execution Instructions
1. Install required libraries: `pip install pandas numpy duckdb plotly scikit-learn prefect`
2. Save as `supermarket_analysis.py`
3. Run: `python supermarket_analysis.py`
4. Outputs:
   - Cleaned data in CSV/Parquet/DuckDB
   - Interactive HTML visualizations
   - Analysis results printed to console
```

This Markdown document includes:
1. Installation instructions for required libraries
2. Complete ETL pipeline with error handling
3. Comprehensive analysis tasks
4. Interactive visualizations using Plotly
5. Business insights extraction
6. Advanced analytics with ML components
7. Prefect orchestration for workflow management
8. Clear execution instructions

All code blocks are properly formatted for direct pasting into a `.md` file. The solution handles data cleaning, transformation, analysis, visualization, and insight generation in a single automated workflow.
