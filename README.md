# SETU---Data-Handling-and-Infrastructure-
## Milestone 1 - Data type and storage
### Raw data storage - Where the Raw Data Will Live
The data collected from [Kaggle](https://www.kaggle.com/datasets/sergionefedov/credit-risk-dataset-50k-loans-10-sectors/data) is stored in a Google Cloud Storage (GCS) bucket as raw source files. These files are kept exactly as they were originally downloaded and are never edited or overwritten. This ensures that there is always a trustworthy copy of the original data available when needed. The raw files are also loaded into BigQuery, allowing the data to be queried and analyzed efficiently while still preserving the original version. Keeping an immutable raw data layer helps maintain data quality, supports reproducibility, and makes it easier to reuse the same dataset for future analytics, AI, and machine learning projects without having to collect the data again.
<img width="876" height="268" alt="image" src="https://github.com/user-attachments/assets/5123e404-88e1-4dbb-ac9e-6255bf9b7ecd" />

### Processed data storage & file formats - Where Processed Data Will Be Stored and File Formats
The raw data will be analyzed and processed in BigQuery using SQL to join the relevant tables and select the columns required for this project. The processed dataset will then be exported and stored separately in Google Cloud Storage (GCS) as Parquet files using Python. As the dataset contains a large number of columns, additional analysis is required to determine the most relevant features, and screenshots of the proposed processing approach have been attached for reference.
**Big Query for Analysis**
<img width="941" height="405" alt="image" src="https://github.com/user-attachments/assets/79dd852e-74c5-43bd-8635-d015438d5cda" />
**Sample code on how to store the processed data in GCS as Parquet file**
<img width="550" height="305" alt="image" src="https://github.com/user-attachments/assets/1a982440-2d6b-4a5d-9a77-7ed4689778be" />
<img width="457" height="292" alt="image" src="https://github.com/user-attachments/assets/48d88837-290c-4bb8-9497-83a06ad4ab4c" />



### 3.Database / object storage decision - database, object storage, file system, or other solution
A data lake approach was chosen for this project to keep the data flexible and easy to work with as it moves through different stages of analysis and preparation. Google Cloud Storage (GCS) is used to store both the raw and processed datasets because it provides reliable, scalable, and cost-effective storage for large files. BigQuery is used for data exploration and SQL-based processing, allowing the required tables to be joined and transformed without the need to manage database infrastructure. The processed data is then stored as Parquet files, which take up less storage space and provide faster performance for analytics and machine learning tasks compared to CSV files. This approach is more scalable and easier to manage than storing and processing the data on a traditional local file system.

### 4. Data versioning - how different versions of the data will be identified and tracked

### 5. Data access - how the system/code will access the data
Python acted as the integration layer to move data between Kaggle, Google Cloud Storage (GCS), and BigQuery. The dataset was downloaded directly from Kaggle using the kagglehub library.
<img width="649" height="160" alt="image" src="https://github.com/user-attachments/assets/215be3a6-15bc-4a1f-a132-924ad07e4ca5" />

Access to Google Cloud services was authenticated through the Google Colab environment using the project's Google Cloud credentials. The google-cloud-storage and google-cloud-bigquery Python libraries were used to interact with GCS and BigQuery. Using these libraries, a GCS bucket was created, the raw data files were uploaded to cloud storage, and the datasets were loaded into BigQuery for analysis and SQL-based processing. The google-cloud-storage and google-cloud-bigquery Python libraries were used to interact with GCS and BigQuery. Using these libraries, a GCS bucket was created, the raw data files were uploaded to cloud storage, and the datasets were loaded into BigQuery for analysis and SQL-based processing.

<img width="510" height="269" alt="image" src="https://github.com/user-attachments/assets/74b92aa4-340d-44f8-b6fe-b8a1058696c2" />


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



