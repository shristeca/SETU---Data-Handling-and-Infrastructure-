# SETU---Data-Handling-and-Infrastructure-
## Milestone 1 - Data type and storage
### 1. Raw data storage - Where the Raw Data Will Live
The raw data will be stored in an object-storage-based data lake structure. For this project, the original CSV files downloaded from the Kaggle dataset will be stored in Google cloud storage in the raw layer. The files will remain unchanged to preserve the original source data and enable reproducibility.

The raw data will be stored in Google Cloud Storage (GCS) as part of the project's data lake architecture. The original CSV files obtained from Kaggle will be retained in their unchanged format within the Bronze (Raw) layer. Keeping the source files intact ensures reproducibility, supports auditing, and provides a reliable foundation for all downstream data processing, analytics, and machine learning activities.
Object storage is appropriate because:

The data consists of files rather than transactional records.
Raw data should remain immutable.
The same data can be reused for multiple AI models.


### 2. Processed data storage & file formats - Where Processed Data Will Be Stored and File Formats
After data cleaning and transformation, the processed data will be stored in the processed layer of the data lake in Parquet format within Google Cloud Storage (GCS). Parquet has been selected because it offers 
efficient data compression, 
faster query performance, and is optimized for analytical workloads.
supports columnar storage for faster analytical queries, 
integrates well with Apache Spark and 
is widely used in modern data engineering and machine learning workflows.

### 3.Database / object storage decision - database, object storage, file system, or other solution
For this project, object storage using a Data Lake architecture will be used instead of a relational database or a traditional file system.

Why not a database?

The project focuses primarily on:

Data ingestion
Data cleaning and transformation
Feature engineering
Machine learning model training

rather than transactional operations such as frequent inserts, updates, and deletes, which are the primary strengths of relational databases.

Why not a traditional file system?

Traditional file systems are suitable for storing files on a single machine or server but become difficult to manage as data volumes grow. They offer limited scalability, are not optimized for distributed data processing, and do not integrate as effectively with big data tools such as Apache Spark and cloud analytics platforms.

Why object storage?

Object storage provides:

Storage of large datasets at scale
Efficient processing with Apache Spark
Clear separation of raw and processed data
Easy dataset versioning and management
Cost-effective cloud storage
Integration with modern analytics and machine learning tools

### 4. Data versioning - how different versions of the data will be identified and tracked

### 5. Data access - how the system/code will access the data
The system will access data directly from Google Cloud Storage (GCS) using Apache Spark. Spark will read and write data using the GCS connector, while authentication will be managed through service account credentials. This approach enables secure, scalable, and efficient access to data throughout the pipeline.

### 6. Data split / validation strategy - clear and appropriate data-splitting strategy
A time-based train/dev/test split will be implemented using Apache Spark to prevent data leakage. Loans originated between 2015 and 2020 will be used for training, loans originated between 2021 and 2022 will be used for validation, and loans originated between 2023 and 2024 will be reserved for final testing. The split will be performed after feature engineering and the resulting datasets will be stored as versioned Parquet files. Additional model evaluation will be conducted across borrower sectors and macroeconomic stress scenarios to assess generalization under different business and economic conditions.

### 7,8. Feature description, data types and formats
The are 3 files that I have considered on using to train my prediction model.
The main files being the loan_portfolio.csv the supporting files will be macro_stress_scenarios.csv and portfolio_metrics.csv

#### loan_portfolio.csv
| Variable             | Description                      | N Sample Values                              | Datatype       |
| -------------------- | -------------------------------- | -------------------------------------------- | -------------- |
| loan\_id             | Unique loan identifier           | LN000123                                     | String         |
| origination\_date    | Loan origination date            | 2019-05-15                                   | Date           |
| maturity\_date       | Scheduled maturity date          | 2026-05-15                                   | Date           |
| maturity\_months     | Loan tenor in months             | 12, 36, 60, 120                              | Integer        |
| sector               | Borrower industry sector         | Technology, Energy, Retail                   | String         |
| loan\_type           | Type of loan                     | term\_loan, revolving, mortgage, bond, lease | String         |
| collateral           | Collateral status                | secured, unsecured, partially\_secured       | String         |
| initial\_rating      | Credit rating at origination     | AAA, AA, A, BBB, BB, B, CCC                  | String         |
| credit\_score        | Borrower credit score            | 300-850                                      | Integer        |
| ead                  | Exposure at Default              | 1,250,000                                    | Decimal/Double |
| coupon\_rate         | Loan interest rate (%)           | 4.5, 7.2                                     | Decimal        |
| leverage             | Debt / EBITDA ratio              | 2.3, 4.1                                     | Decimal        |
| interest\_coverage   | EBIT / Interest Expense          | 3.5, 6.2                                     | Decimal        |
| debt\_to\_equity     | Debt-to-Equity ratio             | 0.8, 2.1                                     | Decimal        |
| pd\_annual           | Annual Probability of Default    | 0.015 = 1.5%                                 | Decimal        |
| lgd                  | Loss Given Default               | 0.45 = 45%                                   | Decimal        |
| el                   | Expected Loss                    | PD × LGD × EAD                               | Decimal/Double |
| unexpected\_loss     | Unexpected Loss measure          | Calculated risk metric                       | Decimal/Double |
| rwa                  | Risk Weighted Assets             | 750000                                       | Decimal/Double |
| defaulted            | Loan default flag                | 0 = No, 1 = Yes                              | Integer        |
| default\_date        | Date of default                  | 2022-07-10, NULL                             | Date/Null      |
| survival\_months     | Months until default or maturity | 24, 60                                       | Integer        |
| recovery\_rate       | Recovery rate after default      | 0.55 = 55%                                   | Decimal        |
| loss\_given\_default | Actual loss amount               | LGD × EAD                                    | Decimal/Double |


#### macro_stress_scenarios
| Variable               | Description                                                     | Notes / Comments                                        | Datatype         |
| ---------------------- | --------------------------------------------------------------- | ------------------------------------------------------- | ---------------- |
| scenario               | Stress testing scenario name                                    | baseline, mild, adverse, severe, gfc\_like, covid\_like | String           |
| gdp\_shock\_pp         | GDP growth shock (percentage points) applied under the scenario | 0, -1.5, -3.5, -6.0                                     | Decimal          |
| unemp\_shock\_pp       | Unemployment rate shock (percentage points)                     | 0, 1.5, 3.0, 5.5                                        | Decimal          |
| rate\_shock\_pp        | Interest rate shock (percentage points)                         | 0, 0.5, 1.5, 3.0                                        | Decimal          |
| credit\_spread\_bps    | Credit spread shock in basis points                             | 0, 50, 150, 300                                         | Integer          |
| sector                 | Industry sector to which the stress factors apply               | Financials, Energy, Retail, Technology                  | String           |
| base\_pd               | Baseline Probability of Default before stress is applied        | 0.022308 = 2.23%                                        | Decimal          |
| stressed\_pd           | Probability of Default after stress is applied                  | 0.028500 = 2.85%                                        | Decimal          |
| pd\_uplift\_pp         | Increase in PD caused by the scenario (percentage points)       | 0 = no increase                                         | Decimal          |
| pd\_multiplier         | Multiplier applied to baseline PD                               | 1 = no change, 1.5 = 50% increase                       | Decimal          |
| base\_lgd              | Baseline Loss Given Default                                     | 0.45 = 45%                                              | Decimal          |
| stressed\_lgd          | Loss Given Default after stress scenario                        | 0.45 = 45%, 0.55 = 55%                                  | Decimal          |
| total\_ead             | Total Exposure at Default for the sector                        | 3.02E+10 = €30.2 billion                                | Decimal / Double |
| expected\_loss\_base   | Expected Loss under baseline conditions                         | 3.16E+08 = €316 million                                 | Decimal / Double |
| expected\_loss\_stress | Expected Loss under stressed conditions                         | 3.03E+08 = €303 million                                 | Decimal / Double |
| el\_increase\_pct      | Percentage change in expected loss due to stress                | -4.2, 12.5, 35.8                                        | Decimal          |

#### portfolio_metrics.csv
| Variable            | Description                                               | Notes / Comments | Datatype |
| ------------------- | --------------------------------------------------------- | ---------------- | -------- |
| date                | Monthly reporting date                                    | 01/01/2015       | Date     |
| n\_active\_loans    | Number of active loans in the portfolio                   | 482 loans        | Integer  |
| total\_ead          | Total Exposure at Default of the portfolio                | 1,697,995,934    | Double   |
| total\_el           | Total Expected Loss across all loans                      | 25,747,639.48    | Double   |
| total\_rwa          | Total Risk Weighted Assets                                | 341,156,224      | Double   |
| el\_rate            | Expected Loss as a percentage of portfolio exposure       | 0.015164 = 1.52% | Decimal  |
| avg\_pd             | Average Probability of Default across the portfolio       | 0.023472 = 2.35% | Decimal  |
| avg\_lgd            | Average Loss Given Default across the portfolio           | 0.5378 = 53.78%  | Decimal  |
| var\_99             | Value at Risk at the 99% confidence level                 | 59,991,999.99    | Double   |
| cvar\_995           | Conditional Value at Risk at the 99.5% confidence level   | 75,440,583.68    | Double   |
| sector\_hhi         | Herfindahl-Hirschman Index measuring sector concentration | 0.1312           | Decimal  |
| new\_defaults       | Number of new defaults during the reporting period        | 0, 1, 5, etc.    | Integer  |
| gdp\_growth         | GDP growth rate used in the model                         | 2.591 = 2.59%    | Decimal  |
| unemployment        | Unemployment rate                                         | 5.319 = 5.32%    | Decimal  |
| policy\_rate        | Central bank policy interest rate                         | 3 = 3%           | Decimal  |
| credit\_spread\_bps | Credit spread measured in basis points                    | 154 bps          | Integer  |

### Reproducibility of Data Collection and Preprocessing
The data used in this project originates from the Kaggle dataset "Credit Risk Dataset – 50K Loans, 10 Sectors", which contains five CSV files. The dataset can be reproduced by downloading the files from Kaggle and uploading them unchanged in a Google Cloud Storage (GCS) raw-data bucket (gs://credit-risk-raw/) to preserve the original source data and ensure reproducibility.
To reproduce the processed dataset, Apache Spark will execute a documented preprocessing pipeline. The pipeline will load the raw CSV files from GCS, validate data types, handle missing values and duplicates, standardize categorical fields, convert the data to Parquet format, and store the results in a curated GCS bucket (gs://credit-risk-curated/). Relevant datasets will then be joined, engineered features will be created, and a time-based train/validation/test split (2015-2020, 2021-2022, 2023-2024) will be applied to prevent data leakage. The final feature datasets will be stored as versioned Parquet files in a features bucket (gs://credit-risk-features/). All Spark scripts and transformation logic will be maintained and well documented, allowing another user to reproduce the complete data pipeline from the original Kaggle source files to the final machine learning datasets.
