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
