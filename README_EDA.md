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
```
import kagglehub

path = kagglehub.dataset_download("sergionefedov/credit-risk-dataset-50k-loans-10-sectors")
```

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
| **payments_history.csv** | 1,212,815 | Historical repayment records for individual loans, including payment timing and status. | Supports repayment behaviour analysis and delinquency trend monitoring. |

In line with the project objectives, loan_portfolio.csv will the primary dataset used for EDA and machine learning model development because it contains the target variable (defaulted) and the predictor variables required for default classification. The secondary analysis is supported by macro_stress_scenarios.csv, which facilitates stress testing under different economic conditions, and portfolio_metrics.csv, which provides portfolio-level risk and loss measures for performance evaluation.
The remaining datasets, payments_history.csv, credit_ratings.csv, and vintage_analysis.csv, provide additional information on repayment behaviour, credit rating migrations, and loan performance trends, respectively. However, they are outside the scope of the current phase of the project, which focuses on loan-level default prediction and portfolio risk assessment.

**Data Types**

The dataset contains a mixture of:

Numerical variables: 'maturity_months', 'credit_score', 'ead', 'coupon_rate', 'leverage', 'interest_coverage', 'debt_to_equity', 'pd_annual', 'lgd', 'el', 'unexpected_loss', 'rwa', 'defaulted', 'survival_months', 'recovery_rate', 'loss_given_default'

Categorical variables: 'loan_id', 'maturity_date', 'sector', 'loan_type', 'collateral', 'initial_rating'

Date variables: 'origination_date', 'default_date'

```
print(loan_df.info())

num_cols = loan_df.select_dtypes(include=['int64','float64']).columns

cat_cols = loan_df.select_dtypes(include=['object','category']).columns

date_cols = loan_df.select_dtypes(include=['datetime64']).columns
```

**Target Variable**
```
loan_df['defaulted'].value_counts()
```

The target variable is defaulted, which indicates whether a loan has defaulted.

0 = Non-Defaulted Loan
1 = Defaulted Loan

This makes the problem a binary classification task.

**Time Range**

```
loan_df['origination_year'] = loan_df['origination_date'].dt.year
loan_df[['origination_date', 'origination_year']].head()
```

The dataset contains loan records originating between Jan 2015 and Dec 2023, based on the origination_date field.

**Class Balance**

```loan_df['defaulted'].value_counts(normalize=True)*100```

The target variable is distributed as follows:

Non-Defaulted Loans: 86.1%
Defaulted Loans: 13.9%

This distribution will be assessed to determine whether class imbalance techniques are required during model development.

**Missingness**

```loan_df.isnull().sum()```

Missing value analysis was performed across all variables across the 3 datasets and no significant missing values were identified in the dataset.

**Known Limitations**

- The dataset is synthetic and may not fully capture real-world lending behaviour.
- Relationships between borrower characteristics and default risk are based on simulated assumptions.
- The dataset may not reflect all economic, regulatory, or market conditions observed in actual loan portfolios.
- Results should therefore be interpreted as a demonstration of machine learning techniques rather than a direct representation of real-world credit risk.

### Exploratory Data Analysis

**Removal of Leakage Columns**

A quick review was conducted on the columns in Loan portfolio dataset to identify variables that would not be available at the time of loan origination. The guiding question was:

"Would this information be known on the day the loan is approved?"

The below variables were removed from the modelling dataset as they contain information available only after a loan gets defaulted. Retaining these variables could introduce data leakage, causing the model to learn information that would not be available in a real-world prediction scenario.

```
remove_cols = [
    'pd_annual',
    'el',
    'unexpected_loss',
    'loss_given_default',
    'default_date',
    'survival_months',
    'rwa',
    'recovery_rate'
]
loan_df = loan_df.drop(columns=remove_cols)
```


**Target Variable Analysis**

#### Defaulted vs Non-Defaulted loans

<img width="589" height="455" alt="image" src="https://github.com/user-attachments/assets/c9938e63-efb7-41f4-99e8-907a80a1cddb" />

The majority of loans are non-defaulted, while a smaller proportion have defaulted. This indicates that loan default is a relatively infrequent event in the portfolio.

**Numerical Feature Analysis**

#### Descriptive Statistics

<img width="664" height="215" alt="image" src="https://github.com/user-attachments/assets/2ea8c21a-8064-4359-ac20-1624aa8a8569" />

#### Histogram

<img width="1489" height="990" alt="image" src="https://github.com/user-attachments/assets/33f2bc93-81c5-4717-b3a0-52d78fd5af54" />

Based on the statistics and Histogram we have the below findings:

#### Maturity Months

Loan terms range from 12 to 120 months, with an average maturity of approximately 54 months. Most loans are medium-term, while a small number of long-term loans increase the average.

#### Credit Score

Credit scores range from 518 to 850. The mean and median are almost identical, indicating a balanced and approximately symmetric distribution of borrower credit quality.

#### EAD

EAD values vary considerably across the portfolio, with a small number of loans having exceptionally high exposure amounts compared to the majority of loans.

#### Coupon Rate

Coupon rates are generally concentrated around the average value, suggesting that most loans are priced within a moderate interest rate range.

#### Leverage

Most borrowers maintain moderate leverage levels, while a smaller group of borrowers exhibits substantially higher leverage and potentially higher financial risk.

#### Interest Coverage

Most borrowers appear capable of meeting their interest payment obligations, while a few borrowers demonstrate exceptionally strong repayment capacity.

#### Debt-to-Equity

Debt-to-equity ratios vary across the portfolio. While most borrowers have moderate debt levels, some rely heavily on debt financing, increasing their financial risk.

#### Overall Observation

Credit Score and Coupon Rate appear relatively well distributed, whereas EAD, Leverage, Interest Coverage and Debt-to-Equity contain more extreme values, indicating greater variation in borrower risk profiles.

### Skewness Analysis

```loan_df[num_cols].skew().round(2).sort_values(ascending=False)```

EAD, Debt-to-Equity and Interest Coverage are strongly right-skewed, while Credit Score and Coupon Rate are nearly symmetric.

### Correlation Analysis

<img width="1019" height="792" alt="image" src="https://github.com/user-attachments/assets/409d865a-1441-49ab-b111-e026109e23de" />

Most variables have weak correlations with each other. Credit Score has the strongest relationship with default, with lower scores being associated with higher default rates.

### Numerical Columns Vs Target 

**Credit Score vs Default**

<img width="562" height="557" alt="image" src="https://github.com/user-attachments/assets/8aff2c65-aae9-43a5-9ff3-520ee02ef2f3" />

More than 30% of defaulted loans fall into the lowest credit score group.

**Coupon Rate vs Default**

<img width="458" height="388" alt="image" src="https://github.com/user-attachments/assets/3b49424e-3e92-40fe-94d7-b838be572ec7" />

Defaulted loans generally have higher interest rates than non-defaulted loans.

**Maturity vs Default**

<img width="571" height="455" alt="image" src="https://github.com/user-attachments/assets/af6cde40-431a-4f0c-854e-18eade3f6820" />

Default rates increase as loan maturity increases.

### Categorical Column Analaysis

**Sector Analysis**

<img width="626" height="455" alt="image" src="https://github.com/user-attachments/assets/b2a60735-8095-4b71-b7ad-2d8d32253c91" />

Loans are evenly distributed across all sectors, and default rates are relatively similar.

**Rating Analysis**

<img width="589" height="471" alt="image" src="https://github.com/user-attachments/assets/dd31413b-da30-41ad-90ac-3a8ac53d6aa2" />

<img width="846" height="485" alt="image" src="https://github.com/user-attachments/assets/d53afdea-05d1-4709-b17b-06364b5f98df" />

BBB is the most common rating, while CCC has the highest number of defaults. Default frequency increases as rating quality decreases.

**Loan Type Analysis**

<img width="589" height="500" alt="image" src="https://github.com/user-attachments/assets/0ff4878b-27ea-44c9-bcee-bf578c2880d6" />

Most loans are term loans. Default rates are fairly similar across loan types.

**Collateral Analysis**

<img width="589" height="455" alt="image" src="https://github.com/user-attachments/assets/69d689f7-c17c-4ac0-9144-0ebe6fe9b23e" />

The portfolio contains more secured loans, but default rates appear similar for both secured and unsecured loans.

### Date Variable analysis

**Loan Originations by Year**

<img width="580" height="476" alt="image" src="https://github.com/user-attachments/assets/b2fd78b2-2636-4817-ad1d-5ec195722525" />

The number of loans remains fairly consistent between 2015 and 2023.

**Default Rate based on Origination Year**

<img width="576" height="476" alt="image" src="https://github.com/user-attachments/assets/40f668cb-b25a-4382-996f-c0171a8ed661" />

Loans originated in 2020 show the highest default rate.

**Borrower Quality Over Time**

<img width="565" height="438" alt="image" src="https://github.com/user-attachments/assets/182d10e1-1802-487b-85d0-871f4f75cf17" />

Average credit scores remain close to 715 across all years.

**Portfolio Composition Over Time**

<img width="1190" height="490" alt="image" src="https://github.com/user-attachments/assets/37abc940-f156-484f-ae29-407928e3a8ce" />

The proportion of loans in each sector remains largely unchanged over time.

**Implications**

The EDA provided several useful insights for model development:

- The target variable is slightly imbalanced, so model performance should be evaluated using metrics such as Precision, Recall, F1-Score, and ROC-AUC rather than accuracy alone.
- EAD, Debt-to-Equity, and Interest Coverage contain extreme values and are highly skewed. These variables may require scaling or transformation before modelling.
- Credit Score, Credit Rating, Coupon Rate, and Maturity Months show the strongest relationship with default behaviour and are likely to be important predictors.
- The correlation analysis shows weak relationships between most variables, indicating that multicollinearity is not a major concern.
- The portfolio is well distributed across sectors and years, reducing the risk of bias in the training and testing datasets.
- Categorical variables such as Sector, Rating, Loan Type, and Collateral will need to be encoded before they can be used in machine learning models.
- All selected features will be retained initially, and their importance will be assessed during model training.

Overall, the EDA suggests that Credit Score, Credit Rating, Coupon Rate, and Maturity Months are the most useful indicators of default risk. The analysis also identified skewed variables and class imbalance, which should be considered during data preparation and model evaluation. These findings will help guide the next stage of feature engineering and machine learning model development.

### Design Architecture

                    ┌──────────────────────────┐
                    │  Kaggle Credit Risk      │
                    │  Dataset (CSV Files)     │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Data Acquisition         │
                    │ Download from Kaggle     │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Data Ingestion           │
                    │ loan_portfolio.csv       │
                    │ credit_ratings.csv       │
                    │ payment_history.csv      │
                    │ macro_stress_scenarios   │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Data Validation          │
                    │ Missing Values           │
                    │ Duplicate Checks         │
                    │ Data Type Validation     │
                    │ Outlier Detection        │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Google Data Storage      |
                    | Big Query                |
                    │ Pandas DataFrames        │
                    │ Processed Dataset        │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Data Processing          │
                    │ Data Cleaning            │
                    │ EDA                      │
                    │ Feature Engineering      │
                    │ Encoding                 │
                    │ Train/Test Split         │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   Model Development      |
                    │  Binary Classification   |
                    │        Models            │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ Outputs                  │
                    │ Default Predictions      │
                    │ Portfolio EL Estimates   │
                    │ Model Metrics            │
                    │ EDA Visualisations       │
                    └──────────────────────────┘

The architecture follows a structured workflow from data acquisition to model output. Python automates data ingestion, validation, cleaning, analysis, feature preparation, model training, and evaluation, while selecting features, interpreting results, choosing the final model, and documenting the findings will remain manual.

### Implementation and Reproducability

The data pipeline was successfully implemented up to the Exploratory Data Analysis (EDA) stage using Python. The loan portfolio dataset (50,000 records) was loaded, validated, cleaned, and analysed to ensure data quality and readiness for modelling. The implementation included:

- Dataset loading and shape verification
- Missing value and duplicate checks
- Data type validation
- Removal of data leakage variables
- Summary statistics for numerical variables
- Target variable distribution analysis
- Correlation analysis
- Numerical and categorical feature visualisations
- Time-based analysis of portfolio characteristics
- Storage of transformed datasets in Google Cloud Storage (GCS) using a raw and processed data structure 

The successful completion of these tasks confirms that the data was correctly ingested, validated, processed, and stored, providing a strong foundation for the subsequent feature engineering and model development stages. Following data cleaning and transformation, the processed dataset was uploaded to Google Cloud Storage (GCS), with the original dataset retained in the raw layer and the transformed dataset stored in the processed layer to support data lineage, reproducibility, and future pipeline activities. To further support reproducibility, the full implementation of the data ingestion, validation, cleaning, storage, and EDA pipeline is available in the link [Loan Portfolio-EDA](https://github.com/shristeca/SETU---Data-Handling-and-Infrastructure-/blob/b35cb9d619910e492345b9b702bc23f42b393ef6/Project_AI_Infra_EDA.ipynb) . The notebook contains the Python code, intermediate outputs, and visualisations generated during the analysis.

<img width="737" height="290" alt="image" src="https://github.com/user-attachments/assets/df87d585-8f05-4bd2-ac9d-f9ccf4ba9440" />



### Next Step

Despite the dataset being clean and free from missing values and duplicates, several issues remain for further investigation. Variables such as EAD, Debt-to-Equity, and Interest Coverage contain extreme values and exhibit strong positive skewness. In addition, the target variable is imbalanced, with fewer defaulted loans than non-defaulted loans. The relationship between some variables and the target may also be non-linear and could require additional feature engineering to improve model performance.

Milestone 3 will focus on feature engineering, data preparation, and the development of predictive models. The data will be split into training/dev/testing data and different machine learning models will be evaluated and compared to identify the most suitable approach for predicting loan default probability.
