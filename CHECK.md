Here's the solution organized by question/task in a clear Q&A format for easy reference:

```markdown
# Supermarket Sales Analysis: Complete Solution

## 1️⃣ Data Preparation (ETL)

### a. Extract data from the provided URL
```python
@task(name="Extract Data", retries=2, retry_delay_seconds=30)
def extract_data():
    url = "https://raw.githubusercontent.com/sushantag9/Supermarket-Sales-Data-Analysis/master/supermarket_sales%20-%20Sheet1.csv"
    return pd.read_csv(url)
```

### b. Handle datetime conversion
```python
def transform_data(df):
    df['DateTime'] = pd.to_datetime(df['Date'] + ' ' + df['Time'])
    return df
```

### c. Clean data
```python
def transform_data(df):
    text_cols = ['Branch', 'City', 'Customer type', 'Gender', 'Product line', 'Payment']
    for col in text_cols:
        df[col] = df[col].str.title()
    return df
```

### d. Calculate new metrics
```python
def transform_data(df):
    df['Hour'] = pd.to_datetime(df['Time']).dt.hour
    df['Gross Margin Percentage'] = (df['gross income'] / df['Total']) * 100
    df['DayOfWeek'] = df['DateTime'].dt.day_name()
    return df
```

### e. Save cleaned data
```python
@task(name="Load Data")
def load_data(df):
    df.to_csv('cleaned_supermarket_sales.csv', index=False)
    df.to_parquet('supermarket_sales.parquet', index=False)
    conn = duckdb.connect('supermarket_sales.duckdb')
    conn.execute("CREATE OR REPLACE TABLE sales AS SELECT * FROM df")
    return "Data loaded successfully"
```

## 2️⃣ Exploratory Analysis

### a. Total revenue and average transaction value
```python
def perform_analysis(df):
    results = {}
    results['total_revenue'] = df['Total'].sum()
    results['avg_transaction'] = df['Total'].mean()
    return results
```

### b. Most popular product line by quantity sold
```python
def perform_analysis(df):
    results['product_quantity'] = df.groupby('Product line')['Quantity'].sum().to_dict()
    return results
```

### c. City with highest customer ratings
```python
def perform_analysis(df):
    results['city_ratings'] = df.groupby('City')['Rating'].mean().to_dict()
    return results
```

### d. Hourly sales pattern
```python
def perform_analysis(df):
    results['hourly_sales'] = df.groupby('Hour')['Total'].sum().to_dict()
    return results
```

### e. Gender spending differences
```python
def perform_analysis(df):
    results['gender_spending'] = df.groupby('Gender')['Total'].mean().to_dict()
    return results
```

## 3️⃣ Visualization Tasks

### a. Sales distribution by product line
```python
def generate_visualizations(df):
    fig1 = px.bar(df.groupby('Product line')['Total'].sum().reset_index(), 
                  x='Product line', y='Total',
                  title='Total Sales by Product Line',
                  color='Product line')
    fig1.write_html('sales_by_product.html')
```

### b. Payment method popularity
```python
def generate_visualizations(df):
    fig2 = px.pie(df, names='Payment', 
                  title='Payment Method Distribution',
                  hole=0.3)
    fig2.write_html('payment_methods.html')
```

### c. Hourly sales trend
```python
def generate_visualizations(df):
    fig3 = px.line(df.groupby('Hour')['Total'].sum().reset_index(), 
                   x='Hour', y='Total',
                   title='Hourly Sales Trend',
                   markers=True)
    fig3.write_html('hourly_sales.html')
```

### d. Relationship between quantity and total sales
```python
def generate_visualizations(df):
    fig4 = px.scatter(df, x='Quantity', y='Total', 
                      trendline='ols',
                      title='Quantity vs Total Sales',
                      color='Product line')
    fig4.write_html('quantity_vs_sales.html')
```

### e. Rating distribution by customer type
```python
def generate_visualizations(df):
    fig5 = px.box(df, x='Customer type', y='Rating', 
                  color='Customer type',
                  title='Rating Distribution by Customer Type')
    fig5.write_html('ratings_by_customer.html')
```

## 4️⃣ Business Insights

### 1. Branch with highest gross income
```python
def extract_insights(df):
    branch_income = df.groupby('Branch')['gross income'].sum()
    insights['top_branch'] = branch_income.idxmax()
    insights['branch_percentage'] = (branch_income.max() / branch_income.sum()) * 100
```

### 2. Average rating for electronic accessories
```python
def extract_insights(df):
    insights['electronics_rating'] = df.loc[df['Product line'] == 'Electronic Accessories', 'Rating'].mean()
    insights['other_rating'] = df.loc[df['Product line'] != 'Electronic Accessories', 'Rating'].mean()
```

### 3. Revenue by customer type
```python
def extract_insights(df):
    customer_revenue = df.groupby('Customer type')['Total'].sum()
    insights['member_revenue'] = customer_revenue.get('Member', 0)
    insights['normal_revenue'] = customer_revenue.get('Normal', 0)
```

### 4. Busiest day of the week
```python
def extract_insights(df):
    daily_sales = df.groupby('DayOfWeek')['Total'].sum()
    insights['busiest_day'] = daily_sales.idxmax()
```

### 5. Correlation between unit price and quantity
```python
def extract_insights(df):
    insights['price_quantity_corr'] = df['Unit price'].corr(df['Quantity'])
```

## 5️⃣ Advanced Analysis (Bonus)

### a. Customer segmentation
```python
def advanced_analysis(df):
    spending_threshold = df['Total'].quantile(0.9)
    high_value = df[df['Total'] > spending_threshold]
    high_value_count = len(high_value)
    return {'high_value_customers': high_value_count}
```

### b. Time-series analysis
```python
def advanced_analysis(df):
    daily_trend = df.groupby(df['DateTime'].dt.date)['Total'].sum()
    return {'daily_trend': daily_trend.to_dict()}
```

### c. Predictive modeling
```python
def advanced_analysis(df):
    X = df[['Hour']]
    y = df['Total']
    model = LinearRegression().fit(X, y)
    r_sq = model.score(X, y)
    return {'time_spending_r2': r_sq}
```

### d. Anomaly detection
```python
def advanced_analysis(df):
    mean_total = df['Total'].mean()
    std_total = df['Total'].std()
    anomalies = df[abs(df['Total'] - mean_total) > 3 * std_total]
    anomaly_count = len(anomalies)
    return {'anomaly_count': anomaly_count}
```

## Orchestration Flow
```python
@flow(name="Supermarket Sales Analysis")
def supermarket_analysis_flow():
    raw_data = extract_data()
    clean_data = transform_data(raw_data)
    load_status = load_data(clean_data)
    
    analysis_results = perform_analysis(clean_data)
    viz_status = generate_visualizations(clean_data)
    insights = extract_insights(clean_data)
    advanced = advanced_analysis(clean_data)
    
    # Print results
    print(f"Total Revenue: ${analysis_results['total_revenue']:,.2f}")
    print(f"Top Branch: {insights['top_branch']}")
    print(f"High-value Customers: {advanced['high_value_customers']}")
    
    return "Analysis Complete!"

if __name__ == "__main__":
    result = supermarket_analysis_flow()
    print(result)
```

## Key Features
- **Modern Stack**: Prefect + DuckDB + Plotly
- **Production Ready**: Error handling, retries, multiple output formats
- **Complete Analysis**: From ETL to advanced analytics
- **Actionable Insights**: Business-ready metrics and visualizations

## How to Run
```bash
pip install prefect pandas duckdb plotly scikit-learn
python supermarket_analysis.py
```

**Outputs**:
- Cleaned data: CSV, Parquet, DuckDB
- Interactive HTML visualizations
- Business insights in console
```
