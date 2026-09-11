# E-Commerce Data Warehouse: Customer Behaviour, Sales & Analysis

## 1. Project Title
**E-Commerce Data Warehouse: Customer Behaviour, Sales & Analysis**

---

## 2. Project Overview
This repository contains a Data Warehousing and Data Mining case study focusing on customer behavior analysis, sales trends, and unsupervised customer segmentation for an online retail e-commerce platform. The project demonstrates the end-to-end pipeline from dataset loading, data cleaning, RFM (Recency, Frequency, Monetary) metric calculation, log transformation, feature scaling, clustering algorithm implementations (K-Means vs. Agglomerative Hierarchical Clustering), to data warehouse star schema design, OLAP queries, and business strategy formulation.

---

## 3. Problem Statement
E-commerce businesses process large volumes of transactional records daily. However, raw transactional records lack direct customer behavioral insights, making it difficult to:
- Identify high-value "champion" customers versus churn-risk casual shoppers.
- Understand purchasing frequencies, spending habits, and recency of customer activity.
- Organize unstructured transaction logs into a structured Data Warehouse (Star Schema) suitable for multi-dimensional Analytical SQL reporting and OLAP analysis.
- Formulate data-driven marketing and retention strategies based on empirical customer segments.

---

## 4. Project Objectives
- **Data Preprocessing & Cleaning**: Clean raw retail transaction logs by filtering invalid entries, missing CustomerIDs, and purchase cancellations.
- **Customer Feature Engineering & RFM Analysis**: Aggregate transaction logs at the customer level to extract Recency, Frequency, Monetary, Average Order Value (AOV), Customer Lifetime Days, and Purchase Rate.
- **Unsupervised Machine Learning**: Apply Log Transformation ($\log(1+x)$) and `StandardScaler` to handle distribution skewness, followed by K-Means and Agglomerative Hierarchical Clustering.
- **Model Evaluation**: Compare cluster models using Silhouette Scores to select the optimal model.
- **Data Warehouse Modeling**: Design a Star Schema model comprising `Fact_Sales` and associated dimension tables (`Dim_Customer`, `Dim_Product`, `Dim_Time`).
- **OLAP & Business Reporting**: Map conceptual OLAP operations (Roll-up, Drill-down, Slice, Dice) and derive actionable business strategies for e-commerce growth.

---

## 5. Dataset and Source
- **Dataset Name**: Online Retail Dataset
- **Source**: UCI Machine Learning Repository / E-Commerce Retail Transactions
- **Raw Dataset Size**: 541,909 rows $\times$ 8 columns
- **Raw Schema Attributes**:
  - `InvoiceNo`: 6-digit transaction code (Prefix 'C' denotes cancellation)
  - `StockCode`: Product item code
  - `Description`: Product name
  - `Quantity`: Number of units purchased per transaction
  - `InvoiceDate`: Timestamp of transaction
  - `UnitPrice`: Product unit price in GBP (£)
  - `CustomerID`: 5-digit unique customer identification number (135,080 missing values)
  - `Country`: Customer geographic country name
- **Processed Customer Records**: 4,338 unique customer profiles

---

## 6. Technologies Used
- **Programming Language**: Python 3.12
- **Data Processing & Analysis**: Pandas, NumPy
- **Machine Learning & Modeling**: Scikit-Learn (`KMeans`, `AgglomerativeClustering`, `StandardScaler`, `silhouette_score`)
- **Data Visualization**: Plotly Express (3D Scatter plots, Bar charts), Matplotlib
- **Model Persistence**: Joblib (`.pkl` serialization)
- **Data Warehousing & SQL**: Relational Data Warehouse modeling, Star Schema DDL/DML, OLAP SQL Queries
- **Development Environment**: Jupyter Notebook, VS Code / Antigravity IDE
- **Version Control**: Git / GitHub

---

## 7. Project Architecture / Workflow
```
[Raw E-Commerce Dataset (Online Retail.xlsx)]
                    │
                    ▼
       [Data Preprocessing & Cleaning]
 (Filter null CustomerIDs, remove cancelled 'C' invoices & negative values)
                    │
                    ▼
     [Customer Feature Engineering & RFM]
  (Calculate Recency, Frequency, Monetary, AOV, Lifetime, Purchase Rate)
                    │
                    ▼
      [Log Transformation & Feature Scaling]
    (np.log1p skewness correction + StandardScaler normalization)
                    │
                    ▼
      [Unsupervised Customer Clustering]
  (K-Means vs. Agglomerative Hierarchical Clustering; Silhouette Evaluation)
                    │
                    ▼
       [Model & Result Serialization]
   (Save models: .pkl | Export datasets & summaries: .csv)
                    │
                    ▼
  [Data Warehouse Modeling & OLAP SQL Analysis]
 (Star Schema Architecture, Fact/Dimension Tables, OLAP Slicing & Dicing)
                    │
                    ▼
     [Business Insights & Executive Strategy]
```

---

## 8. Data Preprocessing
- **Missing Value Handling**: Removed 135,080 records with missing `CustomerID` to ensure accurate customer-level tracking.
- **Cancellation Filtering**: Excluded 9,288 cancelled transactions (Invoice numbers prefixed with 'C') and records with `Quantity <= 0` or `UnitPrice <= 0`.
- **Derived Attribute Calculation**: Created `SalesAmount = Quantity * UnitPrice` to quantify total spending per transaction line item.
- **Data Type Standardization**: Converted `InvoiceNo` to string format and parsed `InvoiceDate` into datetime objects.

---

## 9. Customer Analytics and RFM
Transactional data was grouped by `CustomerID` to construct behavioral metrics:
- **Recency ($R$)**: Days elapsed between the customer's last purchase date (`Last_Purchase`) and the reference snapshot date (`InvoiceDate.max() + 1 day`).
- **Frequency ($F$)**: Total number of unique orders placed by the customer (`InvoiceNo` count).
- **Monetary ($M$)**: Total cumulative monetary expenditure across all transactions (`SalesAmount` sum).
- **Additional Engineered Features**:
  - `Average_Order_Value (AOV)` = $\frac{\text{Monetary}}{\text{Frequency}}$
  - `Customer_Lifetime_Days` = $(\text{Last\_Purchase} - \text{First\_Purchase})\text{ in days}$
  - `Purchase_Rate` = $\frac{\text{Frequency}}{\text{Customer\_Lifetime\_Days} + 1}$
  - `Unique_Products` = Count of unique `StockCode` items purchased.

---

## 10. Customer Segmentation
Customer segmentation organizes the customer base into homogenous clusters sharing distinct purchasing frequency, recency, and monetary patterns. Unsupervised machine learning enables automated behavioral grouping without manual threshold heuristics.

---

## 11. K-Means and Agglomerative Clustering
### Data Preparation & Normalization
1. **Log Transformation**: Applied $\log(1 + x)$ (`np.log1p`) on `Recency`, `Frequency`, and `Monetary` features to reduce right-skewness and stabilize variance.
2. **Feature Scaling**: Scaled transformed features using `StandardScaler` ($\mu=0, \sigma=1$) to prevent monetary scale dominance during distance calculations.

### Model Evaluation & Comparison
Both K-Means and Agglomerative Hierarchical Clustering were evaluated across $K \in [2, 10]$ using the Elbow Method and Silhouette Score analysis. $K=2$ was identified as optimal.

| Model Algorithm | Silhouette Score | Status |
| :--- | :--- | :--- |
| **K-Means Clustering ($K=2$)** | **0.432827** (~0.4328) | **Selected Primary Model** |
| **Agglomerative Hierarchical Clustering ($K=2$)** | **0.404010** (~0.4040) | Secondary Model |

### K-Means Cluster Breakdown
- **Cluster 0 — Casual / At-Risk Customers**:
  - Customer Count: 2,672 (61.6% of customer base)
  - Avg Recency: 134.09 days
  - Avg Frequency: 1.67 orders
  - Avg Monetary Spend: \$495.59
  - Avg Order Value (AOV): \$320.20
- **Cluster 1 — High-Value / Champion Customers**:
  - Customer Count: 1,666 (38.4% of customer base)
  - Avg Recency: 25.89 days
  - Avg Frequency: 8.44 orders
  - Avg Monetary Spend: \$4,539.60
  - Avg Order Value (AOV): \$573.93

---

## 12. Data Warehouse Design
To support OLAP reporting and multi-dimensional analytical SQL queries, the project incorporates a **Star Schema Architecture**:

### Star Schema Structure
- **Fact Table**: `Fact_Sales`
  - Keys: `Sales_ID` (PK), `Customer_ID` (FK), `Product_ID` (FK), `Date_ID` (FK)
  - Measures: `Quantity`, `UnitPrice`, `Total_Amount`, `Discount_Amount`
- **Dimension Tables**:
  - `Dim_Customer`: `Customer_ID` (PK), `Country`, `Customer_Segment_Cluster`, `First_Purchase_Date`, `Recency_Days`
  - `Dim_Product`: `Product_ID` (PK), `StockCode`, `Description`, `UnitPrice`
  - `Dim_Time`: `Date_ID` (PK), `Full_Date`, `Day`, `Month`, `Quarter`, `Year`, `DayOfWeek`

---

## 13. ETL Process
- **Extract**: Ingestion of raw transactional data from e-commerce files (`Online Retail.xlsx`).
- **Transform**: Clean invalid records, perform RFM transformations, scale features, assign cluster segment labels (`Cluster` 0 or 1), and resolve surrogate keys.
- **Load**: Populate relational Data Warehouse dimension and fact tables for BI analysis.

---

## 14. OLAP Operations
The DW design supports four core OLAP operations:
1. **Roll-up**: Aggregating daily sales metrics to Monthly, Quarterly, and Yearly revenue totals.
2. **Drill-down**: Decomposing country-level sales into customer segment and individual product sales.
3. **Slice**: Isolating sales data for a specific cluster (e.g., analyzing Cluster 1 High-Value Champions).
4. **Dice**: Subsetting data across multiple dimensions simultaneously (e.g., Year 2011 $\times$ UK Region $\times$ Cluster 1).

---

## 15. Analytical SQL
Analytical SQL query categories designed for data warehouse exploration:
- Customer Lifetime Spend Ranking (`RANK() OVER (ORDER BY SUM(Total_Amount) DESC)`).
- Monthly Segment Revenue Trend Analysis (`GROUP BY Month, Cluster`).
- Churn Risk Analysis (`WHERE Recency > 90 AND Frequency < 2`).
- Product Co-purchase & High-Volume Category Metrics.

---

## 16. Sales Trend Analysis
- **Revenue Concentration**: 38.4% of customers (Cluster 1) generate over 85% of total sales revenue.
- **Recency Gap**: Cluster 0 exhibits an average recency of 134 days compared to 25.89 days for Cluster 1, indicating potential customer drop-off if unaddressed.
- **Seasonal Demand**: Higher transaction frequency observed towards Q4 (holiday shopping period).

---

## 17. Business Insights and Findings
1. **Champion VIP Retention**: Cluster 1 customers purchase frequently (8.44 orders avg) and spend \$4,539.60 avg. Priority should be given to exclusive loyalty programs and early product access.
2. **Re-engagement Campaign**: Cluster 0 customers make infrequent purchases (1.67 orders avg) with high recency. Targeted discount vouchers and win-back email campaigns can reactivate this group.
3. **Product Bundling**: Upselling products to Cluster 0 can increase Average Order Value from \$320.20 towards Cluster 1 levels (\$573.93).

---

## 18. Team Contributions

> [!IMPORTANT]
> This college case study project is completed by 3 team members, with responsibilities distributed across Data Engineering, Data Warehousing, and Business Analytics.

### Member 1 — Data & ETL Engineer *(Vaishnavi Parashar - Active Contributor)*
**Commit Title**: `Implemented Data Processing, RFM Analysis & Clustering`
- **Dataset Collection & Ingestion**: Loaded and audited 541,909 raw records from `Online Retail.xlsx`.
- **Data Cleaning & Filtering**: Removed null CustomerIDs, purchase cancellations ('C' invoices), and negative quantities/prices.
- **Feature Engineering & RFM Analysis**: Computed Customer-level RFM metrics (`Recency`, `Frequency`, `Monetary`), `Average_Order_Value`, `Customer_Lifetime_Days`, and `Purchase_Rate`.
- **Log Transformation & Feature Scaling**: Applied $\log(1+x)$ transform and `StandardScaler` to prepare normalized inputs.
- **Clustering Model Implementation**: Built and trained K-Means ($K=2$) and Agglomerative Hierarchical Clustering models.
- **Model Evaluation & Comparison**: Evaluated models using Silhouette Scores (K-Means: `0.4328` vs Agglomerative: `0.4040`).
- **Interactive Visualizations**: Created 3D cluster visualizations using Plotly Express (`px.scatter_3d`).
- **Model & Artifact Serialization**: Exported processed datasets (`customer_segmentation_results.csv`, `cluster_summary.csv`, `model_comparison.csv`) and saved binary model files (`.pkl` scaler and model artifacts).

### Member 2 — Data Warehouse & OLAP Engineer
- Data Warehouse Architecture & Star Schema Design (`Fact_Sales`, `Dim_Customer`, `Dim_Product`, `Dim_Time`).
- Primary Key and Foreign Key constraint definition and relational schema integrity.
- SQL ETL Staging & Data Transformation Scripts.
- Analytical SQL Query writing and OLAP Cubes (Roll-up, Drill-down, Slice, Dice).

### Member 3 — Data Mining & Business Analyst
- Business interpretation of customer segments (Cluster 0 Casuals vs. Cluster 1 Champions).
- Revenue breakdown and sales trend analysis across customer profiles.
- Strategic business recommendations, visual charts, executive presentation, and viva demonstration.

---

## 19. Project Folder Structure
```
E-Commerce-Data-Warehouse/
├── data/
│   ├── raw/
│   │   └── Online Retail.xlsx               # Raw transactional dataset (541,909 rows)
│   └── processed/
│       ├── customer_segmentation_results.csv # Customer-level RFM & cluster labels (4,338 records)
│       ├── cluster_summary.csv              # K-Means cluster mean profiles
│       └── model_comparison.csv             # Silhouette score evaluation summary
├── models/
│   ├── customer_scaler.pkl                  # Fitted StandardScaler model artifact
│   ├── customer_segmentation_kmeans.pkl     # Trained K-Means clustering model (K=2)
│   ├── customer_segmentation_hierarchical.pkl # Trained Agglomerative clustering model (K=2)
│   └── customer_segmentation_model.pkl      # Primary deployed segmentation model artifact
├── notebooks/
│   ├── 01_data_processing_rfm_clustering.ipynb # Clean, documented pipeline notebook (Member 1)
│   ├── case1.ipynb                          # Original execution notebook
│   └── sample.ipynb                         # Scratch notebook
├── sql/
│   └── README.md                            # Data Warehouse & SQL query documentation (Member 2)
├── docs/
│   └── DWDM_Case_Study_Overview.md          # Case study documentation & architecture overview
├── .gitignore                               # Git ignore configuration
└── README.md                                # Comprehensive project documentation
```

---

## 20. How to Run the Project
### Prerequisites
- Python 3.8+
- Jupyter Notebook or VS Code
- Git

### Installation Steps
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/Vaishnavi-Parashar/E-Commerce-Data-Warehouse.git
   cd E-Commerce-Data-Warehouse
   ```
2. **Install Required Python Packages**:
   ```bash
   pip install pandas numpy matplotlib scikit-learn plotly joblib openpyxl
   ```
3. **Execute the Jupyter Notebook**:
   ```bash
   jupyter notebook notebooks/01_data_processing_rfm_clustering.ipynb
   ```
4. **Load Saved Models in Python**:
   ```python
   import joblib

   scaler = joblib.load('models/customer_scaler.pkl')
   kmeans_model = joblib.load('models/customer_segmentation_kmeans.pkl')
   ```

---

## 21. Results / Visualizations
### Model Silhouette Score Comparison
| Model | Silhouette Score |
| :--- | :--- |
| **K-Means Clustering** | **0.4328** |
| **Agglomerative Clustering** | **0.4040** |

### Customer Cluster Summary (K-Means)
| Cluster ID | Customers | Avg Recency (Days) | Avg Frequency (Orders) | Avg Monetary ($) | Avg Order Value ($) | Segment Label |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Cluster 0** | 2,672 (61.6%) | 134.09 | 1.67 | \$495.59 | \$320.20 | Casual / At-Risk |
| **Cluster 1** | 1,666 (38.4%) | 25.89 | 8.44 | \$4,539.60 | \$573.93 | VIP Champions |

---

## 22. Conclusion
This case study successfully demonstrates the integration of data engineering, feature extraction, unsupervised machine learning, and data warehousing principles. By transforming raw retail transactions into structured RFM features and clustering customers into distinct segments (Casual vs. Champion), e-commerce businesses can transition from generic promotions to targeted, multi-dimensional business strategies supported by Data Warehouse Star Schemas and OLAP queries.

---

## 23. Future Scope
- **Real-Time Data Pipelines**: Integrating Apache Kafka and PySpark for real-time transaction streaming and dynamic cluster assignment.
- **API Deployment**: Packaging the trained scaler and K-Means model into a REST API using FastAPI/Flask for live prediction.
- **Predictive LTV Modeling**: Building supervised regression models to forecast Customer Lifetime Value (CLV).
- **Automated Marketing Integrations**: Triggering automated promotional emails via Webhooks when a customer transitions into the At-Risk segment.

---

## 24. References
1. UCI Machine Learning Repository: *Online Retail Data Set*.
2. Scikit-Learn Documentation: *K-Means and Hierarchical Clustering Algorithms*.
3. Ralph Kimball & Margy Ross: *The Data Warehouse Toolkit: The Definitive Guide to Dimensional Modeling*.
4. Arthur, D. and Vassilvitskii, S.: *k-means++: The Advantages of Careful Seeding*, SODA 2007.
