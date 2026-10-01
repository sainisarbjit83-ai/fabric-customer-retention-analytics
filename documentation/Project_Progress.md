\# Project Progress



\## Customer Retention \& Revenue Analytics



This project is an end-to-end Microsoft Fabric analytics solution for a fictional SaaS company that sells software through subscription plans.



The objective is to analyze customer retention, churn, subscriptions, and revenue.



\## Architecture



Source Data

→ Fabric Data Pipeline

→ Bronze Lakehouse

→ Silver Lakehouse

→ Gold Warehouse

→ Power BI Semantic Model

→ Power BI Report



\## Completed



\### 1. Source Data Profiling

\- Profiled Customers, Products, and Subscriptions using Excel.

\- Identified duplicate customers.

\- Identified missing City and MonthlyFee values.

\- Identified inconsistent capitalization and whitespace.

\- Validated CustomerID and ProductID relationships using XLOOKUP.

\- Validated subscription date and status rules.



\### 2. Bronze Layer

\- Created `LH\_Bronze`.

\- Created metadata-driven Fabric Data Pipeline: `PL\_Ingest\_Bronze`.

\- Used an array parameter and ForEach activity for file ingestion.

\- Used conditional logic to route CSV and Excel sources.

\- Loaded:

&#x20; - `Bronze\_Customers`

&#x20; - `Bronze\_Products`

&#x20; - `Bronze\_Subscriptions`

\- Bronze preserves the source data without business-rule modifications.



\### 3. Silver Layer



\#### Silver Customers

\- Created `DF\_Silver\_Customers`.

\- Removed duplicate CustomerIDs.

\- Standardized AcquisitionChannel.

\- Replaced missing City with `Unknown`.

\- Converted columns to appropriate data types.

\- Validated 1,000 unique customers.



\#### Silver Products

\- Created `DF\_Silver\_Products`.

\- Standardized ProductCategory.

\- Standardized PlanType.

\- Validated MonthlyPrice as numeric.

\- Loaded 30 products.



\#### Silver Subscriptions

\- Created `DF\_Silver\_Subscriptions`.

\- Standardized subscription status.

\- Trimmed identifier fields.

\- Converted dates and MonthlyFee to appropriate data types.

\- Validated EndDate against StartDate.

\- Created `EndDateValidation`.

\- Created `MonthlyFeeValidation`.

\- Created `StatusEndDateValidation`.

\- Validated 1,500 subscriptions and 1,500 unique SubscriptionIDs.

\- Validated that no records remain with EndDate earlier than StartDate.



\## Next Steps



\### 4. Gold Layer

\- Create Fabric Gold Warehouse.

\- Design dimensional/star schema.

\- Build customer, product, and date dimensions.

\- Build subscription and transaction fact tables.



\### 5. Power BI

\- Create semantic model.

\- Develop DAX measures.

\- Build customer retention, churn, and revenue dashboards.



\### 6. Final Validation

\- Validate Gold model.

\- Validate business metrics.

\- Document the complete solution.

