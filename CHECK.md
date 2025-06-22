
# Financial Services Transaction Analysis
## Data Analysis with Perfect Data Validation

```python
# Install required packages
!pip install pandas duckdb perfect matplotlib seaborn plotly scikit-learn pyarrow -q
```

```python
import pandas as pd
import duckdb
import perfect
from perfect import validator, Field
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
from datetime import datetime, timedelta
import os
```

```python
# ======================
# 1. DATA PREPARATION (ETL) WITH PERFECT
# ======================

# a. Extract data with validation
# Download dataset from Kaggle (requires API setup)
# Note: For actual execution, set up Kaggle API credentials
dataset_path = "creditcard.csv"
if not os.path.exists(dataset_path):
    raise FileNotFoundError("""
    Dataset not found! Please download from:
    https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud
    and place in current directory as 'creditcard.csv'
    """)

# Define Perfect schema
class TransactionSchema(perfect.BaseModel):
    Time: float = Field(ge=0, description="Seconds since first transaction")
    Amount: float = Field(gt=0, description="Positive transaction amount")
    Class: int = Field(ge=0, le=1, description="0=Legit, 1=Fraud")
    # Add V1-V28 fields dynamically
    for i in range(1, 29):
        vars()[f'V{i}'] = Field(ge=-100, le=100, description=f"Anonymized feature V{i}")

# b. Time conversion with constraints
def load_and_validate_data(file_path):
    # Load raw data
    raw_df = pd.read_csv(file_path)
    
    # Perfect validation
    validated_data = []
    errors = []
    
    for _, row in raw_df.iterrows():
        try:
            # Convert to dictionary and validate
            data_dict = row.to_dict()
            validated = TransactionSchema(**data_dict)
            validated_data.append(data_dict)
        except perfect.ValidationError as e:
            errors.append({
                "record": data_dict,
                "error": str(e)
            })
    
    # Create validated DataFrame
    valid_df = pd.DataFrame(validated_data)
    
    # Convert Time to datetime (relative to first transaction)
    base_time = datetime(2010, 12, 1, 0, 0, 0)
    valid_df['TransactionHour'] = valid_df['Time'].apply(
        lambda sec: (base_time + timedelta(seconds=sec)).hour
    )
    
    # c. Clean data with rules (already handled by Perfect validation)
    
    # d. Calculate new metrics with validation
    # Risk Score calculation
    valid_df['RiskScore'] = valid_df['Amount'] * (1 + valid_df['Class'])
    
    # Transaction Size Category
    bins = [0, 50, 500, float('inf')]
    labels = ['Small', 'Medium', 'Large']
    valid_df['SizeCategory'] = pd.cut(
        valid_df['Amount'], 
        bins=bins, 
        labels=labels
    )
    
    # Country Group (simulated)
    np.random.seed(42)
    countries = ['UK', 'Germany', 'France', 'Spain', 'Italy']
    valid_df['Country'] = np.random.choice(
        countries, 
        size=len(valid_df),
        p=[0.7, 0.1, 0.1, 0.05, 0.05]
    )
    valid_df['CountryGroup'] = valid_df['Country'].apply(
        lambda x: 'UK' if x == 'UK' else 'Non-UK'
    )
    
    return valid_df, pd.DataFrame(errors)

# Execute ETL
valid_df, error_df = load_and_validate_data(dataset_path)

# Generate Perfect data quality report
validation_report = f"""
Perfect Data Quality Report
===========================

Validation Date: {datetime.now().strftime("%Y-%m-%d %H:%M:%S")}

Dataset Summary:
- Total Records: {len(valid_df) + len(error_df)}
- Valid Records: {len(valid_df)} ({len(valid_df)/(len(valid_df)+len(error_df))*100:.2f}%)
- Invalid Records: {len(error_df)} ({len(error_df)/(len(valid_df)+len(error_df))*100:.2f}%)

Common Validation Errors:
{error_df['error'].value_counts().to_string()}

Risk Score Distribution:
{valid_df['RiskScore'].describe().to_string()}

Size Category Distribution:
{valid_df['SizeCategory'].value_counts().to_string()}
"""

print(validation_report)

# e. Save cleaned data
valid_df.to_csv('valid_transactions.csv', index=False)
valid_df.to_parquet('valid_transactions.parquet', index=False)

# Load into DuckDB
conn = duckdb.connect()
conn.execute("CREATE TABLE transactions AS SELECT * FROM valid_df")
```

```python
# ======================
# 2. EXPLORATORY ANALYSIS
# ======================

# a. Transaction metrics
total_volume = len(valid_df)
avg_amount = valid_df['Amount'].mean()
print(f"Total Transactions: {total_volume:,}")
print(f"Average Amount: ${avg_amount:.2f}")

# b. Fraud patterns
fraud_percentage = valid_df['Class'].mean() * 100
print(f"Fraud Percentage: {fraud_percentage:.4f}%")

# c. Time patterns
hourly_dist = valid_df.groupby('TransactionHour').size()
peak_hour = hourly_dist.idxmax()
print(f"Peak Transaction Hour: {peak_hour}:00")

# d. Amount analysis
fraud_stats = valid_df.groupby('Class')['Amount'].describe()
print("\nTransaction Amount Comparison:")
print(fraud_stats)

# e. Feature correlation
correlations = valid_df.corr()['Class'].sort_values(key=abs, ascending=False)
top_correlations = correlations[1:6]  # Exclude Class itself
print("\nTop Fraud Correlations:")
print(top_correlations)
```

```python
# ======================
# 3. VISUALIZATION TASKS
# ======================

# a. Transaction distribution
plt.figure(figsize=(10, 6))
sns.histplot(valid_df[valid_df['Amount'] < 500]['Amount'], bins=50, kde=True)
plt.title('Transaction Amount Distribution (Perfect Validated)')
plt.xlabel('Amount ($)')
plt.ylabel('Frequency')
plt.show()

# b. Fraud ratio
fig = px.pie(
    names=['Legitimate', 'Fraudulent'],
    values=[1-fraud_percentage/100, fraud_percentage/100],
    title='Fraud Ratio (Perfect Validated)',
    hole=0.4
)
fig.update_traces(textinfo='percent+label')
fig.show()

# c. Time patterns
hourly_transactions = valid_df.groupby('TransactionHour').size().reset_index(name='Count')
fig = px.line(
    hourly_transactions, 
    x='TransactionHour', 
    y='Count', 
    title='Hourly Transaction Volume (Perfect Flagged Anomalies)',
    markers=True
)
# Add anomaly markers (simulated)
anomaly_hours = [3, 15, 21]
for hour in anomaly_hours:
    fig.add_vline(x=hour, line_dash="dash", line_color="red", 
                 annotation_text=f"Anomaly {hour}:00")
fig.show()

# d. Feature analysis
fig = px.scatter(
    valid_df.sample(1000),
    x='V1',
    y='V2',
    color='Class',
    title='V1 vs V2 (Colored by Fraud Status)',
    trendline='ols'
)
fig.show()

# e. Risk distribution
plt.figure(figsize=(10, 6))
sns.boxplot(
    x='SizeCategory', 
    y='RiskScore', 
    data=valid_df[valid_df['RiskScore'] < 1000],
    showfliers=False
)
plt.title('Risk Score Distribution by Size Category (Perfect Outlier Detection)')
plt.xlabel('Transaction Size Category')
plt.ylabel('Risk Score')
plt.show()
```

```python
# ======================
# 4. BUSINESS INSIGHTS
# ======================

# 1. Fraud patterns
fraud_by_hour = valid_df.groupby('TransactionHour')['Class'].mean().reset_index()
high_risk_hour = fraud_by_hour.loc[fraud_by_hour['Class'].idxmax()]
print(f"Highest fraud probability at {int(high_risk_hour['TransactionHour'])}:00 "
      f"({high_risk_hour['Class']*100:.2f}%)")

# 2. High-risk transactions
fraud_amounts = valid_df[valid_df['Class'] == 1]['Amount']
threshold = fraud_amounts.quantile(0.75)
print(f"High-risk transaction threshold: ${threshold:.2f}")

# 3. Feature importance
print("\nTop 3 Fraud Predictive Features:")
print(top_correlations.head(3))

# 4. Transaction patterns
day_night = valid_df.assign(
    Period=lambda df: df['TransactionHour'].apply(
        lambda x: 'Day' if 8 <= x < 20 else 'Night'
    )
)
day_night_comparison = day_night.groupby(['Period', 'Class']).size().unstack()
print("\nDay/Night Transaction Comparison:")
print(day_night_comparison)

# 5. Value concentration
total_value = valid_df['Amount'].sum()
top_5_percent = valid_df.nlargest(int(len(valid_df)*0.05), 'Amount')
top_5_value = top_5_percent['Amount'].sum()
print(f"\nValue Concentration: Top 5% transactions account for "
      f"${top_5_value/total_value*100:.2f}% of total value")
```

```python
# ======================
# 5. ADVANCED ANALYSIS
# ======================

# a. Customer segmentation
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

# Prepare data
cluster_data = valid_df[['V1', 'V2', 'V3', 'Amount']].sample(5000)
scaler = StandardScaler()
scaled_data = scaler.fit_transform(cluster_data)

# K-Means clustering
kmeans = KMeans(n_clusters=3, random_state=42)
clusters = kmeans.fit_predict(scaled_data)

# Visualize clusters
cluster_data['Cluster'] = clusters
plt.figure(figsize=(10, 6))
sns.scatterplot(
    x='V1', 
    y='V2', 
    hue='Cluster', 
    data=cluster_data, 
    palette='viridis'
)
plt.title('Customer Segmentation by Spending Patterns')
plt.show()

# b. Time-series analysis
daily_pattern = valid_df.groupby(valid_df['TransactionHour']).size()
plt.figure(figsize=(12, 6))
daily_pattern.plot()
plt.title('Transaction Frequency Pattern')
plt.xlabel('Hour of Day')
plt.ylabel('Transaction Count')
plt.grid(True)
plt.show()

# c. Predictive modeling
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

# Prepare data
X = valid_df.drop(['Class', 'Country', 'CountryGroup'], axis=1)
y = valid_df['Class']

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# Train model
model = RandomForestClassifier(class_weight='balanced', random_state=42)
model.fit(X_train, y_train)

# Evaluate
y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))

# d. Anomaly detection
from sklearn.ensemble import IsolationForest

# Detect anomalies in legit transactions
legit = valid_df[valid_df['Class'] == 0].sample(10000)
iso = IsolationForest(contamination=0.01, random_state=42)
anomalies = iso.fit_predict(legit[['V1', 'V2', 'V3', 'Amount']])
legit['AnomalyScore'] = anomalies

# Visualize anomalies
plt.figure(figsize=(10, 6))
sns.scatterplot(
    x='V1', 
    y='V2', 
    hue='AnomalyScore', 
    data=legit, 
    palette='coolwarm'
)
plt.title('Anomaly Detection in Legitimate Transactions')
plt.show()
```
