# 2023 Amazon Kindle Books Dataset Analysis (SQL)

## Table of Contents
* [Data](#data)
* [Inspection and Cleaning]
* [Data Observations]
* [Exploratory Data Analysis]
  * [Publisher]
  * [Pricing]
  * [Author Analysis]
  * [Kindle Unlimited (KU) Analysis]
  * [Book Ratings]
* [Conclusions]
* [Visuals] 

## Data 
This data was gathered from Kaggle.

Additional information about the data set, including it's creator and column descriptions, can be found here: https://www.kaggle.com/datasets/asaniczka/amazon-kindle-books-dataset-2023-130k-books

## Inspection and Cleaning

-- primary key inspection
```sql
SELECT COUNT(*) AS total_rows
FROM [kindle_data_2023 ];
``` 
![/home/simpdan/Desktop/Portfolio Projects/SQL_pics/Screenshot_20260413_124602.png]
