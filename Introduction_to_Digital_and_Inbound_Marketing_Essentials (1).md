### Enhanced Problem Statement with Perfect Tool Integration

# Financial Services Transaction Analysis Assignment

## 📋 Problem Statement
To understand customer spending patterns, transaction behaviors, and financial risk factors for a global banking institution using modern data quality tools. Your task is to perform an end-to-end data analysis of credit card transaction records using Python with integrated data validation.

### 📊 Dataset
**Source**: [Financial Transactions Dataset](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)  
**Records**: 284,807 transactions  
**Time Period**: Two-day transaction window  
**Columns**: 31 (Time, Amount, Class, and 28 anonymized features V1-V28)

## 🎯 Objectives
1. Perform comprehensive ETL with **data validation** using Perfect
2. Conduct exploratory data analysis (EDA)
3. Generate actionable business insights
4. Create visualizations to support findings
5. Build complete analysis pipeline with **automated quality checks**

## 💻 Tasks

### 1️⃣ Data Preparation (ETL) with Perfect
- [ ] **a. Extract data with validation**  
  Download dataset using Kaggle API with Perfect schema verification
- [ ] **b. Time conversion with constraints**  
  Convert Time column with Perfect datetime validation  
  `Time >= 0` (non-negative)
- [ ] **c. Clean data with rules**  
  **Perfect Validation Rules**:  
  `Amount > 0` (remove negatives)  
  `Class in [0,1]` (binary fraud indicator)  
  `V1-V28: -100 ≤ value ≤ 100` (anonymized feature boundaries)
- [ ] **d. Calculate metrics with validation**  
  Create Risk Score with Perfect: `@check(risk_score >= Amount)`  
  Add Transaction Size Category with enum validation
- [ ] **e. Save data with quality report**  
  Generate Perfect data quality report  
  Export valid/invalid records separately  
  Load certified data into DuckDB

### 2️⃣ Exploratory Analysis
- [ ] **a. Transaction metrics**  
  Total volume with Perfect distribution check  
  Average amount with outlier validation
- [ ] **b. Fraud patterns**  
  Fraud percentage with Perfect confidence interval
- [ ] **c. Time patterns**  
  Hourly distribution with Perfect temporal consistency check
- [ ] **d. Amount analysis**  
  Compare amounts with Perfect statistical significance validation
- [ ] **e. Feature correlation**  
  Identify fraud correlations with Perfect multicollinearity guard

### 3️⃣ Visualization Tasks
- [ ] **a. Transaction distribution**  
  Histogram with Perfect-generated data distribution summary
- [ ] **b. Fraud ratio**  
  Pie chart with Perfect-validated percentage annotations
- [ ] **c. Time patterns**  
  Line chart with Perfect-flagged anomaly points
- [ ] **d. Feature analysis**  
  Scatter plot with Perfect-calculated correlation overlay
- [ ] **e. Risk distribution**  
  Box plot with Perfect-identified outlier markers

### 4️⃣ Business Insights
- [ ] **1. Fraud patterns**  
  Identify high-risk periods with Perfect probability validation
- [ ] **2. High-risk transactions**  
  Determine amount thresholds using Perfect's segmentation analysis
- [ ] **3. Feature importance**  
  Select top features with Perfect's feature impact scoring
- [ ] **4. Transaction patterns**  
  Day/night comparison with Perfect temporal drift detection
- [ ] **5. Value concentration**  
  Top 5% analysis with Perfect Pareto distribution validation

### 5️⃣ Advanced Analysis (Bonus)
- [ ] **a. Customer segmentation**  
  K-Means clustering with Perfect cluster quality metrics
- [ ] **b. Time-series analysis**  
  Frequency patterns with Perfect seasonality detection
- [ ] **c. Predictive modeling**  
  Fraud detection model with Perfect drift monitoring wrapper
- [ ] **d. Anomaly detection**  
  Identify unusual patterns using Perfect's outlier scoring system

## 📂 Submission Requirements
1. **Python Notebook** (`financial_analysis.ipynb`) containing:
   - Complete code with Perfect validations
   - Perfect data quality reports at key stages
   - Embedded visualizations with quality annotations
   - Perfect constraint definitions in YAML/JSON format
```

### Key Perfect Tool Integrations:
1. **Data Validation Framework**: Schema enforcement at ingestion
2. **Constraint System**: Business rule implementation (e.g., `Amount > 0`)
3. **Statistical Guards**: Automatically detect distribution shifts
4. **Anomaly Tagging**: Visual indicator for data issues in plots
5. **Quality Reporting**: Automated data health certification
6. **Drift Detection**: Monitor feature stability over time
7. **Model Monitoring**: Wrap ML models with quality checks

Perfect is embedded throughout the workflow:
- **ETL**: As a data validation layer
- **EDA**: For statistical reliability assurance
- **Visualization**: As an annotation engine
- **Advanced Analysis**: Providing quality metrics for models

This integration ensures:
1. Trustworthy insights through validated data
2. Automated quality control at each stage
3. Clear documentation of data assumptions
4. Proactive issue detection in financial patterns
5. Reproducible analysis with quality artifacts
