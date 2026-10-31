# Data Handling and Infrastructure
## Milestone 2 - Explortaory Data Analysis
### Project definition
**Title** - Predicting Loan Default Probability and Portfolio Expected Loss Under Alternative Economic Scenarios

**Problem Statement** - What is the probability that a loan defaults and how it varies across different economic conditions and sectors, and what is the resulting portfolio expected loss?

**Objective** - The objective of this project is to develop a machine learning classification model that estimates the probability of default (PD) for individual loans using borrower and loan characteristics. These Probabilities will then be used to calculate the average PD's across sectors under different economic conditions and Portfolio Expected Loss, supporting credit risk management and lending decisions.

**Intended user/Business settings** - This project is designed for banks, lending institutions, credit risk analysts, and portfolio managers who need to assess credit risk and make informed lending decisions.

**Analytical/ML Task** 

_Primary Task_ - Binary Classification

The primary objective is to develop a machine learning model that predicts whether a loan will default or not.

_Secondary Task_

Estimate expected portfolio loss using the predicted default risk for various sectors.

### Data Source

**URL** - The dataset used in this project objected from  Kaggle: Credit Risk Dataset: 50K Loans, 10 Sectors by Sergio Nefedov.
URL: https://www.kaggle.com/datasets/sergionefedov/credit-risk-dataset-50k-loans-10-sectors.

**Licence and Permissions** - The dataset is publicly available through Kaggle and is used solely for academic and educational purposes in accordance with Kaggle's terms of use and the dataset licence. As the datadet is fully synthetic, it does not contain any real customer information or personally identifiable information (PII) hence the risk of privacy breaches is minimal.

**Data Collection/Acquisition/Storage** - The dataset is a synthetic financial dataset containing approximately 50,000 loans across 10 industry sectors. It consists of 7 CSV files covering various loan characteristics and risk metrics. According to Kaggle's descritpion, the data was specifically designed to support credit risk analysis, machine learning model development, stress testing, and portfolio risk assessment.
All 7 CSV files were directly downloaded from Kaggle using the Kaggle Dataset API. Raw copies of the files are stored in Google Cloud Storage (GCS) for backup and reproducibility and also into Google BigQuery to support data exploration, querying, and pipeline development.

**Bias and Ethical Considerations** - As this is a synthetic dataset, it may not fully reflect real-world lending behaviour and could contain biases that can include Sector-specific default patterns that may not accurately represent real-world markets, Class imbalance between defaulted and non-defaulted loans, Simplified or overly complicated relationships between various conditions and default behaviour. As a result, model predictions should be interpreted carefully and used as a decision-support tool rather than the sole basis for lending decisions. Regular evaluation is important to ensure the model remains fair, transparent, and free from unintended bias.

### Data Description
As mentioned above the dataset is a collection of 7 csv files with 50,000 loans across 10 different sector and all files contains various information about the loans.

| Dataset | Rows | Description | General Purpose |
|----------|---------:|-------------|----------------|
| **loan_portfolio.csv** | 50,000 | Individual loan records containing PD, LGD, EAD, default outcomes, and borrower/loan characteristics. | Supports loan-level credit risk analysis, default prediction, and expected loss estimation. |
| **credit_ratings.csv** | 17,939 | Annual credit rating migrations for 2,000 issuers from 2015 to 2024. | Tracks changes in credit quality over time and enables rating transition analysis. |
| **macro_stress_scenarios.csv** | 60 | Probability of default (PD) uplifts across 6 economic scenarios and 10 sectors. | Supports stress testing and the assessment of sector-specific impacts under different economic conditions. |
| **portfolio_metrics.csv** | 120 | Monthly portfolio-level metrics including EAD, Expected Loss, VaR, CVaR, and concentration measures. | Provides portfolio performance and risk monitoring information. |
| **vintage_analysis.csv** | 2,160 | Cumulative default curves by origination quarter and Months-on-Book (MOB 1–60). | Supports vintage analysis and examination of how default rates evolve over a loan's lifecycle. |


The primary dataset used for exploratory analysis and machine learning model development is loan_portfolio.csv, as it contains the target variable (defaulted) and the key borrower and loan characteristics required for prediction. 
