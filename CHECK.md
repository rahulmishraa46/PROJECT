Supermarket Sales Analysis: Complete Solution
This production-grade Python script solves the supermarket sales analysis tasks using a modern data stack including:

Prefect for orchestration

DuckDB for fast analytics

Plotly for interactive visualizations

Dataset: https://raw.githubusercontent.com/sushantag9/Supermarket-Sales-Data-Analysis/master/supermarket_sales%20-%20Sheet1.csv

Python Implementation
from prefect import flow, task
import pandas as pd
import duckdb
import plotly.express as px
import numpy as np
from datetime import datetime
from sklearn.linear_model import LinearRegression

# ---------------------------
# 1️⃣ Data Preparation (ETL)
# ---------------------------
@task(name="Extract Data", retries=2, retry_delay_seconds=30)
def extract_data():
    """Download dataset from GitHub"""
    url = "https://raw.githubusercontent.com/sushantag9/Supermarket-Sales-Data-Analysis/master/supermarket_sales%20-%20Sheet1.csv"
    return pd.read_csv(url)

@task(name="Transform Data")
def transform_data(df):
    """Clean and enrich dataset"""
    # DateTime conversion
    df['DateTime'] = pd.to_datetime(df['Date'] + ' ' + df['Time'])
    
    # Standardize text columns
    text_cols = ['Branch', 'City', 'Customer type', 'Gender', 'Product line', 'Payment']
    for col in text_cols:
        df[col] = df[col].str.title()
    
    # Calculate new metrics
    df['Hour'] = pd.to_datetime(df['Time']).dt.hour
    df['Gross Margin Percentage'] = (df['gross income'] / df['Total']) * 100
    df['DayOfWeek'] = df['DateTime'].dt.day_name()
    
    return df

@task(name="Load Data")
def load_data(df):
    """Save data in multiple formats"""
    # CSV
    df.to_csv('cleaned_supermarket_sales.csv', index=False)
    
    # Parquet
    df.to_parquet('supermarket_sales.parquet', index=False)
    
    # DuckDB
    conn = duckdb.connect('supermarket_sales.duckdb')
    conn.execute("CREATE OR REPLACE TABLE sales AS SELECT * FROM df")
    
    return "Data loaded successfully"

# ---------------------------
# 2️⃣ Exploratory Analysis
# ---------------------------
@task(name="Perform Analysis")
def perform_analysis(df):
    """Calculate key business metrics"""
    results = {}
    
    # Revenue metrics
    results['total_revenue'] = df['Total'].sum()
    results['avg_transaction'] = df['Total'].mean()
    
    # Product analysis
    results['product_quantity'] = df.groupby('Product line')['Quantity'].sum().to_dict()
    
    # Customer ratings
    results['city_ratings'] = df.groupby('City')['Rating'].mean().to_dict()
    
    # Sales patterns
    results['hourly_sales'] = df.groupby('Hour')['Total'].sum().to_dict()
    results['gender_spending'] = df.groupby('Gender')['Total'].mean().to_dict()
    
    return results

# ---------------------------
# 3️⃣ Visualization Tasks
# ---------------------------
@task(name="Generate Visualizations")
def generate_visualizations(df):
    """Create interactive business dashboards"""
    # Sales by product line
    fig1 = px.bar(df.groupby('Product line')['Total'].sum().reset_index(), 
                  x='Product line', y='Total',
                  title='Total Sales by Product Line',
                  color='Product line')
    fig1.write_html('sales_by_product.html')
    
    # Payment methods
    fig2 = px.pie(df, names='Payment', 
                  title='Payment Method Distribution',
                  hole=0.3)
    fig2.write_html('payment_methods.html')
    
    # Hourly sales pattern
    fig3 = px.line(df.groupby('Hour')['Total'].sum().reset_index(), 
                   x='Hour', y='Total',
                   title='Hourly Sales Trend',
                   markers=True)
    fig3.write_html('hourly_sales.html')
    
    # Quantity vs Total sales
    fig4 = px.scatter(df, x='Quantity', y='Total', 
                      trendline='ols',
                      title='Quantity vs Total Sales',
                      color='Product line')
    fig4.write_html('quantity_vs_sales.html')
    
    # Ratings by customer type
    fig5 = px.box(df, x='Customer type', y='Rating', 
                  color='Customer type',
                  title='Rating Distribution by Customer Type')
    fig5.write_html('ratings_by_customer.html')
    
    return "Visualizations created"

# ---------------------------
# 4️⃣ Business Insights
# ---------------------------
@task(name="Extract Insights")
def extract_insights(df):
    """Generate strategic business insights"""
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
    
    # Daily patterns
    daily_sales = df.groupby('DayOfWeek')['Total'].sum()
    insights['busiest_day'] = daily_sales.idxmax()
    
    # Price-quantity correlation
    insights['price_quantity_corr'] = df['Unit price'].corr(df['Quantity'])
    
    return insights

# ---------------------------
# 5️⃣ Advanced Analysis
# ---------------------------
@task(name="Advanced Analytics")
def advanced_analysis(df):
    """Perform bonus analytical tasks"""
    # Customer segmentation
    spending_threshold = df['Total'].quantile(0.9)
    high_value = df[df['Total'] > spending_threshold]
    high_value_count = len(high_value)
    
    # Time-series analysis
    daily_trend = df.groupby(df['DateTime'].dt.date)['Total'].sum()
    
    # Predictive modeling
    X = df[['Hour']]
    y = df['Total']
    model = LinearRegression().fit(X, y)
    r_sq = model.score(X, y)
    
    # Anomaly detection
    mean_total = df['Total'].mean()
    std_total = df['Total'].std()
    anomalies = df[abs(df['Total'] - mean_total) > 3 * std_total]
    anomaly_count = len(anomalies)
    
    return {
        'high_value_customers': high_value_count,
        'daily_trend': daily_trend.to_dict(),
        'time_spending_r2': r_sq,
        'anomaly_count': anomaly_count
    }

# ---------------------------
# MAIN FLOW
# ---------------------------
@flow(name="Supermarket Sales Analysis")
def supermarket_analysis_flow():
    # 1. ETL Pipeline
    raw_data = extract_data()
    clean_data = transform_data(raw_data)
    load_status = load_data(clean_data)
    print(f"✅ ETL Complete: {load_status}")
    
    # 2. Exploratory Analysis
    analysis_results = perform_analysis(clean_data)
    print(f"\n📊 Analysis Results:")
    print(f"Total Revenue: ${analysis_results['total_revenue']:,.2f}")
    print(f"Avg Transaction: ${analysis_results['avg_transaction']:,.2f}")
    
    # 3. Visualizations
    viz_status = generate_visualizations(clean_data)
    print(f"\n🎨 {viz_status}")
    
    # 4. Business Insights
    insights = extract_insights(clean_data)
    print("\n💡 Business Insights:")
    print(f"Top Branch: {insights['top_branch']} ({insights['branch_percentage']:.1f}% of income)")
    print(f"Electronics Rating: {insights['electronics_rating']:.2f} vs Others: {insights['other_rating']:.2f}")
    print(f"Member Revenue: ${insights['member_revenue']:,.2f}, Normal: ${insights['normal_revenue']:,.2f}")
    print(f"Busiest Day: {insights['busiest_day']}")
    print(f"Price-Quantity Correlation: {insights['price_quantity_corr']:.2f}")
    
    # 5. Advanced Analysis
    advanced = advanced_analysis(clean_data)
    print("\n🚀 Advanced Analysis:")
    print(f"High-value Customers: {advanced['high_value_customers']}")
    print(f"Anomalous Transactions: {advanced['anomaly_count']}")
    print(f"Time-of-day vs Spending (R²): {advanced['time_spending_r2']:.3f}")
    
    return "\n🎉 Analysis Complete!"

# Run the flow
if __name__ == "__main__":
    result = supermarket_analysis_flow()
    print(result)
