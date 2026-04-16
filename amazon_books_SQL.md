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

```sql
-- Number of distinct rows
SELECT COUNT(DISTINCT asin) AS num_distinct_asins
FROM [kindle_data_2023 ];
-- no duplicates
```

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

```sql
-- Investigate duplicated titles
SELECT title, COUNT(title) AS title_duplicates_amounts
FROM [kindle_data_2023 ]
GROUP BY title
HAVING COUNT(title) > 1;
-- seems to be different publications for various titles
```

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

```sql
-- Noticed 'Not Found' titles:
SELECT *
FROM [kindle_data_2023 ]
WHERE title = 'Not Found';
```

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
SELECT *
FROM [kindle_data_2023 ]
WHERE soldBy = '';
```

```sql
-- Check publisher list
SELECT soldBY
FROM [kindle_data_2023 ]
GROUP BY 1
ORDER BY 1;
-- many small errors/repeats in soldBy list
```

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

```sql
-- How many books have a 0 (or missing) star review?
SELECT COUNT(*) AS NA_stars
FROM [kindle_data_2023 ]
WHERE stars = 0;
```

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

```sql
-- Fix error in 'Arts & Photo graphy'
UPDATE [kindle_data_2023 ]
SET category_name = 'Arts & Photography'
WHERE category_name = 'Arts & Photo graphy';
```

### Published Date

```sql
-- Check for null/missing publish dates
SELECT publishedDate
FROM [kindle_data_2023 ]
WHERE publishedDate IS NULL OR publishedDate = '';
```

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
WHERE soldBY != ''”
GROUP BY 2
ORDER BY 1 DESC
LIMIT 5;
```

```sql
-- Top 5 least common publishers
SELECT COUNT(soldBy) AS total_books, soldBY
FROM [kindle_data_2023 ]
GROUP BY 2
ORDER BY 1
LIMIT 5;
-- Amazon.com Services LLC is the most common publisher and RCS MediaGroup S.p.A is the least common
```

### Pricing

```sql
-- Average book price
SELECT ROUND(AVG(price), 2) AS average_book_price
FROM [kindle_data_2023 ]
WHERE price != 0;
```

```sql
-- Most expensive books
SELECT title, author, price, category_name, soldBY
FROM [kindle_data_2023 ]
ORDER BY price DESC
LIMIT 10;
-- educational textbooks have the highest prices and are all sold by Amazon
```

```sql
-- Least expensive books
SELECT title, author, price, category_name, soldBY
FROM [kindle_data_2023 ]
WHERE price != 0
ORDER BY price
LIMIT 5;
-- educational textbooks also have textbooks have the lowest prices and are all sold by Amazon
```

```sql
-- Which category has the most expensive books on average?
SELECT ROUND(AVG(price), 2) AS average_price, category_name
FROM [kindle_data_2023 ]
GROUP BY 2
ORDER BY AVG(price) DESC
LIMIT 5;
-- law books have the highest average price
```

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

```sql
-- Most/least common category
SELECT category_name, COUNT(*) AS total_books
FROM [kindle_data_2023 ]
GROUP BY category_name
ORDER BY 2 DESC;
-- Mystery, Thriller, & Suspense is the most common category and comics is the least common category
```

```sql
-- Average price of high rated books
SELECT ROUND(AVG(price), 2) AS average_price_high
FROM [kindle_data_2023 ]
WHERE stars >= 4 AND price != 0;
```

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

```sql
-- Editor’s pick average price
SELECT ROUND(AVG(price), 2) AS average_ep_price
FROM [kindle_data_2023 ]
WHERE isEditorsPick = 'True' AND price != 0;
```
```sql
-- Best seller average price
SELECT ROUND(AVG(price), 2) AS average_bs_price
FROM [kindle_data_2023 ]
WHERE isBestSeller = 'True' AND price != 0;
```

```sql
-- Good Read’s Choice average price
SELECT ROUND(AVG(price), 2) AS average_gr_price
FROM [kindle_data_2023 ]
WHERE isGoodReadsChoice = 'True' AND price != 0;
```

```sql
-- Average price per distributor
SELECT soldBy, ROUND(AVG(price), 2) as average_dist_price
FROM [kindle_data_2023 ]
WHERE price != 0 AND soldBy != ‘’
GROUP BY soldBy
ORDER BY average_dist_price DESC
LIMIT 5;
```

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

```sql
-- How many authors have 5 star books?
SELECT author, COUNT(stars) AS num_top_rated_books
FROM [kindle_data_2023 ]
WHERE stars = 5
GROUP BY author
ORDER BY num_top_rated_books DESC;
--4,614 authors have 5 star books, Olly Richards has the most 5 star books
```

```sql
-- Olly Richards 5 star books
SELECT title, author
FROM [kindle_data_2023 ]
WHERE author = 'Olly Richards' AND stars = 5;
```

### Kindle Unlimited (KU) Analysis

```sql
-- How many books are available through Kindle Unlimited?
SELECT COUNT(*) AS total_available_KU_books
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True';
```

```sql
-- Which publishers prefer KU?
SELECT soldBy, COUNT(*) AS num_books_KU
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True' AND soldBY != ''
GROUP BY soldBy
ORDER BY num_books_KU DESC;
```

```sql
-- What are the most/least common categories in KU?
SELECT category_name, COUNT(*) AS KUbooks_in_category
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True'
GROUP BY category_name
ORDER BY KUbooks_in_category DESC;
```

```sql
-- KU price vs Non KU price
SELECT ROUND(AVG(price), 2) AS average_KU_price
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True' and price != 0;

SELECT ROUND(AVG(price), 2) AS average_nonKU_price
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'False' and price != 0;
```

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

```sql
-- Kindle Unlimited vs author
SELECT author, COUNT(*) AS total_KU_books
FROM [kindle_data_2023 ]
WHERE isKindleUnlimited = 'True' AND author != ''
GROUP BY author
ORDER BY total_KU_books DESC
LIMIT 10;
```

```sql
-- Are editor's choice books on KU?
SELECT isKindleUnlimited, COUNT(*) AS total
FROM [kindle_data_2023 ]
WHERE isEditorsPick = 'True'
GROUP BY isKindleUnlimited;
```

```sql
-- Are best seller books on KU?
SELECT isKindleUnlimited, COUNT(*) AS total
FROM [kindle_data_2023 ]
WHERE isBestSeller = 'True'
GROUP BY isKindleUnlimited;
```

```sql
-- Are good read choices on KU?
SELECT isKindleUnlimited, COUNT(*) AS total
FROM [kindle_data_2023 ]
WHERE isGoodReadsChoice = 'True'
GROUP BY isKindleUnlimited;
```

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

```sql
-- Average star amount of editors pick
SELECT isEditorsPick, ROUND(AVG(stars), 2) AS average_ep_stars
FROM [kindle_data_2023 ]
WHERE stars != 0
GROUP BY isEditorsPick;
```

```sql
-- Average star amount of good reads
SELECT isGoodReadsChoice, ROUND(AVG(stars), 2) AS average_gr_stars
FROM [kindle_data_2023 ]
WHERE stars != 0
GROUP BY isGoodReadsChoice;
```

```sql
-- Average star amount of best seller
SELECT isBestSeller, ROUND(AVG(stars), 2) AS average_bs_stars
FROM [kindle_data_2023 ]
WHERE stars != 0
GROUP BY isBestSeller;
```

## Conclusions
The average KU book price is 30% of a the average non-book price
About 35% of all listed books are available on KU
The top 5 KU categories include fiction books of various types while the top 5 non-KU books include non-fiction and educational books
Star ratings between KU and non-KU books are pretty similar with most books rated around 4 stars
Best Sellers are more likely to be available on KU than Editor’s Pick and Good Read’s Choice books


## Visuals

<img width="999" height="799" alt="Dashboard 1" src="https://github.com/user-attachments/assets/57ac1456-9e9c-417f-a9a7-ac405306d0b7" />

