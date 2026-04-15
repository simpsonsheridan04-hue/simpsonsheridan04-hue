# **2023 Amazon Kindle Books Dataset Analysis (SQL)**

# **Data**

This data was gathered from Kaggle.

Additional information about the data set, including it's creator and column descriptions, can be found here: https://www.kaggle.com/datasets/asaniczka/amazon-kindle-books-dataset-2023-130k-books

# **Inspection and Cleaning**

*Primary Key Inspection*  
**SELECT** COUNT(\*) AS total\_rows  
**FROM** \[kindle\_data\_2023 \];

*Number of distinct rows*  
**SELECT** COUNT(DISTINCT asin) AS num\_distinct\_asins  
**FROM** \[kindle\_data\_2023 \];  
\-- no duplicates

*Check for null values in asin column*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** asin IS NULL;  
\-- no asin nulls

*Check for missing values in asin column*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** asin \= ‘’;  
\-- no missing asin values

*Check for duplicate titles*  
**SELECT** COUNT(DISTINCT title) AS distinct\_titles  
**FROM** \[kindle\_data\_2023 \];

*Investigate duplicated titles*  
**SELECT** title, COUNT(title) AS title\_duplicates\_amounts  
**FROM** \[kindle\_data\_2023 \]  
**GROUP** **BY** title  
**HAVING** **COUNT**(title) \> 1;  
\-- seems to be different publications for various titles

*Check for null titles*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** title IS NULL;  
\-- no title nulls

*Check for missing titles*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** title \= '';  
\-- no missing titles

*Check for possible translation errors or misinterpreted symbols*  
**SELECT** title  
**FROM** \[kindle\_data\_2023 \]  
**ORDER** **BY** title DESC;  
\-- many titles in non-english languages

*Check for null authors*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** author IS NULL;  
\-- no author nulls

*Check for missing authors*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** author \= '';  
\-- 423 books missing author data (seems random)

*Noticed 'Not Found' titles:*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** title \= 'Not Found';

*As almost all data for these books is missing, let's remove them*  
**DELETE** **FROM** \[kindle\_data\_2023 \]  
**WHERE** title \= 'Not Found';

*Check for null distributors*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** soldBy IS NULL;  
\-- no null distributors

*Check for missing distributors*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** soldBy \= '';

*Check distributor list*  
**SELECT** soldBY  
**FROM** \[kindle\_data\_2023 \]  
**GROUP** **BY** 1  
**ORDER** **BY** 1;

*Update distributor list to make it uniform*  
**UPDATE** \[kindle\_data\_2023 \]  
**SET** soldBy \= 'HarperCollins Publishing'  
**WHERE** soldBY \= 'HarperCollins Publishers' OR soldBY \= 'Harper Collins';

**UPDATE** \[kindle\_data\_2023 \]  
**SET** soldBy \= 'Macmillan Higher Education'  
**WHERE** soldBY \= 'Macmillan';

**UPDATE** \[kindle\_data\_2023 \]  
**SET** soldBy \= 'Simon and Schuster Digital Sales Inc'  
**WHERE** soldBY \= 'Simon & Schuster Digital Sales Inc.';

**UPDATE** \[kindle\_data\_2023 \]  
**SET** soldBy \= 'Amazon'  
**WHERE** soldBy \= 'Amazon Digital Services LLC GU' OR  
soldBy \= 'Amazon Digital Services LLC HN' OR  
soldBy \= 'Amazon Digital Services LLC MK' OR  
soldBy \= 'Amazon.com' OR  
soldBy \= 'Amazon.com Services LLC';

**UPDATE** \[kindle\_data\_2023 \]  
**SET** soldBy \= 'Random House'  
**WHERE** soldBY \= 'RH AU' OR  
soldBy \= 'Random House India' OR  
soldBy \= 'Random House LLC' OR  
soldBy \= 'Random House NZ' OR  
soldBy \= 'Random House ZA';

**UPDATE** \[kindle\_data\_2023 \]  
**SET** soldBy \= 'Penguin'  
**WHERE** soldBY \= 'Penguin Group (USA) LLC' OR  
soldBy \= 'Penguin Random House Australia Pty Ltd' OR  
soldBy \= 'Penguin Random House Grupo Editorial' OR  
soldBy \= 'Penguin Random House Publisher Services';

*Check for null reviews*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars IS NULL;  
\-- no stars nulls

*Check to make sure reviews are from 0 \- 5*  
**SELECT** MAX(stars) AS max\_rating, MIN(stars) AS min\_rating  
**FROM** \[kindle\_data\_2023 \];

*How many books have a 0 (or missing) star review*  
**SELECT** COUNT(\*) AS NA\_stars  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars \= 0;

*Check for null review amounts*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** reviews IS NULL;  
\-- no review amount nulls

*How many books have 0 (or missing) review amounts?*  
**SELECT** COUNT(\*) AS NA\_reviews  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** reviews \= 0;

*Check for null prices*   
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** price IS NULL;  
\-- no price nulls

*How many books have 0 (or missing) prices?*  
**SELECT** COUNT(\*) AS NA\_prices  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** price \= 0;

*Check for null kindle unlimited*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited IS NULL;  
\-- no kindle unlimited nulls

*Check for missing kindle unlimited*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited \= '';  
\-- no kindle unlimited missing values

*Check to make sure kindle unlimited status is only TRUE or FALSE*  
**SELECT** isKindleUnlimited  
**FROM** \[kindle\_data\_2023 \]  
**GROUP** **BY** 1;

*Check for null best seller*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isBestSeller IS NULL;  
\-- no best seller nulls

*Check for missing best seller*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isBestSeller \= '';  
\-- no best seller missing values

*Check to make sure best seller is only TRUE or FALSE*  
**SELECT** isBestSeller  
**FROM** \[kindle\_data\_2023 \]  
**GROUP** **BY** 1;

*Check for editor's pick nulls*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isEditorsPick IS NULL;  
\-- no editor's pick nulls

*Check for missing editor's pick*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isEditorsPick \= '';  
\-- no editor's pick missing values

*Check to make sure editor's pick is only TRUE or FALSE*  
**SELECT** isEditorsPick  
**FROM** \[kindle\_data\_2023 \]  
**GROUP** **BY** 1;

*Check for good reads nulls*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isGoodReadsChoice IS NULL;  
\-- no good reads nulls

*Check for missing good reads*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isGoodReadsChoice \= '';  
\-- no good reads missing values

*Check to make sure good reads is only TRUE or FALSE*  
**SELECT** isGoodReadsChoice  
**FROM** \[kindle\_data\_2023 \]  
**GROUP** **BY** 1;

*Check for null category values*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** category\_name IS NULL OR categoryID IS NULL;  
\-- no category name or ID nulls

*Check for missing category names or IDs*  
**SELECT** \*  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** category\_name \= '' OR categoryID \= 0;  
\-- no category name or ID missing values

*Check category values and check to make sure categoryIDs are consistent with each category\_name*  
**SELECT** categoryID, category\_name  
**FROM** \[kindle\_data\_2023 \]  
**GROUP** **BY** 1;

*Fix error in 'Arts & Photo graphy'*  
**UPDATE** \[kindle\_data\_2023 \]  
**SET** category\_name \= 'Arts & Photography'  
**WHERE** category\_name \= 'Arts & Photo graphy';

*Check for null/missing publish dates*  
**SELECT** publishedDate  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** publishedDate IS NULL OR publishedDate \= '';

# **Observations**

\-- different publications (by different publishers) of the same book are included in the data  
\-- same books published in multiple languages are included in the data  
\-- 64,668 missing review amounts  
\-- 3,180 missing review rating  
\-- 423 authors are missing for no apparent reason  
\-- 9,231 distributors missing  
\-- 4,064 missing prices  
\-- 49,014 missing published dates  
\-- category names doesn't always align properly (for example, some romance books are under the category parenting & relationships \- true for Amazon Kindle in general though from personal experience)

# **Exploratory Data Analysis**

## **Publisher**

*Top 5 most common publishers*  
**SELECT** COUNT(soldBy) AS total\_books, soldBY  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** soldBY \!= ''”  
**GROUP BY** 2  
**ORDER BY** 1 DESC  
**LIMIT** 5;

*Top 5 least common publishers*  
**SELECT** COUNT(soldBy) AS total\_books, soldBY  
**FROM** \[kindle\_data\_2023 \]  
**GROUP BY** 2  
**ORDER BY** 1  
**LIMIT** 5;  
\-- Amazon.com Services LLC is the most common publisher and RCS MediaGroup S.p.A is the least common

## **Pricing**

*Average book price*  
**SELECT** ROUND(AVG(price), 2\) AS average\_book\_price  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** price \!= 0;

*Most expensive books*  
**SELECT** title, author, price, category\_name, soldBY  
**FROM** \[kindle\_data\_2023 \]  
**ORDER BY** price DESC  
**LIMIT** 10;  
\-- educational textbooks have the highest prices and are all sold by Amazon

*Least expensive books*  
**SELECT** title, author, price, category\_name, soldBY  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** price \!= 0  
**ORDER BY** price  
**LIMIT** 5;  
\-- educational textbooks also have textbooks have the lowest prices and are all sold by Amazon

*Which category has the most expensive books on average?*  
**SELECT** ROUND(AVG(price), 2\) AS average\_price, category\_name  
**FROM** \[kindle\_data\_2023 \]  
**GROUP BY** 2  
**ORDER BY** AVG(price) DESC  
**LIMIT** 5;  
\-- law books have the highest average price

*Which category has the least expensive books on average?*  
**SELECT** ROUND(AVG(price), 2\) AS average\_price, category\_name  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** price \!= 0  
**GROUP BY** 2  
**ORDER BY** AVG(price)  
**LIMIT** 5;  
\-- romance books have the lowest average price

*Most/least common category*  
**SELECT** category\_name, COUNT(\*) AS total\_books  
**FROM** \[kindle\_data\_2023 \]  
**GROUP BY** category\_name  
**ORDER BY** 2 DESC;  
\-- Mystery, Thriller, & Suspense is the most common category and comics is the least common category

*Average price of high rated books*  
**SELECT** ROUND(AVG(price), 2\) AS average\_price\_high  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars \>= 4 AND price \!= 0;

*Average price of low rated books*  
**WITH** rating AS (  
**SELECT** \*,  
**CASE**  
**WHEN** stars \= 5 THEN 'Excellent'  
**WHEN** stars \> 3.9 THEN 'Good'  
**WHEN** stars \> 2.9 THEN 'Average'  
**WHEN** stars \> 1.9 THEN 'Below Average'  
**WHEN** stars \> 0 THEN 'Poor'  
**ELSE** 'NA'  
**END** AS review\_category  
**FROM** \[kindle\_data\_2023 \]  
)  
**SELECT** ROUND(AVG(price),2) AS average\_price\_low  
**FROM** rating  
**WHERE** review\_category \= 'Below Average' OR review\_category \= 'Poor' AND price \!= 0;

*Editor’s pick average price*  
**SELECT** ROUND(AVG(price), 2\) AS average\_ep\_price  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isEditorsPick \= 'True' AND price \!= 0;  
*Best seller average price*  
**SELECT** ROUND(AVG(price), 2\) AS average\_bs\_price  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isBestSeller \= 'True' AND price \!= 0;

*Good Read’s Choice average price*  
**SELECT** ROUND(AVG(price), 2\) AS average\_gr\_price  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isGoodReadsChoice \= 'True' AND price \!= 0;

*Average price per distributor*  
**SELECT** soldBy, ROUND(AVG(price), 2\) as average\_dist\_price  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** price \!= 0 AND soldBy \!= ‘’  
**GROUP BY** soldBy  
**ORDER BY** average\_dist\_price DESC  
**LIMIT** 5;

## **Author Analysis**

*Authors with multiple books*  
**SELECT** author, COUNT(\*) AS books\_per\_author  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** author\!= ''  
**GROUP** **BY** author  
**ORDER BY** books\_per\_author DESC  
**LIMIT 10**;  
\-- includes different language prints and different edition prints

*How many authors have 5 star books?*  
**SELECT** author, COUNT(stars) AS num\_top\_rated\_books  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars \= 5  
**GROUP** **BY** author  
**ORDER** **BY** num\_top\_rated\_books DESC;  
\--4,614 authors have 5 star books, Olly Richards has the most 5 star books  
*Olly Richards 5 star books*  
**SELECT** title, author  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** author \= 'Olly Richards' AND stars \= 5;

## **Kindle Unlimited (KU) Analysis**

*How many books are available through Kindle Unlimited?*  
**SELECT** COUNT(\*) AS total\_available\_KU\_books  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited \= 'True';

*Which publishers prefer KU?*  
**SELECT** soldBy, COUNT(\*) AS num\_books\_KU  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited \= 'True' AND soldBY \!= ''  
**GROUP** BY soldBy  
**ORDER** BY num\_books\_KU DESC;  
*What are the most/least common categories in KU?*  
**SELECT** category\_name, COUNT(\*) AS KUbooks\_in\_category  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited \= 'True'  
**GROUP** **BY** category\_name  
**ORDER** **BY** KUbooks\_in\_category DESC;  
![][image1]

*KU price vs Non KU price*  
**SELECT** ROUND(AVG(price), 2\) AS average\_KU\_price  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited \= 'True' and price \!= 0;

**SELECT** ROUND(AVG(price), 2\) AS average\_nonKU\_price  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited \= 'False' and price \!= 0;

*Are top rated books on KU?*  
**WITH** rating AS (  
**SELECT** \*,  
**CASE**  
**WHEN** stars \= 5 THEN 'Excellent'  
**WHEN** stars \> 3.9 THEN 'Good'  
**WHEN** stars \> 2.9 THEN 'Average'  
**WHEN** stars \> 1.9 THEN 'Below Average'  
**WHEN** stars \> 0 THEN 'Poor'  
**ELSE** 'NA'  
**END** AS review\_category  
**FROM** \[kindle\_data\_2023 \]  
)  
**SELECT** COUNT(\*) AS total, isKindleUnlimited  
**FROM** rating  
**WHERE** review\_category \= 'Excellent' OR review\_category \= 'Good'  
**GROUP** **BY** isKindleUnlimited;

*Are low rated books on KU?*  
**WITH** rating AS (  
**SELECT** \*,  
**CASE**  
**WHEN** stars \= 5 THEN 'Excellent'  
**WHEN** stars \> 3.9 THEN 'Good'  
**WHEN** stars \> 2.9 THEN 'Average'  
**WHEN** stars \> 1.9 THEN 'Below Average'  
**WHEN** stars \> 0 THEN 'Poor'  
**ELSE** 'NA'  
**END** AS review\_category  
**FROM** \[kindle\_data\_2023 \]  
)  
**SELECT** COUNT(\*) AS total, isKindleUnlimited  
**FROM** rating  
**WHERE** review\_category \= 'Below Average' OR review\_category \= 'Poor'  
**GROUP** **BY** isKindleUnlimited;

*Are average rated books on KU?*  
**WITH** rating AS (  
**SELECT** \*,  
**CASE**  
**WHEN** stars \= 5 THEN 'Excellent'  
**WHEN** stars \> 3.9 THEN 'Good'  
**WHEN** stars \> 2.9 THEN 'Average'  
**WHEN** stars \> 1.9 THEN 'Below Average'  
**WHEN** stars \> 0 THEN 'Poor'  
**ELSE** 'NA'  
**END** AS review\_category  
**FROM** \[kindle\_data\_2023 \]  
)  
**SELECT** COUNT(\*) AS total, isKindleUnlimited  
**FROM** rating  
**WHERE** review\_category \= 'Average'  
**GROUP** **BY** isKindleUnlimited;

*Kindle Unlimited vs author*  
**SELECT** author, COUNT(\*) AS total\_KU\_books  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isKindleUnlimited \= 'True' AND author \!= ''  
**GROUP BY** author  
**ORDER** **BY** total\_KU\_books DESC  
**LIMIT** 10;

*Are editor's choice books on KU?*  
**SELECT** isKindleUnlimited, COUNT(\*) AS total  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isEditorsPick \= 'True'  
**GROUP** **BY** isKindleUnlimited;  
*Are best seller books on KU?*  
**SELECT** isKindleUnlimited, COUNT(\*) AS total  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isBestSeller \= 'True'  
**GROUP** **BY** isKindleUnlimited;

*Are good read choices on KU?*  
**SELECT** isKindleUnlimited, COUNT(\*) AS total  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** isGoodReadsChoice \= 'True'  
**GROUP** **BY** isKindleUnlimited;

## **Book Ratings**

*Average rating per distributor*  
**SELECT** soldBy, ROUND(AVG(stars), 2\) AS average\_dist\_stars  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars \!= 0 AND soldBy \!= ''  
**GROUP** **BY** soldBy  
**ORDER** **BY** average\_dist\_stars DESC  
**LIMIT** 10;  
*Average star amount of editors pick*  
**SELECT** isEditorsPick, ROUND(AVG(stars), 2\) AS average\_ep\_stars  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars \!= 0  
**GROUP** **BY** isEditorsPick;

*Average star amount of good reads*  
**SELECT** isGoodReadsChoice, ROUND(AVG(stars), 2\) AS average\_gr\_stars  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars \!= 0  
**GROUP** **BY** isGoodReadsChoice;

*Average star amount of best seller*  
**SELECT** isBestSeller, ROUND(AVG(stars), 2\) AS average\_bs\_stars  
**FROM** \[kindle\_data\_2023 \]  
**WHERE** stars \!= 0  
**GROUP** **BY** isBestSeller;

# **Conclusions**

* The average KU book price is 30% of a the average non-book price  
* About 35% of all listed books are available on KU  
* The top 5 KU categories include fiction books of various types while the top 5 non-KU books include non-fiction and educational books  
* Star ratings between KU and non-KU books are pretty similar with most books rated around 4 stars  
* Best Sellers are more likely to be available on KU than Editor’s Pick and Good Read’s Choice books

# **Visuals**

[image1]: <data:image/png;base64,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>