# 2023 Amazon Kindle Books Dataset Analysis (SQL)

## Table of Contents
* [Data](#data)
* [Inspection and Cleaning](#inspection-and-cleaning)
  * [ASIN](#asin)
  * [Title](#title)
  * [Author](#author)
  * [SoldBy](#soldby)
  * [Review Amounts and Stars](#review-amounts-and-stars)
  * [Price](#price)
  * [Kindle Unlimited](#kindle-unlimited-status)
  * [Best Seller](#best-seller-status)
  * [Editor's Pick](#editors-pick-status)
  * [Good Read's Choice](#good-reads-choice-status)
  * [Category](#category)
  * [Published Date](#published-date)
* [Data Observations](#observations)
* [Exploratory Data Analysis](#exploratory-data-analysis)
  * [Publisher](#publisher)
  * [Pricing](#pricing)
  * [Author Analysis](#author-analysis)
  * [Kindle Unlimited (KU) Analysis](#kindle-unlimited-ku-analysis)
  * [Book Ratings](#book-ratings)
* [Conclusions](#conclusions)
* [Visuals](#visuals) 

## Data 
This data was gathered from Kaggle.

Additional information about the data set, including it's creator and column descriptions, can be found here: https://www.kaggle.com/datasets/asaniczka/amazon-kindle-books-dataset-2023-130k-books

## Inspection and Cleaning

### Asin

```sql
-- How many rows?
SELECT COUNT(*) AS total_rows
FROM [kindle_data_2023 ];
``` 
<img width="80" height="39" alt="Screenshot_20260417_115441" src="https://github.com/user-attachments/assets/384ec497-047c-4e91-82e2-c572bd9b77a6" />
<br/><br/>

```sql
-- Number of distinct rows
SELECT COUNT(DISTINCT asin) AS num_distinct_asins
FROM [kindle_data_2023 ];
-- no duplicates
```
<img width="130" height="39" alt="Screenshot_20260417_115549" src="https://github.com/user-attachments/assets/0fb06802-dbd4-4c65-828b-f710bde66354" />
<br/><br/>

```sql
-- Check for null values in asin column
SELECT *
FROM [kindle_data_2023 ]
WHERE asin IS NULL;
-- no asin nulls
```

```sql
-- Check for missing values in asin column
SELECT *
FROM [kindle_data_2023 ]
WHERE asin = ‘’;
-- no missing asin values
```

### Title

```sql
-- Check for duplicate titles
SELECT COUNT(DISTINCT title) AS distinct_titles
FROM [kindle_data_2023 ];
```
<img width="97" height="39" alt="Screenshot_20260417_115915" src="https://github.com/user-attachments/assets/c355dc10-40c7-49aa-995a-3911056d396e" />
<br/><br/>

```sql
-- Investigate duplicated titles
SELECT title, COUNT(title) AS title_duplicates_amounts
FROM [kindle_data_2023 ]
GROUP BY title
HAVING COUNT(title) > 1;
-- seems to be different publications for various titles
```
<img width="786" height="122" alt="Screenshot_20260417_120048" src="https://github.com/user-attachments/assets/2ceb866d-54e1-40be-bfe4-dac404a17863" />
<br/><br/>

```sql
-- Check for null titles
SELECT *
FROM [kindle_data_2023 ]
WHERE title IS NULL;
-- no title nulls
```

```sql
-- Check for missing titles
SELECT *
FROM [kindle_data_2023 ]
WHERE title = '';
-- no missing titles
```

```sql
-- Check for possible translation errors or misinterpreted symbols
SELECT title
FROM [kindle_data_2023 ]
ORDER BY title DESC;
-- many titles in non-english languages
```
<img width="632" height="122" alt="Screenshot_20260417_120140" src="https://github.com/user-attachments/assets/52980ddd-060a-4675-9072-95a2b974c27d" />
<br/><br/>

### Author

```sql
-- Check for null authors
SELECT *
FROM [kindle_data_2023 ]
WHERE author IS NULL;
-- no author nulls
```

```sql
-- Check for missing authors
SELECT *
FROM [kindle_data_2023 ]
WHERE author = '';
-- 423 books missing author data (seems random)
```
<img width="860" height="122" alt="Screenshot_20260417_120304" src="https://github.com/user-attachments/assets/ba402d12-5224-4146-8f4e-4a5438ae00a8" />
<br/><br/>

```sql
-- Noticed 'Not Found' titles:
SELECT *
FROM [kindle_data_2023 ]
WHERE title = 'Not Found';
```
<img width="800" height="61" alt="Screenshot_20260413_124602" src="https://github.com/user-attachments/assets/313e6aa3-eceb-42bd-9db9-e72287ae3c7a" />
<br/><br/>

```sql
-- As almost all data for these books is missing, let's remove them
DELETE FROM [kindle_data_2023 ]
WHERE title = 'Not Found';
```

### SoldBy

```sql
-- Check for null distributors
SELECT *
FROM [kindle_data_2023 ]
WHERE soldBy IS NULL;
-- no null distributors
```

```sql
-- Check for missing distributors
SELECT COUNT(*) AS missing_distributors
FROM [kindle_data_2023 ]
WHERE soldBy = '';
-- 9,231 seemly randomly missing
```
<img width="140" height="39" alt="Screenshot_20260417_120734" src="https://github.com/user-attachments/assets/c3b23d9b-77dc-431b-8ae7-e2f2a8a226be" />
<br/><br/>

```sql
-- Check publisher list
SELECT soldBY
FROM [kindle_data_2023 ]
GROUP BY 1
ORDER BY 1;
-- many small errors/repeats in soldBy list
```
<img width="241" height="166" alt="Screenshot_20260413_123959" src="https://github.com/user-attachments/assets/6c2572c6-7429-42ac-92b1-ad87986b560a" />
<br/><br/>

```sql
-- Update HarperCollins
UPDATE [kindle_data_2023 ]
SET soldBy = 'HarperCollins Publishing'
WHERE soldBY = 'HarperCollins Publishers' OR soldBY = 'Harper Collins';
```

```sql
-- Update Macmillan
UPDATE [kindle_data_2023 ]
SET soldBy = 'Macmillan Higher Education'
WHERE soldBY = 'Macmillan';
```

```sql
-- Update Simon and Schuster
UPDATE [kindle_data_2023 ]
SET soldBy = 'Simon and Schuster Digital Sales Inc'
WHERE soldBY = 'Simon & Schuster Digital Sales Inc.';
```

```sql
-- Update Amazon
UPDATE [kindle_data_2023 ]
SET soldBy = 'Amazon'
WHERE soldBy = 'Amazon Digital Services LLC GU' OR
soldBy = 'Amazon Digital Services LLC HN' OR
soldBy = 'Amazon Digital Services LLC MK' OR
soldBy = 'Amazon.com' OR
soldBy = 'Amazon.com Services LLC';
```

```sql
-- Update Random House
UPDATE [kindle_data_2023 ]
SET soldBy = 'Random House'
WHERE soldBY = 'RH AU' OR
soldBy = 'Random House India' OR
soldBy = 'Random House LLC' OR
soldBy = 'Random House NZ' OR
soldBy = 'Random House ZA';
```

```sql
-- Update Penguin 
UPDATE [kindle_data_2023 ]
SET soldBy = 'Penguin'
WHERE soldBY = 'Penguin Group (USA) LLC' OR
soldBy = 'Penguin Random House Australia Pty Ltd' OR
soldBy = 'Penguin Random House Grupo Editorial' OR
soldBy = 'Penguin Random House Publisher Services';
```

```sql
-- Re-check publisher list
SELECT soldBY
FROM [kindle_data_2023 ]
GROUP BY 1
ORDER BY 1;
```
<img width="232" height="374" alt="Screenshot_20260417_120928" src="https://github.com/user-attachments/assets/a93528cc-0eab-46c3-ab6e-d9efe0884e46" />
<br/><br/>

### Review Amounts and Stars
```sql
-- Check for null reviews
SELECT *
FROM [kindle_data_2023 ]
WHERE stars IS NULL;
-- no stars nulls
```

```sql
-- Check to make sure reviews are from 0 - 5
SELECT MAX(stars) AS max_rating, MIN(stars) AS min_rating
FROM [kindle_data_2023 ];
```
<img width="154" height="38" alt="Screenshot_20260417_121136" src="https://github.com/user-attachments/assets/a84c34cd-c9a6-45e0-9f26-cb259e60212d" />
<br/><br/>

```sql
-- How many books have a 0 (or missing) star review?
SELECT COUNT(*) AS NA_stars
FROM [kindle_data_2023 ]
WHERE stars = 0;
```
<img width="73" height="38" alt="Screenshot_20260417_121211" src="https://github.com/user-attachments/assets/72dce1c2-7fa9-41d8-8aa5-c02e61498805" />
<br/><br/>

```sql
-- Check for null review amounts
SELECT *
FROM [kindle_data_2023 ]
WHERE reviews IS NULL;
-- no review amount nulls
```

```sql
-- How many books have 0 (or missing) review amounts?
SELECT COUNT(*) AS NA_reviews
FROM [kindle_data_2023 ]
WHERE reviews = 0;
```
<img width="86" height="38" alt="Screenshot_20260417_121303" src="https://github.com/user-attachments/assets/d59bcff1-aebf-45e5-963f-4ec9d1f33352" />
<br/><br/>

### Price

```sql
-- Check for null prices 
SELECT *
FROM [kindle_data_2023 ]
WHERE price IS NULL;
-- no price nulls
```

```sql
-- How many books have 0 (or missing) prices?
SELECT COUNT(*) AS NA_prices
FROM [kindle_data_2023 ]
WHERE price = 0;
```
<img width="74" height="38" alt="Screenshot_20260417_121335" src="https://github.com/user-attachments/assets/0d20e7d9-782b-4da8-a5d4-edb13e6bb00f" />
<br/><br/>

### Kindle Unlimited Status

```sql
--Check for null kindle unlimited
SELECT *
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited IS NULL;
-- no kindle unlimited nulls
```

```sql
-- Check for missing kindle unlimited
SELECT *
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = '';
-- no kindle unlimited missing values
```

```sql
-- Check to make sure kindle unlimited status is only TRUE or FALSE
SELECT isKindleUnlimited
FROM [kindle_data_2023 ]
GROUP BY 1;
```
<img width="123" height="60" alt="Screenshot_20260417_121410" src="https://github.com/user-attachments/assets/4e0e9401-1f2a-449d-814a-5e6ea3508cee" />
<br/><br/>

### Best Seller Status

```sql
-- Check for null best seller
SELECT *
FROM [kindle_data_2023 ]
WHERE isBestSeller IS NULL;
-- no best seller nulls
```

```sql
-- Check for missing best seller
SELECT *
FROM [kindle_data_2023 ]
WHERE isBestSeller = '';
-- no best seller missing values
```

```sql
-- Check to make sure best seller is only TRUE or FALSE
SELECT isBestSeller
FROM [kindle_data_2023 ]
GROUP BY 1;
```
<img width="86" height="60" alt="Screenshot_20260417_121440" src="https://github.com/user-attachments/assets/abae7040-e8d7-4a4e-a035-eb7020deec6e" />
<br/><br/>

### Editor's Pick Status

```sql
-- Check for editor's pick nulls
SELECT *
FROM [kindle_data_2023 ]
WHERE isEditorsPick IS NULL;
-- no editor's pick nulls
```

```sql
-- Check for missing editor's pick
SELECT *
FROM [kindle_data_2023 ]
WHERE isEditorsPick = '';
-- no editor's pick missing values
```

```sql
-- Check to make sure editor's pick is only TRUE or FALSE
SELECT isEditorsPick
FROM [kindle_data_2023 ]
GROUP BY 1;
```
<img width="96" height="60" alt="Screenshot_20260417_121523" src="https://github.com/user-attachments/assets/dce5250b-63ac-41c9-a4c5-cd5e9756b81a" />
<br/><br/>

### Good Read's Choice Status

```sql
-- Check for good reads nulls
SELECT *
FROM [kindle_data_2023 ]
WHERE isGoodReadsChoice IS NULL;
-- no good reads nulls
```

```sql
-- Check for missing good reads
SELECT *
FROM [kindle_data_2023 ]
WHERE isGoodReadsChoice = '';
-- no good reads missing values
```

```sql
-- Check to make sure good reads is only TRUE or FALSE
SELECT isGoodReadsChoice
FROM [kindle_data_2023 ]
GROUP BY 1;
```
<img width="132" height="63" alt="Screenshot_20260417_121601" src="https://github.com/user-attachments/assets/7b587d62-3112-4b69-b51e-3210c3a3c520" />
<br/><br/>

### Category

```sql
-- Check for null category values
SELECT *
FROM [kindle_data_2023 ]
WHERE category_name IS NULL OR categoryID IS NULL;
-- no category name or ID nulls
```

```sql
-- Check for missing category names or IDs
SELECT *
FROM [kindle_data_2023 ]
WHERE category_name = '' OR categoryID = 0;
-- no category name or ID missing values
```

```sql
-- Check category values and check to make sure categoryIDs are consistent with each category_name
SELECT categoryID, category_name
FROM [kindle_data_2023 ]
GROUP BY 1;
```
<img width="260" height="164" alt="Screenshot_20260413_123724" src="https://github.com/user-attachments/assets/18de6ecb-412e-4c66-aec1-1a26849a6c6c" />
<br/><br/>

```sql
-- Fix error in 'Arts & Photo graphy'
UPDATE [kindle_data_2023 ]
SET category_name = 'Arts & Photography'
WHERE category_name = 'Arts & Photo graphy';
```
<img width="260" height="40" alt="Screenshot_20260413_123819" src="https://github.com/user-attachments/assets/a4f5896a-9638-45db-8f3d-8ba729ed4c02" />
<br/><br/>

### Published Date

```sql
-- Check for null/missing publish dates
SELECT COUNT(*) AS missing_dates
FROM [kindle_data_2023 ]
WHERE publishedDate IS NULL OR publishedDate = '';
```
<img width="99" height="43" alt="Screenshot_20260417_121826" src="https://github.com/user-attachments/assets/d21fd75e-07c0-4a52-b1a6-c70808ecd4c7" />
<br/><br/>

## Observations
* Different publications (by different publishers) of the same book are included in the data
* Same books published in multiple languages are included in the data
* 64,668 missing review amounts
* 3,180 missing review rating
* 423 authors are missing for no apparent reason
* 9,231 distributors missing
* 4,064 missing prices
* 49,014 missing published dates
* Category names doesn't always align properly (for example, some romance books are under the category parenting & relationships - true for Amazon Kindle in general though from personal experience)


## Exploratory Data Analysis

### Publisher

```sql
-- Top 5 most common publishers
SELECT COUNT(soldBy) AS total_books, soldBY
FROM [kindle_data_2023 ]
WHERE soldBY != ''
GROUP BY 2
ORDER BY 1 DESC
LIMIT 5;
```
<img width="297" height="127" alt="Screenshot_20260417_121938" src="https://github.com/user-attachments/assets/d9ac9e34-89d1-4dbc-bb07-3f8d06dc60c9" />
<br/><br/>

```sql
-- Top 5 least common publishers
SELECT COUNT(soldBy) AS total_books, soldBY
FROM [kindle_data_2023 ]
GROUP BY 2
ORDER BY 1
LIMIT 5;
-- Amazon.com is the most common publisher and RCS MediaGroup S.p.A is the least common
```
<img width="223" height="127" alt="Screenshot_20260417_122011" src="https://github.com/user-attachments/assets/608fca5f-279c-40e0-af5e-6f672ee9dae2" />
<br/><br/>

### Pricing

```sql
-- Average book price
SELECT ROUND(AVG(price), 2) AS average_book_price
FROM [kindle_data_2023 ]
WHERE price != 0;
```
<img width="133" height="40" alt="Screenshot_20260417_122322" src="https://github.com/user-attachments/assets/d23d617c-1c65-484d-822d-601cea31243b" />
<br/><br/>

```sql
-- Most expensive books
SELECT title, author, price, category_name, soldBY
FROM [kindle_data_2023 ]
ORDER BY price DESC
LIMIT 10;
-- educational textbooks have the highest prices and are all sold by Amazon
```
<img width="996" height="232" alt="Screenshot_20260417_122400" src="https://github.com/user-attachments/assets/0a3b7a27-dbb6-41c5-81b2-58f2d415e4b9" />
<br/><br/>

```sql
-- Least expensive books
SELECT title, author, price, category_name, soldBY
FROM [kindle_data_2023 ]
WHERE price != 0
ORDER BY price
LIMIT 5;
-- educational textbooks also have textbooks have the lowest prices and are all sold by Amazon
```
<img width="1010" height="124" alt="Screenshot_20260417_122930" src="https://github.com/user-attachments/assets/088664b3-c9e3-436d-a7ad-4541a0d8c265" />
<br/><br/>

```sql
-- Which category has the most expensive books on average?
SELECT ROUND(AVG(price), 2) AS average_price, category_name
FROM [kindle_data_2023 ]
GROUP BY 2
ORDER BY AVG(price) DESC
LIMIT 5;
-- law books have the highest average price
```
<img width="274" height="124" alt="Screenshot_20260417_123011" src="https://github.com/user-attachments/assets/653e8d43-9987-4988-ab3f-8dd221b0840e" />
<br/><br/>

```sql
-- Which category has the least expensive books on average?
SELECT ROUND(AVG(price), 2) AS average_price, category_name
FROM [kindle_data_2023 ]
WHERE price != 0
GROUP BY 2
ORDER BY AVG(price)
LIMIT 5;
-- romance books have the lowest average price
```
<img width="267" height="124" alt="Screenshot_20260417_123042" src="https://github.com/user-attachments/assets/e88037f9-10d8-480e-b645-11a170f256ed" />
<br/><br/>

```sql
-- Most/least common category
SELECT category_name, COUNT(*) AS total_books
FROM [kindle_data_2023 ]
GROUP BY category_name
ORDER BY 2 DESC;
-- Mystery, Thriller, & Suspense is the most common category and comics is the least common category
```
<img width="267" height="124" alt="Screenshot_20260417_123120" src="https://github.com/user-attachments/assets/8e0909ae-46da-47bd-af0d-1bfd1866d47b" />
<br/><br/>
<img width="267" height="102" alt="Screenshot_20260417_123151" src="https://github.com/user-attachments/assets/3285c1b3-b372-414c-84ad-10d7ecd6effd" />
<br/><br/>

```sql
-- Average price of high rated books
SELECT ROUND(AVG(price), 2) AS average_price_high
FROM [kindle_data_2023 ]
WHERE stars >= 4 AND price != 0;
```
<img width="129" height="42" alt="Screenshot_20260417_123544" src="https://github.com/user-attachments/assets/4910f1bc-fb6d-4b17-8090-202c6ab7bfad" />
<br/><br/>

```sql
-- Average price of low rated books
WITH rating AS (
 SELECT *,
 CASE
  WHEN stars = 5 THEN 'Excellent'
  WHEN stars > 3.9 THEN 'Good'
  WHEN stars > 2.9 THEN 'Average'
  WHEN stars > 1.9 THEN 'Below Average'
  WHEN stars > 0 THEN 'Poor'
  ELSE 'NA'
 END AS review_category
 FROM [kindle_data_2023 ]
)
SELECT ROUND(AVG(price),2) AS average_price_low
FROM rating
WHERE review_category = 'Below Average' OR review_category = 'Poor' AND price != 0;
```
<img width="129" height="42" alt="Screenshot_20260417_123629" src="https://github.com/user-attachments/assets/4d32fd8a-bba3-49cd-8b9d-5e7bc29c8b83" />
<br/><br/>

```sql
-- Editor’s pick average price
SELECT ROUND(AVG(price), 2) AS average_ep_price
FROM [kindle_data_2023 ]
WHERE isEditorsPick = 'True' AND price != 0;
```
<img width="118" height="42" alt="Screenshot_20260417_123749" src="https://github.com/user-attachments/assets/1656494d-6c61-4491-a2d9-6f7355a03ac3" />
<br/><br/>

```sql
-- Best seller average price
SELECT ROUND(AVG(price), 2) AS average_bs_price
FROM [kindle_data_2023 ]
WHERE isBestSeller = 'True' AND price != 0;
```
<img width="118" height="42" alt="Screenshot_20260417_123813" src="https://github.com/user-attachments/assets/dd063943-66cc-4606-8268-bceec407bb22" />
<br/><br/>

```sql
-- Good Read’s Choice average price
SELECT ROUND(AVG(price), 2) AS average_gr_price
FROM [kindle_data_2023 ]
WHERE isGoodReadsChoice = 'True' AND price != 0;
```
<img width="118" height="42" alt="Screenshot_20260417_123833" src="https://github.com/user-attachments/assets/7241d387-ee67-4479-b3a8-77e1573ede67" />
<br/><br/>

```sql
-- Average price per distributor
SELECT soldBy, ROUND(AVG(price), 2) as average_dist_price
FROM [kindle_data_2023 ]
WHERE price != 0 AND soldBy != ‘’
GROUP BY soldBy
ORDER BY average_dist_price DESC
LIMIT 5;
```
<img width="288" height="124" alt="Screenshot_20260417_123957" src="https://github.com/user-attachments/assets/c871e220-276c-403c-9fb8-3d717851dd93" />
<br/><br/>

### Author Analysis

```sql
-- Authors with multiple books
SELECT author, COUNT(*) AS books_per_author
FROM [kindle_data_2023 ]
WHERE author!= ''
GROUP BY author
ORDER BY books_per_author DESC
LIMIT 10;
-- includes different language prints and different edition prints
```
<img width="260" height="230" alt="Screenshot_20260417_124221" src="https://github.com/user-attachments/assets/a9bd7b5b-ca30-44a9-9aed-31bfdfd0e5fd" />
<br/><br/>

```sql
-- How many authors have 5 star books?
SELECT author, COUNT(stars) AS num_top_rated_books
FROM [kindle_data_2023 ]
WHERE stars = 5
GROUP BY author
ORDER BY num_top_rated_books DESC;
--4,614 authors have 5 star books, Olly Richards has the most 5 star books
```
<img width="532" height="159" alt="Screenshot_20260417_124316" src="https://github.com/user-attachments/assets/3390e95c-42a6-466e-aad3-ec883be57248" />
<br/><br/>

```sql
-- Olly Richards 5 star books
SELECT title, author
FROM [kindle_data_2023 ]
WHERE author = 'Olly Richards' AND stars = 5;
```
<img width="693" height="188" alt="Screenshot_20260417_124346" src="https://github.com/user-attachments/assets/a644f81f-c450-4829-9f28-3778952c7386" />
<br/><br/>

### Kindle Unlimited (KU) Analysis

```sql
-- How many books are available through Kindle Unlimited?
SELECT COUNT(*) AS total_available_KU_books
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True';
```
<img width="166" height="42" alt="Screenshot_20260417_124421" src="https://github.com/user-attachments/assets/e90ad070-acb5-4d8c-aa08-b16e98245862" />
<br/><br/>

```sql
-- Which publishers prefer KU?
SELECT soldBy, COUNT(*) AS num_books_KU
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True' AND soldBY != ''
GROUP BY soldBy
ORDER BY num_books_KU DESC;
```
<img width="326" height="271" alt="Screenshot_20260417_124445" src="https://github.com/user-attachments/assets/19d96c1f-1d98-4454-85dc-44c0d6812f69" />
<br/><br/>

```sql
-- What are the most/least common categories in KU?
SELECT category_name, COUNT(*) AS KUbooks_in_category
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True'
GROUP BY category_name
ORDER BY KUbooks_in_category DESC;
```
<img width="326" height="124" alt="Screenshot_20260417_124521" src="https://github.com/user-attachments/assets/65bc7b3e-a43b-4da6-9bc8-4d02327f3f57" />
<br/><br/>
<img width="326" height="104" alt="Screenshot_20260417_124559" src="https://github.com/user-attachments/assets/628a0283-da7f-4b41-b3f3-5b2dc8f04ab1" />
<br/><br/>

```sql
-- KU price vs Non KU price
SELECT ROUND(AVG(price), 2) AS average_KU_price
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True' and price != 0;

SELECT ROUND(AVG(price), 2) AS average_nonKU_price
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'False' and price != 0;
```
<img width="120" height="42" alt="Screenshot_20260417_124627" src="https://github.com/user-attachments/assets/738fcb3e-293a-4c55-8baa-38c102797e48" />
<br/><br/>
<img width="144" height="42" alt="Screenshot_20260417_124702" src="https://github.com/user-attachments/assets/a2ac8160-3603-4c21-bebf-cd458dd678d8" />
<br/><br/>

```sql
-- Are top rated books on KU?
WITH rating AS (
 SELECT *,
 CASE
  WHEN stars = 5 THEN 'Excellent'
  WHEN stars > 3.9 THEN 'Good'
  WHEN stars > 2.9 THEN 'Average'
  WHEN stars > 1.9 THEN 'Below Average'
  WHEN stars > 0 THEN 'Poor'
  ELSE 'NA'
 END AS review_category
 FROM [kindle_data_2023 ]
)
SELECT COUNT(*) AS total, isKindleUnlimited
FROM rating
WHERE review_category = 'Excellent' OR review_category = 'Good'
GROUP BY isKindleUnlimited;
```
<img width="181" height="63" alt="Screenshot_20260417_124829" src="https://github.com/user-attachments/assets/b07a16b4-d7f3-4653-aaa2-edefe2588cd9" />
<br/><br/>

```sql
--Are low rated books on KU?
WITH rating AS (
SELECT *,
 CASE
  WHEN stars = 5 THEN 'Excellent'
  WHEN stars > 3.9 THEN 'Good'
  WHEN stars > 2.9 THEN 'Average'
  WHEN stars > 1.9 THEN 'Below Average'
  WHEN stars > 0 THEN 'Poor'
  ELSE 'NA'
 END AS review_category
 FROM [kindle_data_2023 ]
)
SELECT COUNT(*) AS total, isKindleUnlimited
FROM rating
WHERE review_category = 'Below Average' OR review_category = 'Poor'
GROUP BY isKindleUnlimited;
```
<img width="181" height="63" alt="Screenshot_20260417_124905" src="https://github.com/user-attachments/assets/c0266b80-b2b0-4771-9d15-202b5ecc6625" />
<br/><br/>

```sql
-- Are average rated books on KU?
WITH rating AS (
 SELECT *,
 CASE
  WHEN stars = 5 THEN 'Excellent'
  WHEN stars > 3.9 THEN 'Good'
  WHEN stars > 2.9 THEN 'Average'
  WHEN stars > 1.9 THEN 'Below Average'
  WHEN stars > 0 THEN 'Poor'
  ELSE 'NA'
 END AS review_category
 FROM [kindle_data_2023 ]
)
SELECT COUNT(*) AS total, isKindleUnlimited
FROM rating
WHERE review_category = 'Average'
GROUP BY isKindleUnlimited;
```
<img width="181" height="63" alt="Screenshot_20260417_124930" src="https://github.com/user-attachments/assets/9b150c5a-83cf-41c2-a39d-30b59bb09e8b" />
<br/><br/>

```sql
-- Kindle Unlimited vs author
SELECT author, COUNT(*) AS total_KU_books
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True' AND author != ''
GROUP BY author
ORDER BY total_KU_books DESC
LIMIT 10;
```
<img width="229" height="229" alt="Screenshot_20260417_124958" src="https://github.com/user-attachments/assets/cf68dbd7-3a91-4e8b-a621-69e66ed7091d" />
<br/><br/>

```sql
-- Are editor's choice books on KU?
SELECT isKindleUnlimited, COUNT(*) AS total
FROM [kindle_data_2023 ]
WHERE isEditorsPick = 'True'
GROUP BY isKindleUnlimited;
```
<img width="182" height="61" alt="Screenshot_20260417_125041" src="https://github.com/user-attachments/assets/a5e99ddc-7b90-4eac-b851-8d588e228a28" />
<br/><br/>

```sql
-- Are best seller books on KU?
SELECT isKindleUnlimited, COUNT(*) AS total
FROM [kindle_data_2023 ]
WHERE isBestSeller = 'True'
GROUP BY isKindleUnlimited;
```
<img width="182" height="61" alt="Screenshot_20260417_125114" src="https://github.com/user-attachments/assets/5f898cea-98e8-481c-8449-21106daa27bb" />
<br/><br/>

```sql
-- Are good read choices on KU?
SELECT isKindleUnlimited, COUNT(*) AS total
FROM [kindle_data_2023 ]
WHERE isGoodReadsChoice = 'True'
GROUP BY isKindleUnlimited;
```
<img width="182" height="61" alt="Screenshot_20260417_125141" src="https://github.com/user-attachments/assets/99288de3-ac39-454a-be1e-0129bd19761a" />
<br/><br/>

### Book Ratings

```sql
-- Average rating per distributor
SELECT soldBy, ROUND(AVG(stars), 2) AS average_dist_stars
FROM [kindle_data_2023 ]
WHERE stars != 0 AND soldBy != ''
GROUP BY soldBy
ORDER BY average_dist_stars DESC
LIMIT 10;
```
<img width="343" height="229" alt="Screenshot_20260417_125214" src="https://github.com/user-attachments/assets/b1daeb86-44d8-4b03-9f1c-3f87caf93da6" />
<br/><br/>

```sql
-- Average star amount of editors pick
SELECT isEditorsPick, ROUND(AVG(stars), 2) AS average_ep_stars
FROM [kindle_data_2023 ]
WHERE stars != 0
GROUP BY isEditorsPick;
```
<img width="199" height="60" alt="Screenshot_20260417_125248" src="https://github.com/user-attachments/assets/1e8ed269-3ead-44c0-b7ca-22d98da84af9" />
<br/><br/>

```sql
-- Average star amount of good reads
SELECT isGoodReadsChoice, ROUND(AVG(stars), 2) AS average_gr_stars
FROM [kindle_data_2023 ]
WHERE stars != 0
GROUP BY isGoodReadsChoice;
```
<img width="234" height="60" alt="Screenshot_20260417_125313" src="https://github.com/user-attachments/assets/549d6360-e0dc-4ed5-919c-8149fe6b346e" />
<br/><br/>

```sql
-- Average star amount of best seller
SELECT isBestSeller, ROUND(AVG(stars), 2) AS average_bs_stars
FROM [kindle_data_2023 ]
WHERE stars != 0
GROUP BY isBestSeller;
```
<img width="192" height="60" alt="Screenshot_20260417_125341" src="https://github.com/user-attachments/assets/4c8159b5-8a4c-4eaf-a7dc-13b280d2ed5c" />
<br/><br/>

## Conclusions
The average KU book price is 30% of a the average non-book price
About 35% of all listed books are available on KU
The top 5 KU categories include fiction books of various types while the top 5 non-KU books include non-fiction and educational books
Star ratings between KU and non-KU books are pretty similar with most books rated around 4 stars
Best Sellers are more likely to be available on KU than Editor’s Pick and Good Read’s Choice books


## Visuals

<img width="999" height="799" alt="Dashboard 1" src="https://github.com/user-attachments/assets/6f05e925-2765-49db-a521-f77fb4fec99e" />


