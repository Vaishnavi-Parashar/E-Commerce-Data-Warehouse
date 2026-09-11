# E-Commerce Data Warehouse: Customer Behaviour, Sales & Analysis

## Case Study Overview
This academic case study explores customer purchase behavior, RFM feature engineering, and unsupervised customer segmentation for an online retail dataset containing over 500,000 transaction records.

### Key Components:
1. **Data Engineering & ETL Pipeline** (Member 1)
   - Raw transaction processing, missing value removal, transaction value calculation.
   - RFM (Recency, Frequency, Monetary) metric construction.
   - Log transformation ($\log(1+x)$) and `StandardScaler` feature scaling.
   - Clustering algorithms: K-Means ($K=2$, Silhouette: $0.4328$) and Agglomerative Hierarchical Clustering ($K=2$, Silhouette: $0.4040$).

2. **Data Warehouse Architecture** (Member 2)
   - Conceptual Star Schema design featuring `Fact_Sales` and dimensions (`Dim_Customer`, `Dim_Product`, `Dim_Time`).
   - Staging table structures and analytical SQL queries.

3. **Data Mining & Business Analytics** (Member 3)
   - Behavioral interpretation of Customer Clusters:
     - **Cluster 0 (61.6%)**: Casual / At-Risk Customers ($\text{Recency}=134.09\text{ days}$, $\text{Frequency}=1.67\text{ orders}$, $\text{Monetary}=\$495.59$).
     - **Cluster 1 (38.4%)**: High-Value Champion Customers ($\text{Recency}=25.89\text{ days}$, $\text{Frequency}=8.44\text{ orders}$, $\text{Monetary}=\$4,539.60$).
   - Retention and loyalty growth strategies.
