Customer Retention & Revenue Analytics
An end-to-end Microsoft Fabric analytics project for a fictional subscription-based software company. The solution ingests raw customer, product, and subscription data, profiles and validates it, and moves it through a medallion architecture (Bronze, Silver, Gold) to support customer retention, churn, and revenue reporting in Power BI.
Portfolio / demo project. All data is fictional and was created for learning and demonstration purposes. See the Disclaimer.

Business Objective
Subscription businesses depend on keeping customers. This project aims to give stakeholders a reliable, well-modeled view of:
* Customer and subscription retention
* Subscription churn and its drivers
* Revenue from active subscriptions and revenue lost to churn
* Product and customer-segment performance
A key design goal is trustworthy data: source data contains intentionally introduced quality issues, and the pipeline is designed to detect and flag them transparently rather than silently modifying them.

Key Business Questions
1. How many active customers and subscriptions are there?
2. What is the churn rate, and how does it trend over time?
3. Which customer segments have higher churn?
4. Which acquisition channels have higher churn?
5. How does churn vary by product?
6. How much revenue is associated with active subscriptions?
7. How much revenue is lost through customer churn?
8. What customer and product patterns help explain retention?

Solution Architecture
flowchart TD
    A["CSV<br/>(Customers, Subscriptions)"] --> D
    B["Excel<br/>(Products)"] --> D
    C["SQL Server<br/>(Transactions - planned)"] -.-> D

    D["Fabric Data Pipeline<br/>PL_Ingest_Bronze"] --> E
    E["Bronze Lakehouse<br/>LH_Bronze"] --> F
    F["Silver Lakehouse<br/>LH_Silver<br/>(Dataflows Gen2)"] --> G
    G["Gold Warehouse<br/>(planned)"] -.-> H
    H["Power BI Semantic Model<br/>(planned)"] -.-> I
    I["Power BI Report<br/>(planned)"]

    classDef done fill:#d4edda,stroke:#28a745,color:#000
    classDef planned fill:#f8f9fa,stroke:#6c757d,stroke-dasharray: 5 5,color:#000

    class A,B,D,E,F done
    class C,G,H,I planned
Solid boxes are completed; dashed boxes are planned.

Technology Stack
AreaTechnologyStatusData platformMicrosoft FabricIn useIngestionFabric Data Pipelines (ForEach, If Condition, HTTP connector)In useStorageFabric Lakehouse (Bronze, Silver)In useTransformationDataflows Gen2 / Power QueryIn useValidationSQL (Lakehouse SQL analytics endpoint)In useData profilingMicrosoft Excel (including XLOOKUP)In useVersion controlGit and GitHubIn useDimensional modelFabric Warehouse (T-SQL)PlannedSource databaseSQL ServerPlannedReportingPower BI semantic model, DAX, Power BI reportPlanned
Data Sources
Current sources
DatasetFormatRowsBronze tableCustomersCSV1,005Bronze_CustomersProductsExcel30Bronze_ProductsSubscriptionsCSV1,500Bronze_SubscriptionsPlanned source
DatasetFormatStatusTransactionsSQL ServerPlanned for a later phaseThe source data intentionally contains data-quality issues so that profiling, cleansing, and validation can be demonstrated.

Data Profiling & Data Quality
Before building any transformations, each dataset was profiled in Excel to understand its structure and identify issues.
Customers
* 1,005 source rows, but only 1,000 unique customers (duplicate CustomerID records)
* One missing City value
* AcquisitionChannel had inconsistent capitalization and whitespace
Products
* 30 products
* Inconsistent capitalization in ProductCategory and PlanType
* MonthlyPrice profiled as a numeric/currency field
Subscriptions
* 1,500 records
* CustomerID and ProductID relationships validated with XLOOKUP: no unmatched customers or products
* 1,090 blank EndDate values, associated with active subscriptions
* One subscription with EndDate earlier than StartDate
* One subscription with a missing MonthlyFee
* One lowercase Status value
* Active subscriptions with a populated EndDate identified as a business-rule issue

Medallion Architecture
LayerPurposeFabric itemStatusBronzeRaw ingestion. Preserves source data exactly as received, including quality issues.LH_Bronze (Lakehouse)CompletedSilverCleansed, standardized, and typed data with explicit data-quality validation flags.LH_Silver (Lakehouse)CompletedGoldBusiness-ready dimensional (star schema) model for reporting.Fabric WarehousePlanned
Bronze Layer
Status: Completed
* Lakehouse: LH_Bronze
* Pipeline: PL_Ingest_Bronze
The Bronze pipeline uses a metadata-driven ingestion pattern so that new files can be added through configuration rather than new activities:
* A pipeline array parameter holds the list of source files and their metadata
* A ForEach activity iterates over each entry
* An If Condition activity routes CSV and Excel files to the appropriate copy logic
* Dynamic source relative URLs and dynamic destination table names are built from the parameter values
* The HTTP connector retrieves the source files from GitHub
* Automatic column mapping is used for the copy
* Tables are loaded with overwrite (full load) in the current implementation
Output tables: Bronze_Customers, Bronze_Products, Bronze_Subscriptions
The Bronze layer intentionally preserves source data and its quality issues so that all downstream changes are traceable.

Silver Layer
Status: Completed
* Lakehouse: LH_Silver
* Transformations built with Dataflows Gen2 (Power Query)
Silver_Customers (DF_Silver_Customers)
* Removed duplicate CustomerID records (1,005 rows reduced to 1,000)
* Standardized AcquisitionChannel
* Replaced the missing City value with "Unknown"
* Converted Age to Whole Number and SignupDate to Date
* SQL validation: 1,000 rows and 1,000 unique customers confirmed
Silver_Products (DF_Silver_Products)
* Standardized ProductCategory and PlanType
* Converted MonthlyPrice to an appropriate numeric/currency type
* Validation: 30 product records confirmed
Silver_Subscriptions (DF_Silver_Subscriptions)
* Standardized Status values
* Trimmed SubscriptionID, CustomerID, and ProductID
* Converted StartDate and EndDate to Date and MonthlyFee to Currency
* Added CleanEndDate to handle the record where EndDate is earlier than StartDate
* Added validation columns: EndDateValidation, MonthlyFeeValidation, StatusEndDateValidation
* SQL validation: 1,500 subscription records confirmed

Data Quality Rules
The Silver layer flags data-quality problems in dedicated validation columns instead of silently modifying or removing records. This keeps issues visible and auditable for downstream decisions.
EndDateValidation
ConditionResultEndDate is blankOpenEndDate earlier than StartDateInvalidOtherwiseValidMonthlyFeeValidation
ConditionResultMonthlyFee is missingMissing MonthlyFeeMonthlyFee <= 0Invalid MonthlyFeeOtherwiseValidStatusEndDateValidation
ConditionResultStatus = Active and EndDate is populatedInvalidStatus = Churned and EndDate is blankInvalidOtherwiseValid
Gold Layer
Status: Planned / Next Phase
The Gold layer will be built in a Fabric Warehouse as a dimensional star schema:
* DimCustomer
* DimProduct
* DimDate
* FactSubscription
* FactTransaction (after SQL Server ingestion)
Planned work includes defining primary/business keys and relationships, and applying SQL transformations and business logic for reporting.

Power BI Analytics
Status: Planned / Next Phase
* Build a Power BI semantic model on top of the Gold layer and define relationships
* Create DAX measures for: 
o Active customers
o Churned customers
o Churn rate
o Revenue
o Recurring revenue
o Revenue lost to churn
o Customer and product analysis
* Build an executive / customer-retention dashboard answering the key business questions

Repository Structure
fabric-customer-retention-analytics/
??? data/            # Source datasets (CSV / Excel)
??? sql/             # SQL scripts
??? fabric/          # Microsoft Fabric artifacts
??? powerbi/         # Power BI assets (planned phase)
??? documentation/   # Project progress documentation
??? README.md

Git / Version Control
The project is version-controlled in GitHub. Work so far includes setting up the repository and folder structure, maintaining project progress documentation, and adding Fabric screenshots. A standard Git workflow is used: status, add, commit, pull --rebase, and push.

Project Status
PhaseStatusGitHub setup and documentationCompletedData profilingCompletedBronze layerCompletedSilver layerCompletedGold layer (Warehouse star schema)PlannedSQL Server transactions ingestionPlannedPower BI semantic model and reportPlannedMonitoring, lineage, and business definitions documentationPlanned
Interview Talking Points
* Metadata-driven ingestion: One Fabric pipeline uses an array parameter, ForEach, If Condition, and dynamic paths/table names to load multiple CSV and Excel sources without hard-coding each file.
* Medallion architecture: Why Bronze preserves raw data (traceability, reprocessing) and Silver holds cleansed, typed, validated data.
* Profiling before building: Using Excel profiling and XLOOKUP referential checks to find duplicates, missing values, inconsistent categories, and business-rule violations up front.
* Validation over silent fixes: Flagging issues in validation columns (EndDateValidation, MonthlyFeeValidation, StatusEndDateValidation) so data problems remain visible and auditable.
* Power Query transformations: Deduplication, standardization, trimming, null handling, and data type conversion in Dataflows Gen2.
* SQL validation: Confirming row counts and key uniqueness after each Silver load.
* Design trade-offs: Current full-load overwrite approach and how incremental loading will be explored with the SQL Server source.
* Git/GitHub workflow: Versioning project files and documentation, including rebasing before pushing.
.

