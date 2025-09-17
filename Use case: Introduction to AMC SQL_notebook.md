## Metadata

**id:** f53b1ad1359ef811fcdccdc8c6e69422b83bf06a163da53fddf79b6ca729edb6
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Learn about the most commonly used AMC SQL functions.
**tags:** []
**use_case_categories:** ['How to']
**advertiser_types:** None
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 What is AMC SQL? 
AMC SQL is a custom query language. It is designed to help our advertisers interact with event-level data to generate aggregated outputs, which lead to insights for campaign measurement, audience analysis, media optimization, and more.

Advertisers can only access aggregated, pseudonymized outputs from AMC.  All information in an advertiser’s AMC instance is handled in strict accordance with Amazon’s privacy policies, and your own signals cannot be exported or accessed by Amazon. Hence, extracting event-level data from AMC is not permitted. Event-level data refers to individual events like an impression or click from a single user. Aggregated data refers to grouped data. For example: count of all unique users that were served an impression by a campaign. Our advertisers can extract aggregated data, provided that it meets AMC's aggregation thresholds. The AMC query compiler checks if the thresholds are met and ensures data is pulled in a privacy safe way. For example, AMC does not support SELECT * for any of the tables. 

**Note:** This IQ provides a basic overview of most commonly used AMC SQL functionalities and is not an exhaustive list. For a more complete list of supported functions, refer to [AMC SQL reference](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/amc-sql/overview).

### 1.2 What are aggregation thresholds? 
Each column is classified into a category based on the combination of the aggregation threshold and the filtering restrictions. For simplicity, this classification is referred to as the aggregation threshold. All categories can be found in the [AMC Datasets](https://dbxijvaqtjcqg.cloudfront.net/images/2024/AMC+Migration/AMC_Datasets.xlsx) file under the column ‘aggregation threshold’. The classifications are also in the schema explorer. Refer to [Aggregation thresholds](https://advertising.amazon.com/help/G6ZYAPTTTE54UQPP) to understand more on aggregation thresholds. 


## 2. SQL basics
### 2.1 SELECT

#### Introduction

The SELECT statement is used to select data from a database.

#### Syntax

```
SELECT <column1>, <column2>, ...
FROM <table1>
```

* \<column1\>, \<column2\> refer to the field names in the table 
* \<table1\> refers to the table name that you want to select data from

AMC does not support selecting all columns from a table so SELECT * is not supported, due to privacy reasons.


### 2.2 DISTINCT

#### Introduction

The SELECT DISTINCT statement is used to return only distinct (unique) values from a column in a database.

#### Syntax

```
SELECT DISTINCT <column1>, <column2>, ...
FROM <table1>
```

* \<column1\>, \<column2\> refer to the field names in the table 
* \<table1\> refers to the table name that you want to select data from

### 2.3 FROM

#### Introduction

The FROM statement is used to specify the table to select the data from.

#### Syntax

```
SELECT DISTINCT <column1>, <column2>, ...
FROM <table1>
```

* \<column1\>, \<column2\> refer to the field names in the table 
* \<table1\> refers to the table name that you want to select data from

### 2.4 JOIN

#### Introduction

AMC supports the JOIN clause which combines rows from two or more tables, based on a matching column between them.

#### Syntax

```
SELECT <table1>.<column1>, <table2>.<column2>, <table2>.<column1>,....
FROM table1
JOIN table2
ON <table1>.<matching_column1> = <table2>.<matching_column2>
```

* \<table1\> refers to the first table
* \<column1\> refers to a column in the first table
* \<table2\> refers to the second table
* \<column2\> refers to a column in the second table
* \<matching_column1\> refers to a column in table1 that should be used to match with a column in table2
* \<matching_column2\> refers to a column in table 2 that should be used to match with a column in table1

#### Inner Join / Join

This JOIN returns only the matching rows from both the tables in the JOIN.

#### Query Template



```
-- Instructional Query: How to use INNER JOIN()--

SELECT
    I.campaign_id_string,
    I.campaign,
    SUM(impressions) AS impressions,
    SUM(C.clicks) AS clicks
FROM
    dsp_impressions I
JOIN 
    dsp_clicks C
ON 
    I.campaign_id_string = C.campaign_id_string
WHERE 
    I.campaign IN ('Campaign1', 'Campaign2')
GROUP BY
    1,
    2
```

#### Example Query Results
| campaign_id_string    | campaign                                                 | impressions | clicks |
|----------------|----------------------------------------------------------|-------------|--------|
| 1111111111111 | Kitchen Smart \| All Products \| Awareness               | 1165010     | 50000  |
| 2222222222222    | Kitchen Smart \| Toasters \| Awareness                   | 5121073     | 29371  |
| 3333333333333    | Kitchen Smart \| Blenders \| Category Views \| ASIN56789 | 936365      | 763883 |
| 4444444444444    | Kitchen Smart \|  Blenders \| Remarketing \| ASIN12345   | 4894441     | 32461  |



#### Left Join/ Left Outer Join

This JOIN returns all the rows of the table on the left side of the join and matching rows for the table on the right side of the join. For the rows that don’t match, it will return Null as the matched values.

#### Query Template



```
-- Instructional Query: How to use LEFT JOIN()--

WITH imp_dv AS (
    SELECT
        campaign,
        campaign_id_string,
        SUM(viewable_impressions) AS viewable_impressions,
        SUM(measurable_impressions) AS measurable_impressions,
        SUM(viewable_impressions) / SUM(measurable_impressions) AS viewability_rate
    FROM 
        dsp_views
    GROUP BY 
        1,
        2
), 

imp_di AS (
    SELECT
        campaign,
        campaign_id_string,
        SUM(impressions) AS impressions
    FROM 
        dsp_impressions
    GROUP BY 
        1,
        2
)

SELECT
    di.campaign,
    di.campaign_id_string,
    impressions,
    viewable_impressions,
    measurable_impressions,
    viewability_rate
FROM 
    imp_di di
LEFT JOIN 
    imp_dv dv 
ON 
    di.campaign = dv.campaign AND di.campaign_id_string = dv.campaign_id_string
```

#### Example Query Results

| campaign                                                            | campaign_id_string    | impressions | viewable_impressions | measurable_impressions | viewability_rate |
|---------------------------------------------------------------------|----------------|-------------|----------------------|------------------------|------------------|
| Kitchen Smart \| OLV \| All Products \| Awareness                   | 1111111111111 | 40536       | 26652                | 40075                  | 0.66505          |
| Kitchen Smart \| STV \| Toasters \| Awareness                       | 2222222222222    | 25115       | 5577                 | 25059                  | 0.22255          |
| Kitchen Smart \| Display \| Blenders \| Category Views \| ASIN56789 | 3333333333333    | 1200690     | 874562               | 1153739                | 0.75802          |
| Kitchen Smart \| OLV \| Blenders \| Remarketing \| ASIN12345        | 4444444444444    | 25338       | 5239                 | 25301                  | 0.20707          |



#### Right Join / Right Outer Join

This JOIN returns all the rows of the table on the right side of the join and matching rows for the table on the left side of the join. For the rows that don’t match, it will return Null as the matched values.
AMC does not support RIGHT JOIN. As a workaround, please use LEFT JOIN and swap the tables.

#### Example IQs

[Instructional Query: DSP Path to Conversion by Device](/marketing-cloud/instructional-queries/010ebf2f898de2059585d432e60b0e86f4891b9f7371f0bd297347024ccf2e20)

#### Full Outer Join

The FULL OUTER JOIN returns all the matched and unmatched rows from both the tables.

#### Query Template




```
-- Instructional Query: How to use FULL OUTER JOIN --
-- Use Case: Retrieve all matched and unmatched tracked ASINs from Sponsored Products and DSP--


-- Collect Sponsored Product ASINS --
WITH SP_asins AS (
SELECT
DISTINCT(tracked_asin) AS ta,
campaign_id_string,
campaign
FROM
amazon_attributed_events_by_conversion_time
WHERE
ad_product_type in ('sponsored_products')),

-- Collect DSP ASINS --
dsp_asins AS (
SELECT
DISTINCT(tracked_asin) AS ta,
campaign_id_string,
campaign
FROM
amazon_attributed_events_by_conversion_time
WHERE
ad_product_type is NULL)


-- FULL OUTER --
SELECT
SP.ta AS SP_ASINs, SP.campaign as SP_Campaign_Name, SP.campaign_id_string as SP_campaign_id_string,
D.ta AS DSP_ASINs, D.campaign as DSP_Campaign_Name, D.campaign_id_string as DSP_campaign_id_string
FROM
SP_asins SP
FULL OUTER JOIN
dsp_asins D
ON
SP.ta = D.ta

```

#### Example Query Results

| sp_asins  | sp_campaign_name     |sp_campaign_id_string |    dsp_asins | dsp_campaign_name   | dsp_campaign_id_string                                      |
|-----------|-------------------------------------------------------|----|-----------|-----------------|--------------------------------------------------------|
| ASIN12345 | Kitch Smart \| PC \| Toasters \| SP \| Brand \| Exact | A0011111111111| ASIN12345 |  Kitchen Smart \| Toasters \| Consideration             | 1111111111111 |
| ASIN55555 | Kitchen Smart \| PC \| Blenders \| SP \| Brand        |A00222222222 | ASIN55555 | Kitchen Smart \| Blenders \| Consideration             |2222222222222     |  
| ASIN8888  | Kitchen Smart \| Mobile \| Air Fryer \| SP           |A00333333333 | ASIN7777  |  Kitchen Smart \| Air Fryers \| Remarketing \| ASIN7777 | 3333333333333        |


#### Cross Join

A CROSS JOIN returns the cartesian product of the rows from the tables that are joined.

#### Query Template



```
-- Instructional Query: How to use CROSS JOIN --
-- Use Case: Retrieve all possible ASIN combinations that can be purchased together --

WITH asin_CTE AS (
    SELECT 
        distinct(tracked_asin) as asin
    FROM
        amazon_attributed_events_by_conversion_time
    WHERE
        purchases > 0
    AND user_id IS NOT NULL
)

SELECT 
    A.asin as Asin1, B.asin as Asin2
FROM 
    asin_CTE A
CROSS JOIN 
    asin_CTE B
WHERE 
    A.asin <> B.asin
```

| Asin1      | Asin2    |
|------------|----------|
| ASIN1111 | ASIN2222 |
| ASIN2222   | ASIN3333 |
| ASIN3333   | ASIN2222 |
| ASIN1111   | ASIN3333 |
| ASIN3333   | ASIN1111 |


#### Non-Equi Join

A Non-Equi JOIN retrieves data from multiple tables by matching column values between them based on an inequality operator like >, <, != etc.

#### Syntax

```
SELECT <table1>.<column1>, <table2>.<column2>, <table2>.<column1>,....
FROM table1
JOIN table2
ON <table1>.<matching_column1> [> | <| >= | <=] <table2>.<matching_column2>
```

* \<table1\> refers to the first table
* \<column1\> refers to a column in the first table
* \<table2\> refers to the second table
* \<column2\> refers to a column in the second table
* \<matching_column1\> refers to a column in table1 that should be used to match with a column in table2
* \<matching_column2\> refers to a column in table 2 that should be used to match with a column in table1

#### Query Template


```
-- Instructional Query: How to use NON-EQUI JOIN --
-- Use Case: Retrieve a count of customers who did not Purchase after adding to Shopping Cart --
WITH conv AS(
  SELECT
    user_id,
    user_id_type,
    event_subtype,
    MAX(event_dt_utc) AS dt_max
  FROM
    conversions
  WHERE
    event_subtype IN ('order', 'shoppingCart')
  GROUP BY
    1,
    2,
    3
)
SELECT
  COUNT(DISTINCT(a.user_id))
FROM
  conv a
  LEFT JOIN conv b ON a.user_id = b.user_id
  AND a.user_id_type = b.user_id_type
  AND a.event_subtype = 'shoppingCart'
  AND b.event_subtype = 'order'
  AND a.dt_max > b.dt_max
```

### 2.5 UNION

#### Introduction

UNION combines the data from two or more subqueries. UNION keeps unique records. UNION ALL keeps all records, including duplicate records.

#### Syntax

```
SELECT <column1>, <column2>....,<columnn>
FROM table1
UNION 
SELECT <column1>, <column2>....,<columnn>
FROM table2
```


#### Query Template



```
-- Instructional Query: How to use UNION --

WITH combined AS ( 
    SELECT
        campaign_id_string,
        campaign,
        SUM(impressions) AS impressions,
        0 as clicks
    FROM dsp_impressions
    GROUP BY 
        campaign_id_string,
        campaign

    UNION ALL 

    SELECT
        campaign_id_string,
        campaign,
        0 as impressions,
        SUM(clicks) as clicks
    FROM dsp_clicks
    GROUP BY 
        campaign_id_string,
        campaign
)

SELECT
    campaign_id_string,
    campaign,
    sum(impressions) AS impressions,
    sum(clicks) AS clicks
FROM 
    combined
GROUP BY 
    campaign_id_string,
    campaign

```

#### Example Query Results

| campaign_id_string    | campaign                                                 | impressions | clicks |
|----------------|----------------------------------------------------------|-------------|--------|
| 1111111111111 | Kitchen Smart \| All Products \| Awareness               |     1165010 |  50000 |
| 2222222222222    | Kitchen Smart  \| Toasters \| Awareness                  |     5121073 |  29371 |
| 3333333333333    | Kitchen Smart \| Blenders \| Category Views \| ASIN56789 |      936365 | 763883 |
| 4444444444444    | Kitchen Smart \| Blenders \| Remarketing \| ASIN12345    |     4894441 |  32461 |
| 5555555555555    | Kitchen Smart \| Air Fryers \| Remarketing \| ASIN789    |     4053667 |  36667 |

#### JOIN vs UNION

JOIN clause combines rows from two or more tables, based on a matched column between them.
UNION clause combines the results obtained from two queries

| JOIN                                                                                                                                                                                                                                                                                                                                                 | UNION                                                                                                                                                                                                             |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Combines rows from two or more tables, based on a matched column between them                                                                                                                                                                                                                                                                        | Combines results of two or more queries or SELECT statements                                                                                                                                                         |
| The two or more tables used in the JOIN may have different columns, with 1 foreign key for JOINing to each other                                                                                                                                                                                                                                     | The two or more SELECT statements used in the UNION should have the same number and data type of columns                                                                                                             |
| The result set may have more columns as the JOIN tries to match and retrieve data from the JOINed table. If a row from the first table has keys that match multiple rows in the second table, there will be a row in the Example Query Results table for each combination. The result set may or may not have the same number of rows as the tables used in the JOIN | A UNION will have all the rows from the first data source + all the rows from the second. Hence, the result set will have more rows, but the same set of columns, as the UNION consolidates the data in a single set. UNION keeps unique records. UNION ALL keeps all records, including duplicate records |


#### Example IQs

[Instructional Query: Sponsored Ads and DSP Overlap](/marketing-cloud/instructional-queries/dfc41258bf5adb1ac1b1d7aa5b734b8af4a2e9b3487468ef84ebafbff096ad22)


### 2.6 WHERE

#### Introduction

The WHERE clause is used to filter the query results based on a condition.

#### Syntax

```
SELECT <column1>, <column2>, ...
FROM <table1>
WHERE <condition>
```

* \<column1\>, \<column2\> refer to the field names in the table that you want to select data from
* \<table1\> refers to the table name
* \<condition\> refers to the condition that should be true to filter down the query results




### 2.7 GROUP BY


#### Introduction

The GROUP BY statement is used with aggregate functions  MIN(), MAX(), SUM(), AVG() etc. to group the data by one or more columns.

#### Syntax

```
SELECT <column1>, <FUNC>( <column2>), ...
FROM <table1>
WHERE <condition1>
GROUP BY <column1>
```

* \<column1\>, \<column2\> refer to the field names in the table that you want to select data from
* \<table1\> refers to the table name
* \<FUNC\> refers to an aggregated function like MIN(), MAX() etc.
* \<condition1\> refers to the condition that should be true to filter down the query results

### 2.8 HAVING


#### Introduction

The HAVING clause is similar to the WHERE clause as it helps filter the data based on specific conditions. HAVING clause can be used with aggregate functions, whereas a WHERE clause can not. 

#### Syntax

```
SELECT <column1>, <FUNC>( <column2>), ...
FROM <table1>
WHERE <condition1>
GROUP BY <column1>
HAVING <condition2>
```

* \<column1\>, \<column2\> refer to the field names in the table that you want to select data from
* \<table1\> refers to the table name
* \<FUNC\> refers to an aggregated function like MIN(), MAX() etc.
* \<condition1\> and \<condition2\> refer to the condition that should be true to filter down the query results

### 2.9 ALIASES

#### Introduction

Aliases are used to give a table, or a column, a temporary name.

#### Syntax

```
SELECT <column1> as <alias_name1>, <column2>, ...
FROM <table1> <alias_name2>
```

* \<column1\>, \<column2\> refer to the field names in the table that you want to select data from
* \<table1\> refers to the table name


### 2.10 VALUES

#### Introduction

The VALUES command specifies the values for including in a common table expression. Most AMC IQs use Common Table Expressions to define values to filter the queries.

#### Syntax

```
WITH <table1> ( <column1>) AS (
  VALUES
    (<value1>),
    (<value2>),
    (<value3>)
)
```

* \<column1\> refers to the field names in the table
* \<table1\> refers to the temporary table name
* \<value1\>, \<value2\> etc. refers to the values that will be included in the table

#### Query Template



```
-- Instructional Query: How to use VALUES --

WITH dsp_campaigns (campaign_id_string) AS (
    VALUES
    (1111111111111),
    (222222222222),
    (333333333333)
    )

SELECT
    DISTINCT campaign_id_string
FROM 
    dsp_campaigns

```

#### Example Query Results

| campaign_id_string  |
|--------------|
| 1111111111111 |
| 2222222222222 |
| 3333333333333 |

#### Example IQs

[Instructional Query: ASIN Purchase Overlap for Upselling](/marketing-cloud/instructional-queries/49a5379dd736f7b2ea17157b87feb26274b487592662609310082a9f8e0019ec)

### 2.11 REGEX

#### Introduction

Regex, or Regular Expressions, is a sequence of characters that are used to construct search strings or patterns that can be used for filtering data. A Regex can be a combination of integers, special characters etc.

| Pattern | Usage               | Example                | Explanation                                                      |
|---------|---------------------|------------------------|------------------------------------------------------------------|
| ^       | Begins with         | ^Toast                 | Begins with toast. Example: toaster                              |
| (?!)    | Case Insensitive    | (?!)consideration      | This will match with Consideration, CONSIDERATION, conSIDerATION |
| ‘       | Escape single quote | men''s electric shaver | This will match men’s electric shaver                            |
| “       | Escape double quote | red 48'''' rod         | This will match red 48" rod                                      |

#### Example IQs

[Instructional Query: How to Filter Text Strings](/marketing-cloud/instructional-queries/3af1b6a0e73199b83e1a988ed6045cd77a116efe7571718b1eb9aeb5f83df180)

### 2.12 COMMON TABLE EXPRESSION

#### Introduction

A Common Table Expression or a CTE is a temporary result set derived using a query, which can then be referenced in a SELECT statement as a table. Note that there is a comma seperating multiple CTE definitions and no comma prior to the terminal query.


#### Syntax
```
WITH <cte_name1> 
AS (<query>),

<cte_name2> 
AS (<query>)

SELECT <column1>,<column2> 
FROM <cte_name>
```

* \<cte_name\> refers to the name of the temporary table
* \<query\> refers to the query used to derive the result set 

####  Instructional Query



```
-- Instructional Query: How to use CTEs --
-- Collect Sponsored Display ASINS --

WITH SD_asins AS (
SELECT
DISTINCT(tracked_asin) AS ta,
campaign_id_string,
campaign
FROM
amazon_attributed_events_by_conversion_time
WHERE
ad_product_type in ('sponsored_display')),

-- Collect Sponsored Product ASINS --

SP_asins AS(
SELECT
DISTINCT(tracked_asin) AS ta,
campaign_id_string,
campaign
FROM
amazon_attributed_events_by_conversion_time
WHERE
ad_product_type in ('sponsored_products'))

SELECT
SD.campaign as SD_campaign,
SP.campaign as SP_campaign,
SD.ta as Tracked_ASIN
FROM
SD_asins SD
JOIN
SP_asins SP
ON
SD.ta = SP.ta

```

#### Example Query Results

| sd_campaign                                                            | sp_campaign                                                             | tracked_asin |
|------------------------------------------------------------------------|-------------------------------------------------------------------------|--------------|
| Campaign Kitchen Smart \| SD \| All Products \| Awareness              | Campaign Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | ASIN789      |
| Campaign Kitchen Smart \| SD \| Toasters \| Awareness                  | Campaign Kitchen Smart \| SP \| Blenders \| Remarekting \| ASIN12345    | ASIN12345    |
| Campaign Kitchen Smart \| SD\| Blenders \| Category Views \| ASIN56789 | Campaign Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | ASIN56789    |


#### Example IQs

[Instructional Query: ASIN Purchase Overlap for Upselling](/marketing-cloud/instructional-queries/49a5379dd736f7b2ea17157b87feb26274b487592662609310082a9f8e0019ec)



## 3. String functions

### 3.1 CONCAT()

#### Introduction

AMC supports the CONCAT() function which concatenates multiple strings into a single string. All inputs will be cast to string data type for joining. 

#### Syntax

```
CONCAT( <expression1>, <expression2> ... , <expressionN>)

```
* \<expression1\> refers to the string data to be concatenated with expression2.. expressionN etc

#### Query Template





```
-- Instructional Query: How to use CONCAT()--

SELECT 
    CONCAT('Campaign ', campaign) as Campaign, 
    COUNT(distinct (user_id)) AS Distinct_User_count
FROM 
    amazon_attributed_events_by_conversion_time 
GROUP BY 
    1
```

#### Example Query Results

| campaign                                                                | Distinct_User_count |
|-------------------------------------------------------------------------|---------------------|
| Campaign Kitchen Smart \| SD \| All Products \| Awareness               | 1678                |
| Campaign Kitchen Smart \| SD \| Toasters \| Awareness                   | 900                 |
| Campaign Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 1729                |
| Campaign Kitchen Smart \| SP \| Blenders \| Remarekting \| ASIN12345    | 588                 |
| Campaign Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 5099                |


### 3.2 TRIM()

#### Introduction

AMC supports the TRIM() function which removes the space character or other specified characters from the start or end of a string. This function is useful when working with strings like advertiser or campaign names.

#### Syntax

Removes the leading and trailing space characters from str
```
TRIM(str)
```
Remove the leading and trailing trimStr characters from str
```
TRIM(BOTH trimStr FROM str)
```
Remove the leading trimStr characters from str
```
TRIM(LEADING trimStr FROM str)
```
Remove the trailing trimStr characters from str
```
TRIM(TRAILING trimStr FROM str)
```

#### Example IQs

[Instructional Query: How to Trim Strings](/marketing-cloud/instructional-queries/26834dced8b79ca6eb8058a72665403c43d685c3506eb8fece881a0aad273798)

### 3.3 REGEXP_SPLIT()

#### Introduction

AMC supports the function REGEXP_SPLIT which splits an input string into a collection of substrings, using a regular expression pattern occurrence to split the string.

#### Syntax

```
REGEXP(<string>,<pattern>)
```
* \<string\> The input string that will be split into a collection of substrings
* \<pattern\> The regular expression pattern that is matched to split the string

#### Query Template

    


```
-- Instructional Query: How to use REGEXP_SPLIT --

WITH test_campaigns (campaign) AS (
  VALUES
    ('Testing_Campaign_1'),
    ('Testing_Campaign_2')
),
test_split AS (
  SELECT
    campaign,
    regexp_split(campaign, '_') AS split_campaign
  FROM
    test_campaigns
)
SELECT
  campaign,
  split_campaign
FROM
  test_split
```

#### Example Query Results

|campaign|split_campaign|
|---|---|
|Testing_Campaign_1|[Testing, Campaign, 1]|
|Testing_Campaign_2|[Testing, Campaign, 2]|



### 3.4 SIMILAR TO

#### Introduction

AMC supports the SIMILAR TO operator which matches a column name to a string value specified by the user. SIMILAR TO requires regex syntax and does not work with wildcards. 

#### Syntax
```
<column1> SIMILAR TO <expression> 
```
* \<column1\> specifies the column that is in the WHERE clause that needs to be checked for a condition
* \<expression\> refers to the expression or value that the column needs to be compared to

#### Query Template


```
-- Instructional Query: How to use SIMILAR TO operator --
-- The query below will return all campaigns that begin with "Kit" --

SELECT 
    campaign,
    campaign_id_string,
    SUM(impressions) AS impressions
FROM
    dsp_impressions
WHERE
    campaign SIMILAR TO '^Kit'
GROUP BY
    1,
    2
```

#### Example Query Results

| campaign                                                    | campaign_id_string                                              | impressions | 
|-------------------------------------------------------------|--------------------------------------------------------|-------------|
| Kitchen Smart \| SD \| Toasters \| Awareness                | A001111111111             | 7778223     |
| Kitchen Smart \| SD \| Toasters \| Awareness                | A002222222222 | 4555112     | 
| Kitchen Smart \| SD \| Toasters \| Awareness                | A003333333333 | 321108      |
| Kitchen Smart \| SD \| Toasters \| Awareness                | A004444444444              | 112222      |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | A005555555555           | 3455123     |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | A006666666666   | 2722789     | 
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | A007777777777            | 2222888     | 


### 3.5 LIKE

#### Introduction

AMC supports the LIKE operator which matches a column name to a string value specified by the user. LIKE does not require regex syntax and works with wildcards. 


#### Syntax
```
<column1> LIKE <expression> 
```
* \<column1\> specifies the column that is in the WHERE clause that needs to be checked for a condition
* \<expression\> refers to the expression or value that the column needs to be compared to

#### Query Template


```
-- Instructional Query: How to use LIKE operator --
-- The query below will return all campaigns that begin with "Kit" --

SELECT 
    campaign,
    campaign_id_string,
    SUM(impressions) AS impressions
FROM
    dsp_impressions
WHERE
 campaign LIKE 'Kit%' -- % represents zero or more characters

GROUP BY
    1,
    2
```

#### Example Query Results

| campaign                                                    | campaign_id_string                                              | impressions | 
|-------------------------------------------------------------|--------------------------------------------------------|-------------|
| Kitchen Smart \| SD \| Toasters \| Awareness                | A001111111111             | 7778223     |
| Kitchen Smart \| SD \| Toasters \| Awareness                | A002222222222 | 4555112     | 
| Kitchen Smart \| SD \| Toasters \| Awareness                | A003333333333 | 321108      |
| Kitchen Smart \| SD \| Toasters \| Awareness                | A004444444444              | 112222      |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | A005555555555           | 3455123     |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | A006666666666   | 2722789     | 
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | A007777777777            | 2222888     | 


## 4. Math and numeric functions

### 4.1 ROUND()

#### Introduction

AMC supports the ROUND() function which rounds a number to a specified number of decimal places

#### Syntax
```
ROUND( <number>, <decimals>)
```

* \<number\> specifies the number that is to be rounded
* \<decimals\> is optional and specifies the number of decimal places to round the \<number\> to
* Result of input expression must be a numeric data type. If no rounding is specified, will default to 0 

#### Query Template 
    


```
-- Instructional Query: How to use ROUND()--

SELECT 
    ROUND(5.5) AS Round1, --Round 5.5, No Decimals Specified
    ROUND(5.5,0) AS Round2, --Round 5.5, 0 Decimals Specified
    ROUND(5.55555,1) AS Round3,--Round 5.5, 1 Decimals Specified
    ROUND(5.55555,2) AS Round4,--Round 5.5, 2 Decimals Specified
    ROUND(5.5+.2) AS Round5 --Round the sum of 5.5+.2, No Decimals Specified


```

#### Example Query Results

| round1 | round2 | round3 | round4 | round5 |
|--------|--------|--------|--------|--------|
| 6      | 6      | 5.6    | 5.56   | 6      |



## 5. Aggregate functions 

### 5.1 MIN()

#### Introduction

AMC supports the MIN() function which returns the smallest value of the selected column.

#### Syntax

```
MIN(<column1>)
```

* \<column1\> refers to the column that should be used to grab the largest value from
* MIN\(\) function can be used with the GROUP BY clause if there are non-aggregate columns in the SELECT statement
* Result is the minimum value of all inputs. Nulls will not be considered. Result will be null if all input values are null. 

#### Query Template


```
-- Instructional Query: How to use MIN() --

WITH campaigns AS (
    SELECT 
        campaign, 
        line_item, 
        SUM(impressions) AS impressions 
    FROM 
        dsp_impressions 
    GROUP BY 
        1,2
    )


SELECT 
    campaign, 
    MIN(impressions) as min_line_item_impressions, 
    MAX(impressions) as max_line_item_impressions 
FROM 
    campaigns 
GROUP BY 
    1
```

#### Example Query Results

| campaign                                                       | min_line_item_impressions | max_line_item_impressions |
|----------------------------------------------------------------|---------------------------|---------------------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | 3456                      | 34466                     |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | 7249                      | 46888                     |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 6789                      | 40505                     |
| Kitchen Smart \| SP \| Blenders \| Remarekting \| ASIN12345    | 7413                      | 155577                    |
| Kitchen Smart \| SP \| Air Fryers \| Remarekting \| ASIN789    | 400                       | 78980                     |

#### Example IQs

[Sponsored Ads and DSP Overlap](/marketing-cloud/instructional-queries/dfc41258bf5adb1ac1b1d7aa5b734b8af4a2e9b3487468ef84ebafbff096ad22)




### 5.2 MAX()

#### Introduction

AMC supports the MAX() function which returns the largest value of the selected column.

#### Syntax

```
MAX(<column1>)
```

* \<column1\> refers to the column that should be used to grab the largest value from
* MAX() function can be used with the GROUP BY clause if there are non-aggregate columns in the SELECT statement
* Result is the maximum value of all inputs. Null values will not be considered. Result will be null if all input values are null. 

#### Query Template


```
-- Instructional Query: How to use MAX()--

WITH campaigns AS
(
    SELECT campaign, line_item, sum(impressions) AS impressions 
    FROM dsp_impressions 
    GROUP BY 1,2
)


SELECT 
    campaign, 
    MIN(impressions) as min_line_item_impressions, 
    MAX(impressions) as max_line_item_impressions 
FROM 
    campaigns 
GROUP BY 
    1
```

#### Example Query Results

| campaign                                                       | min_line_item_impressions | max_line_item_impressions |
|----------------------------------------------------------------|---------------------------|---------------------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | 370                       | 18466                     |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | 7249                      | 46744                     |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 1518                      | 40505                     |
| Kitchen Smart \| SP \| Blenders \| Remarekting \| ASIN12345    | 7413                      | 154931                    |
| Kitchen Smart \| SP \| Air Fryers \| Remarekting \| ASIN789    | 78                        | 68054                     |


### 5.3 SUM()

#### Introduction

AMC supports the SUM() function which returns the sum of the column values for a set of rows. Input to the SUM function must be a numeric data type. Sum will return Null results if all input values are null. 


#### Syntax

```
SELECT SUM( <column1>) AS column1
FROM <table> 
```

* \<column1\> specifies the field that should be used as a reference for counting the rows 
 
SUM() can be used with a WHERE clause to specify the condition that should be used for counting the rows

```
SELECT SUM( <column1>)
FROM table_name
WHERE condition
```

SUM() can be also be used with a GROUP BY clause if you have other non-aggregated columns in your Select statement:

```
SELECT <column1>, SUM <column2>
FROM table_name
WHERE condition
GROUP BY 1
```

#### Query Template


```
-- Instructional Query: How to use SUM() --

SELECT
    campaign,
    campaign_id_string,
    SUM(impressions) AS impressions
FROM
    dsp_impressions
GROUP BY
    1,
    2
```

#### Example Query Results

| campaign                                                       | campaign_id_string   | impressions |
|----------------------------------------------------------------|---------------|-------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | 1111111111111  | 3,794,695   |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | 2222222222222  | 2,813,247   |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 3333333333333  | 1,234,788   |
| Kitchen Smart \| SP \| Blenders \| Remarekting \| ASIN12345    | 2456167530601 | 1,853,009   |
| Kitchen Smart \| SP \| Air Fryers \| Remarekting \| ASIN789    | 8697685000701 | 3,794,695   |

#### Example IQs


[Instructional Query: DSP Impression Frequency and Conversions](/marketing-cloud/instructional-queries/2b13b611c86b51a98ea3027822557df0b8a1a994272e2173a3b426214cf8f714)




### 5.4 AVG()

#### Introduction

AMC supports the AVG() function which returns the average value of the column. NULL values are ignored in the calculation. 

#### Syntax
```
AVG(<column_name>)
```

* \<column_name\> specifies the column that should be averaged


#### Query Template


```
-- Instructional Query: How to use AVG()--   

WITH campaigns AS
(
    SELECT 
        campaign, 
        line_item, sum(impressions) AS impressions 
    FROM 
        dsp_impressions 
    GROUP BY 
        1,2
)


SELECT 
    campaign, 
    MIN(impressions) as min_line_item_impressions, 
    AVG(impressions) as avg_line_item_impressions,  
    MAX(impressions) as max_line_item_impressions 
FROM 
    campaigns 
GROUP BY 
    1
```

#### Example Query Results

| campaign                                                       | min_line_item_impressions | avg_line_item_impressions | max_line_item_impressions0 |
|----------------------------------------------------------------|---------------------------|---------------------------|----------------------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | 344                       | 4402.889                  | 13889                      |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | 8300                      | 38299.123                 | 89406                      |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 456                       | 5615.99                   | 18499                      |
| Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | 2000                      | 45111.89                  | 98000                      |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 2334                      | 14556.88                  | 50000                      |


### 5.5 COUNT()

#### Introduction

AMC supports the COUNT() function which returns the count of non-null values of the column.

#### Syntax
```
COUNT(<column_name>)
```
* \<column_name\> specifies the field that should be used as a reference for counting the rows 

COUNT() can be used with a WHERE clause

```
SELECT COUNT(<column_name>)
FROM table_name
WHERE condition
```

COUNT() can be used with a GROUP BY clause if you have non-aggregated columns in your select statement

```
SELECT COUNT( <column1>), <column2> 
FROM table_name
GROUP BY 2
```

COUNT() can also be used with the DISTINCT clause


#### Query Template




```
-- Instructional Query: How to use COUNT() --

SELECT 
    campaign, 
    COUNT(distinct (user_id)) AS Distinct_User_count
FROM 
    amazon_attributed_events_by_conversion_time 
GROUP BY 
    1
```

#### Example Query Results

| campaign                                                       | Distinct_User_count |
|----------------------------------------------------------------|---------------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | 33456               |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | 423211              |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 33422               |
| Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | 55622               |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 11233               |

#### Example IQs

[Instructional Query: DSP Impression Frequency and Conversions](/marketing-cloud/instructional-queries/2b13b611c86b51a98ea3027822557df0b8a1a994272e2173a3b426214cf8f714)

[Instructional Query: DSP Display, Streaming TV and Sponsored Products Three Way Overlap](/marketing-cloud/instructional-queries/2bb8b4ee5bf73bd88afe7da2596e4b02a84fa67ea8719647367cfac1ebed4cbc)



## 6. Date and time functions

### 6.1 SECONDS_BETWEEN()

#### Introduction

AMC supports the SECONDS_BETWEEN() function which returns the number of seconds between the 2 timestamps. This function returns number of seconds between two inputs temporal timestamps by calculating the difference as (<timestamp2> - <timestamp1>).

#### Syntax

```
SECONDS_BETWEEN( <timestamp1>, <timestamp2>)
```

* The \<timestamp1\> value specifies the starting time stamp.
* The \<timestamp2\> value specifies the ending time stamp.

#### Query Template




```
-- Instructional Query: How to use SECONDS_BETWEEN() --

SELECT
    advertiser,
    campaign,
    (
     CASE
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 60 THEN '1 | < 1 MIN'
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 600 THEN '2 | 1 - 10 MIN'
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 1800 THEN '3 | 10 - 30 MIN'
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 3600 THEN '4 | 30 - 60 MIN'
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 7200 THEN '5 | 1 - 2 HRS'
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 43200 THEN '6 | 2 - 12 HRS'
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 86400 THEN '7 | 12 - 24 HRS'
      WHEN SECONDS_BETWEEN (impression_dt, conversion_event_dt) <= 604800 THEN '8 | 1 - 7 DAYS'
      ELSE '9 | 7+ DAYS'
      END
    ) AS time_to_conversion,
    SUM(purchases) AS purchases,
    SUM(total_purchases) AS total_brand_purchases
FROM
    amazon_attributed_events_by_conversion_time 
GROUP BY
    1,
    2,
    3
```

#### Example Query Results

| advertiser    | campaign                                                       | time_to_conversion | purchases | total_brand_purchases |
|---------------|----------------------------------------------------------------|--------------------|-----------|-----------------------|
| Kitchen Smart | Kitchen Smart \| SD \| All Products \| Awareness               | 8 \| 1 - 7 DAYS    | 56        | 200                   |
| Kitchen Smart | Kitchen Smart \| SD \| Toasters \| Awareness                   | 8 \| 1 - 7 DAYS    | 67        | 937                   |
| Kitchen Smart | Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 1 \| < 1 MIN       | 334       | 3345                  |
| Kitchen Smart | Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | 2 \| 1 - 10 MIN    | 878       | 14112                 |
| Kitchen Smart | Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 3 \| 10 - 30 MIN   | 445       | 23456                 |

#### Example IQs

[Instructional Query: Time to Conversion](/marketing-cloud/instructional-queries/e4b1c626c51e5fb3365f76c54d3e3ec6da9c739b86c4a8b6614b776e208d9368)




### 6.2 EPOCH_TO_UTC_TIMESTAMP()

#### Introduction

AMC supports the EPOCH_TO_UTC_TIMESTAMP() function which produces a timestamp value from the input time. 


#### Syntax

```
EPOCH_TO_UTC_TIMESTAMP(<expression>)
```

* \<expression\> refers to the arguments passed to the function. The function will produce a timestamp value from the input time



#### Query Template




```
-- Instructional Query: How to use EPOCH_TO_UTC_TIMESTAMP() --

SELECT 
    EPOCH_TO_UTC_TIMESTAMP('1666735860') AS UTC_time
```

#### Example Query Results


| utc_time                 |
|--------------------------|
| 2022-10-25T22:11:00.000Z |





### 6.3 DATE_TRUNC

#### Introduction

AMC supports the DATE_TRUNC function that  truncates time to specified level of granularity. Granularity must be single quoted and can be one of SECOND, MINUTE, HOUR, DAY, WEEK, MONTH, QUARTER, YEAR. 


#### Syntax

```
DATE_TRUNC(‘<time_unit>’, <timestamp_datatype_column> )
```

* <time_unit> refers to the unit of time like Day, Hour, Minute etc.
* <timestamp_datatype_column> refers to the column of datatype timestamp

#### Query Template




```
-- Instructional Query: How to use DATE_TRUNC function --

SELECT 
    DISTINCT(DATE_TRUNC('day',conversion_event_dt)) AS Day_UTC
FROM 
    amazon_attributed_events_by_conversion_time 
```

#### Example Query Results

| day_utc                  |
|--------------------------|
| 2022-09-13T00:00:00.000Z |
| 2022-09-14T00:00:00.000Z |
| 2022-09-20T00:00:00.000Z |
| 2022-09-25T00:00:00.000Z |
| 2022-09-16T00:00:00.000Z |
| 2022-09-15T00:00:00.000Z |
| 2022-09-22T00:00:00.000Z |
| 2022-09-19T00:00:00.000Z |
| 2022-09-30T00:00:00.000Z |
| 2022-09-23T00:00:00.000Z |
| 2022-09-28T00:00:00.000Z |


### 6.4 EXTEND_TIME_WINDOW()

#### Introduction

EXTEND_TIME_WINDOW function allows for the time period of the input tables to be extended beyond the reporting time window.


#### Syntax

```
EXTEND_TIME_WINDOW( <table_name>, <Number_of_Days_Retro>, <Number_of_Days_Future> )
```

* \<table_name\> refers to the table that needs to be extended beyond the current timeframe of the query
* \<Number_of_Days_Retro\> refers to the Number of Days to extend the table retroactively to pull data from
* \<Number_of_Days_Future\> refers to the Number of Days to extend the table in the future, beyond the current timeframe of the query

#### Query Template




```
-- Instructional Query: How to use EXTEND_TIME_WINDOW function --

SELECT
    DISTINCT campaign,
    campaign_id_string,
    COUNT(DISTINCT(user_id)) AS Reach
FROM
    TABLE(
      EXTEND_TIME_WINDOW('dsp_impressions', 'P15D', 'P0D')
        )
WHERE
    user_id IS NOT NULL
GROUP BY 
    1,
    2
```

#### Example Query Results

| campaign                                                       | campaign_id_string | Reach  |
|----------------------------------------------------------------|-------------|--------|
| Kitchen Smart \| SD \| All Products \| Awareness               | 111111111111111   | 422572 |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | 2222222222222   | 261564 |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 333333333333    | 238734 |
| Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | 444444444444 | 176628 |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 555555555555    | 222931 |

#### Example IQs

[Instructional Query: Custom Attribution - Linear](/marketing-cloud/instructional-queries/95212a0253ea4d060cd94db10de7d3dfd542232fa786e1d74852ae3d8bd58139)



### 6.5 EXTRACT()

#### Introduction

The EXTRACT() function extracts a part from a given date.

#### Syntax

```
EXTRACT(<part> FROM <date>)
```

* \<part\> refers to the date part that needs to be extracted. Example: MONTH, SECOND, MINUTE, HOUR etc
* \<date\> refers to the date you want to extract from

#### Query Template

```
SELECT DISTINCT (EXTRACT(MONTH FROM impression_dt)) as impression_month
from dsp_impressions 
```


#### Example Query Results
| impression_month |
|-------|
| 12    |




## 7. Conditional functions

### 7.1 CASE()

#### Introduction

AMC supports the CASE() expression. The CASE expression goes through conditions and returns a value when the first condition is met. If it encounters a condition that’s true, it will stop reading the rest of the conditions and return the value specified in the \<result\> corresponding to the true condition. If no conditions are true, it returns the value in the ELSE clause.
CASE() can be used in the SELECT statement, as well as in the WHERE clause to filter your data based on a conditional logic.

#### Syntax
```
CASE
    WHEN <condition1> THEN <result1>
    WHEN <condition2> THEN <result1>
    WHEN <conditionN> THEN <resultN>
    ELSE result
END
```

* \<condition\> specifies the condition that should resolve to either true or false
* \<result\> specifies the value that will be returned, if the condition resolves to true




#### Query Template




```
-- Instructional Query: How to use CASE()in SELECT --  

SELECT
    distinct(advertiser),
    conversion_event_date_utc,
    CASE
      WHEN conversion_event_marketplace_name = 'AMAZON.COM' THEN 'Online'
      WHEN conversion_event_marketplace_name = 'PRIME_NOW_US' THEN 'Online'
      WHEN conversion_event_marketplace_name = 'AMAZON_FRESH_STORES_US' THEN 'Offline'
      WHEN conversion_event_marketplace_name = 'WHOLE_FOODS_MARKET_US' THEN 'Offline'
    END AS marketplace,
    SUM(purchases) AS purchases
FROM
    amazon_attributed_events_by_conversion_time
WHERE
    purchases > 0
GROUP BY
    1,
    2,
    3
```

#### Example Query Results

| advertiser       | conversion_event_date_utc | marketplace | purchases |
|------------------|---------------------------|-------------|-----------|
| Kitchen Smart    | 9/15/2022                 | Online      | 201612    |
| Kitchen Smart V2 | 9/23/2022                 | Online      | 1819123   |
| Kitchen Smart    | 9/26/2022                 | Offline     | 267833    |
| Kitchen Smart    | 9/6/2022                  | Online      | 1844111   |


```
-- Instructional Query: How to use CASE()in WHERE clause --      

SELECT 
    distinct(advertiser),
    device_type,
    conversion_event_date_utc,
    conversion_event_marketplace_name,
    SUM(purchases) AS purchases
FROM
    amazon_attributed_events_by_conversion_time
WHERE
    purchases > 0
AND
   CASE
      WHEN conversion_event_marketplace_name = 'AMAZON.COM' THEN device_type = 'PC'
      WHEN conversion_event_marketplace_name = 'PRIME_NOW_US' THEN device_type = 'Mobile'
      WHEN conversion_event_marketplace_name = 'AMAZON_FRESH_STORES_US' THEN device_type = Null
      WHEN conversion_event_marketplace_name = 'WHOLE_FOODS_MARKET_US' THEN device_type = Null
    END  
GROUP BY
    1,
    2,
    3,
    4
```

#### Example Query Results

| advertiser       | device_type | conversion_event_date_utc | conversion_event_marketplace_name | purchases |
|------------------|-------------|---------------------------|-----------------------------------|-----------|
| Kitchen Smart    | PC          | 9/27/2022                 | AMAZON.COM                        | 624       |
| Accent Athletics | Mobile      | 9/3/2022                  | PRIME_NOW_US                      | 56        |
| Kitchen Smart    | Null        | 9/15/2022                 | AMAZON_FRESH_STORES_US            | 43        |
| Accent Athletics | Null        | 9/28/2022                 | WHOLE_FOODS_MARKET_US             | 41        |

#### Example IQs

[Instructional Query: How to Measure In-Store Conversions](/marketing-cloud/instructional-queries/17bfafa195ee45619bb19cfbe066411f8b8bfa10e91ce2d96bdbea92e85dbb81)





### 7.2 IF()

#### Introduction

AMC supports the IF() function which returns a value based on the condition specified in the argument.

#### Syntax

```
IF( <condition>, <value_if_true> , <value_if_false>)
```

* \<condition\> specifies the condition that should resolve to either true or false
* \<value_if_true\> is the value that will be returned if the condition is true
* \<value_if_false\> is the value that will be returned if the condition is false

#### Query Template


```
-- Instructional Query: How to use IF() --

WITH camp_imp AS (

    SELECT 
        campaign, 
        COUNT(impressions) AS Impression_count
    FROM 
        amazon_attributed_events_by_conversion_time 
    GROUP BY 
        1
 )

SELECT 
    campaign, 
    IF(Impression_count<1000, 'less than 1000 impressions', 'greater than 1000 impressions') AS Impression_scale
FROM 
    camp_imp
```

#### Example Query Results

| campaign                                                       | impression_scale              |
|----------------------------------------------------------------|-------------------------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | less than 1000 impressions    |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | greater than 1000 impressions |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | less than 1000 impressions    |
| Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | less than 1000 impressions    |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | greater than 1000 impressions |

#### Example IQs

[Instructional Query: DSP Impression Frequency and Conversions](/marketing-cloud/instructional-queries/2b13b611c86b51a98ea3027822557df0b8a1a994272e2173a3b426214cf8f714)



### 7.3 IN()

#### Introduction

AMC supports the IN() function to specify multiple values in a WHERE clause.

#### Syntax
```
IN( <value1>, <value2>,..... <valueN> )
```

* \<value1\>, \<value2\>, \<valueN\> are the list of values that the column will be specified in the WHERE clause will be compared to. If any of the values match the field, then the IN condition will be true.

```
NOT IN( <value1>, <value2>,..... <valueN> )
```

* the IN() function can also be used with the NOT conditional operator to exclude values

#### Query Template




```
-- Instructional Query: How to use IN() --

SELECT 
    campaign,
    ad_product_type,
    SUM(purchases) AS purchases
FROM
    amazon_attributed_events_by_traffic_time
WHERE
    ad_product_type IN ('sponsored_products', 'sponsored_display')
GROUP BY
    1,
    2
```

#### Example Query Results

| campaign                                                       | ad_product_type    | purchases |
|----------------------------------------------------------------|--------------------|-----------|
| Kitchen Smart \| SD \| All Products \| Awareness               | sponsored_display  | 201612    |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | sponsored_display  | 1819123   |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | sponsored_products | 267833    |
| Kitchen Smart \| SP \| Blenders \| Remarekting \| ASIN12345    | sponsored_products | 1844111   |
| Kitchen Smart \| SP \| Air Fryers \| Remarekting \| ASIN789    | sponsored_products | 234511    |

#### Example IQs

[Instructional Query: Sponsored Ads and DSP Overlap](/marketing-cloud/instructional-queries/dfc41258bf5adb1ac1b1d7aa5b734b8af4a2e9b3487468ef84ebafbff096ad22)



### 7.4 COALESCE()

#### Introduction

AMC supports the  COALESCE() function which returns the first non-null value in a list. If all values evaluate to NULL, then the COALESCE function returns NULL.

#### Syntax
```
COALESCE( <value1>, <value2>, ...., <valueN>)
```
* \<value1\> , \<value2\> refers to the list of values that are evaluated for NULL

#### Query Template

The Ad Product type for “DSP” is NULL in the amazon_attributed_events_by_conversion_time, so you can use the COALESCE function to assign "DSP" as Ad Product type for NULL values.



```
-- Instructional Query: How to COALESCE()--

SELECT
    distinct(COALESCE (ad_product_type, 'DSP')) AS Ad_product_type
FROM
    amazon_attributed_events_by_conversion_time
```

#### Example Query Results

| ad_product_type    |
|--------------------|
| DSP                |
| sponsored_products |
| sponsored_display  |


#### Example IQs

[Instructional Query: Path to Conversion Campaign](/marketing-cloud/instructional-queries/47b4188b0848e7bf5f0b4417cc5c284fafe7517099431e8a0a26c6fa5911cc88)



## 8. Array functions

### 8.1 ARRAY_CONTAINS

#### Introduction

The ARRAY_CONTAINS function returns true if the value is contained in the array. It returns false, if the value is not contained in the array.

#### Syntax

```
ARRAY_CONTAINS( <array1> , <value_to_check> )
```

* \<array1\> The source array.
* \<value_to_check\> specifies the value that the Array \<array1\> should be checked for

#### Query Template


```
-- Instructional Query: How to use ARRAY_CONTAINS--

WITH imps AS ( 
    SELECT 
        campaign,
        regexp_split(campaign, '_') as split_campaign, 
        SUM(impressions) as impressions 
    FROM 
        sponsored_ads_traffic 
    WHERE 
        impressions > 0 group by 1, 2 )

SELECT 
    campaign, 
    CASE 
        WHEN array_contains(split_campaign,'Fire') THEN 'Fire Product' 
        ELSE 'Not Fire Product' 
        END AS campaignContains, 
    SUM(impressions) as impressions 
FROM 
    imps 
GROUP BY 
    1,
    2 
```

#### Example Query Results

| campaign                                                            | campaignContains | impressions |
|---------------------------------------------------------------------|------------------|-------------|
| Kitchen Smart \| SP \| Toasters \| Amazon_Fire_14                   | Fire Product     | 18421       |
| Kitchen Smart \| Blenders \| Remarketing \| Amazon_Echo_20221101    | Not Fire Product | 31957       |
| Kitchen Smart \| SP \| Blenders \| Category Views \| Amazon_Fire_14 | Fire Product     | 23444       |


### 8.2 COLLECT()

#### Introduction

COLLECT() is an aggregate function, used to group column data from multiple rows, into a single cell, in the form of an array, as per the grouping specified in the query.

#### Syntax
```
SELECT
    <column_name_1>,
    <column_name_2>,
    ...,
    <column_name_n>,
    COLLECT(<column1>) AS <column_name_collected_arary>
FROM
    <table_name>
GROUP BY
    1,2,...,n
```

#### Query Template




```sql
WITH initial_aggregation AS (
  SELECT 
    Campaign, 
    tracked_asin as tracked_asin, 
    SUM(total_purchases) as total_purchases, 
    sum(total_product_sales) as total_product_sales
  FROM 
    amazon_attributed_events_by_conversion_time 
  GROUP BY
    1,
    2
)
SELECT 
  IA.Campaign, 
  COLLECT(distinct(IA.tracked_asin)) as tracked_asin, 
  SUM(IA.total_purchases) as total_purchases, 
  sum(IA.total_product_sales) as total_product_sales
FROM 
  initial_aggregation IA
GROUP BY
  1
```

#### Example Query Results

|                            campaign                            |                                                       tracked_asin                                                       | total_purchases | total_sales |
|:--------------------------------------------------------------:|:------------------------------------------------------------------------------------------------------------------------:|-----------------|-------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | [ASIN000001, ASIN000002, ASIN000003, ASIN000004, ASIN000005, ASIN000006, ASIN000007, ASIN000008, ASIN000009, ASIN000010] | 201612          | 13223640.88 |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | [ASIN000011, ASIN000002, ASIN000003, ASIN000004, ASIN000005, ASIN000016, ASIN000007]                                     | 1819123         | 6113575.44  |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | [ASIN000021, ASIN000022, ASIN000023, ASIN000024]                                                                         | 267833          | 2191122.51  |
| Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | [ASIN000001, ASIN000002, ASIN000003, ASIN000004, ASIN000005, ASIN000006, ASIN000007, ASIN000008, ASIN000009, ASIN000010] | 1844111         | 3342562.04  |


#### Advanced Usage

You may use it with the optional DISTINCT() clause, to collect distinct/unique values

```
SELECT
    COLLECT(DISTINCT <column1> AS column1
  FROM
    <table>
```

It is usually used with GROUP BY. COLLECT gathers up the specified field values from within each subgroup formed by GROUP BY

```
SELECT
    COLLECT(DISTINCT <column1>) AS column1,
    <column2>
  FROM
    <table>
  GROUP BY
    2
```

You can layer on ARRAY_SORT() function to sort the collected values in sequence

```
SELECT
    ARRAY_SORT(COLLECT(DISTINCT <column1>)) AS column1,
    <column2>
  FROM
    <table>
  GROUP BY
    2
```

#### Example IQs

[Instructional Query: DSP Path to Conversion by Device](/marketing-cloud/instructional-queries/010ebf2f898de2059585d432e60b0e86f4891b9f7371f0bd297347024ccf2e20)





### 8.3 ARRAY_TO_STRING()

#### Introduction

AMC supports the function ARRAY_TO_STRING, which converts an input array into a string, by concatenating all the elements of the array separated by the separator pattern.

#### Syntax
```
ARRAY_TO_STRING(<array>,<separator_pattern>)
```
* \<array\> The input array that will be concatenated into a single string
* \<separator_pattern\> The pattern that is placed between each element of array in the concatenated string

#### Query Template



```
-- Instructional Query: How to use REGEXP_SPLIT --

WITH test_campaigns (campaign) AS (
  VALUES
    ('Testing_Campaign_1'),
    ('Testing_Campaign_2')
),
test_split AS (
  SELECT
    campaign,
    regexp_split(campaign, '_') AS split_campaign
  FROM
    test_campaigns
)
SELECT
  campaign,
  split_campaign,
  ARRAY_TO_STRING(split_campaign, '&') AS string_campaign
FROM
  test_split
```

#### Example Query Results

|campaign|split_campaign|string_campaign|
|---|---|---|
|Testing_Campaign_1|[Testing, Campaign, 1]|Testing&Campaign&1|
|Testing_Campaign_2|[Testing, Campaign, 2]|Testing&Campaign&2|



## 9. Analytical functions


### 9.1 PARTITION BY

#### Introduction

AMC supports the PARTITION BY clause which divides the result set into partitions based on the specified column. PARTITION BY is generally used with window functions like SUM(), MAX(), RANK() etc. which perform the computation on the windows specified by the Partition BY clause. 

#### Syntax
```
SELECT
   <column1>, <window function>
   OVER(
    PARTITION BY <column2> 
    [ORDER BY <column3>]
    )
FROM table
```

* \<window_function\> specifies the operation that should be performed on the sets of rows within a partition
* \<column2\> specifies the field that should be used to partition the rows
* ORDER BY is the optional clause that can be used within the PARTITION BY clause to sort the rows in a specific order 
* \<column3\> specifies the field that should be used for sorting the rows



#### Query Template




```
-- Instructional Query: How to use RANK() with PARTITION --

WITH campaigns AS (
    SELECT
    campaign,
    line_item,
    SUM(impressions) as impressions
    FROM dsp_impressions
    GROUP BY 1,2
)

SELECT 
    campaign,
    line_item,
    impressions,
    RANK() OVER(
        PARTITION BY campaign
        ORDER BY impressions DESC) Impression_Rank
FROM 
    campaigns
```

#### Example Query Results

| campaign                                                    | line_item                                              | impressions | impression_rank |
|-------------------------------------------------------------|--------------------------------------------------------|-------------|-----------------|
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters \| Mobile              | 7778223     | 1               |
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters\| Model XYZ \| Desktop | 4555112     | 2               |
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters\| Model 123 \| Desktop | 321108      | 3               |
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters \| Tablet              | 112222      | 4               |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | Kitchen Smart \| SP \| Air Fryers \| Desktop           | 3455123     | 1               |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | Kitchen Smart \| SP \| Air Fryers \| PC                | 2722789     | 2               |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | Kitchen Smart \| SP \| Air Fryers \| GEO CA            | 2222888     | 3               |


### 9.2 ROW_NUMBER()

#### Introduction

AMC supports the ROW_NUMBER() function which returns the sequential number of a row within the result set, starting at 1 for the first row, based on the ordering of rows specified in the ORDER BY clause.

#### Syntax
```
ROW_NUMBER()
OVER(
    [PARTITION BY <column1>, ...[n]] 
    ORDER BY <column2>
    )
```

* OVER specifies the window i.e. set of rows that the function operates within
* PARTITION BY is the optional clause that divides the result set into partitions i.e. groups of rows. If this clause is not provided, then the ROW NUMBER function will consider the entire result
* ORDER BY is a mandatory clause that orders the rows in the result set, so that the ROW_NUMBER() function can assign the row number
* \<column1\> refers to the column that the result set should be partitioned by
* \<column2\> refers to the column that the partitioned result set should be ordered by

#### Query Template




```
-- Instructional Query: How to use ROW_NUMBER()--
-- Use Case - Number of users that were exposed to their 1st impression on their Phone --

WITH ranked AS (
  SELECT
      ROW_NUMBER() OVER(
        PARTITION BY user_id
        ORDER BY
          impression_dt_hour
      )   AS device_order, device_type, user_id
  FROM
    dsp_impressions 
  WHERE
    user_id IS NOT NULL
)

SELECT 
    device_order, 
    device_type, 
    count(user_id) as Number_of_users
FROM 
    ranked
WHERE 
    device_order = 1 and device_type = 'Phone'
GROUP BY 
    1,
    2

```

#### Example Query Results



|device_order|device_type|number_of_users|
|------------|-----------|---------------|
|1|Phone|19112360|

#### Example IQs

[Instructional Query: DSP Path to Conversion by Device](/marketing-cloud/instructional-queries/010ebf2f898de2059585d432e60b0e86f4891b9f7371f0bd297347024ccf2e20)



### 9.3 RANK()

#### Introduction

AMC supports the RANK() function which returns the sequential Rank of a row within the result set, starting at 1 for the first row. If 2 rows have the same values, then their Rank is also the same. The next row will be assigned a non-consecutive value.

#### Syntax
```
RANK() 
OVER(
    [PARTITION BY <column1>, ...[n]] 
    ORDER BY <column2>
    )
```
* OVER specifies the window i.e. set of rows that the function operates within
* PARTITION BY is the optional clause that divides the result set into partitions i.e. groups of rows. If this clause is not provided, then the RANK function will consider the entire result
* \<column1\> refers to the column that the result set should be partitioned by
* \<column 2\> refers to the column that the partitioned result set should be ordered by

#### Query Template




```
-- Instructional Query: How to use RANK()--

WITH camp_imp AS
    (
        SELECT campaign, COUNT(impressions) AS Impression_count
        FROM dsp_impressions 
        GROUP BY 1
    )

SELECT 
    RANK() OVER (ORDER BY Impression_count DESC) as Ranked, 
    campaign, 
    impression_count
FROM 
    camp_imp

```

#### Example Query Results

| ranked | campaign                                                       | impression_count |
|--------|----------------------------------------------------------------|------------------|
| 1      | Kitchen Smart \| SD \| All Products \| Awareness               | 201612           |
| 2      | Kitchen Smart \| SD \| Toasters \| Awareness                   | 1819123          |
| 3      | Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 267833           |
| 4      | Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | 1844111          |
| 5      | Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 234511           |



```
-- Instructional Query: How to use RANK() with PARTITION --

WITH campaigns as(
    SELECT
        campaign,
        line_item,
        SUM(impressions) as impressions
    FROM 
        dsp_impressions
    GROUP BY 
        1,2
)

SELECT 
    campaign,
    line_item,
    impressions,
    RANK() OVER(
        PARTITION BY campaign
        ORDER BY impressions DESC) Impression_Rank
FROM 
    campaigns

```

#### Example Query Results

| campaign                                                    | line_item                                              | impressions | impression_rank |
|-------------------------------------------------------------|--------------------------------------------------------|-------------|-----------------|
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters \| Mobile              | 7778223     | 1               |
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters\| Model XYZ \| Desktop | 4555112     | 2               |
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters\| Model 123 \| Desktop | 321108      | 3               |
| Kitchen Smart \| SD \| Toasters \| Awareness                | Kitchen Smart \| SD \| Toasters \| Tablet              | 112222      | 4               |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | Kitchen Smart \| SP \| Air Fryers \| Desktop           | 3455123     | 1               |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | Kitchen Smart \| SP \| Air Fryers \| PC                | 2722789     | 2               |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789 | Kitchen Smart \| SP \| Air Fryers \| GEO CA            | 2222888     | 3               |


### 9.4 DENSE_RANK()


#### Introduction

AMC supports the DENSE_RANK() function which returns the sequential rank of a row within the result set, starting at 1 for the first row. If 2 rows have the same values, then their Dense_Rank is also the same. The next row will be assigned a consecutive value. 
DENSE_RANK() differs from RANK, as RANK() would instead assign a non-consecutive value to rows following ties. 

#### Syntax
```
DENSE_RANK() 
OVER(
    [PARTITION BY <column1>, ...[n]] 
    ORDER BY <column2>
    )
```

* OVER specifies the window i.e. set of rows that the function operates within
* PARTITION BY is the optional clause that divides the result set into partitions i.e. groups of rows. If this clause is not provided, then the DENSE_RANK function will consider the entire result
* \<column1\> refers to the column that the result set should be partitioned by
* \<column2\> refers to the column that the partitioned result set should be ordered by


#### Query Template



```
-- Instructional Query: How to use DENSE_RANK() --

WITH camp_imp AS
    (
        SELECT 
            campaign, 
            COUNT(impressions) AS Impression_count
        FROM 
            amazon_attributed_events_by_conversion_time 
        GROUP BY 
            1
    )

SELECT 
    DENSE_RANK() OVER (ORDER BY Impression_count DESC) as ranked, 
    campaign, 
    impression_count
FROM 
    camp_imp

```

#### Example Query Results

| ranked | campaign                                                       | impression_count |
|--------|----------------------------------------------------------------|------------------|
| 1      | Kitchen Smart \| SD \| All Products \| Awareness               | 201612           |
| 2      | Kitchen Smart \| SD \| Toasters \| Awareness                   | 1819123          |
| 3      | Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 267833           |
| 4      | Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | 1844111          |
| 5      | Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 234511           |


## 10. Advanced functions

### 10.1 CAST()

#### Introduction

AMC supports the  CAST() function to convert a value or an expression from one type to another.

#### Syntax
```
CAST ( <column> AS <target_datatype> )  
```


* \<column\> refers to the field that needs to converted to a different data type
* \<target_datatype\> is the target data type to which you want to convert the column. 

#### Query Template




```
-- Instructional Query: How to CAST() --

SELECT
    DISTINCT CAST(campaign_start_date AS DATE) as Start_date
FROM
    amazon_attributed_events_by_conversion_time
WHERE
    CAST(campaign_start_date AS DATE) > CAST('2022-09-01' AS DATE)
```

#### Example Query Results


| start_date |
|------------|
| 9/28/2022  |
| 10/1/2022  |

#### Example IQs

[Instructional Query: How to Filter by Custom Date](/marketing-cloud/instructional-queries/645c8ce23284679f29bb562644c88cfadd5fe9fbde58b049d5f9a18b6151fcff)




### 10.2 NAMED_ROW()

#### Introduction

AMC supports the NAMED_ROW() function which helps group multiple columns under a single name.

#### Syntax
```
SELECT NAMED_ROW('label1', <column1>, 'label2', <column2>) AS <column3>
```
* \<label1\> and \<label2\> refer to labels to reference column1 and column2

#### Query Template




```
-- Instructional Query: How to use NAMED_ROW() --

SELECT
    NAMED_ROW('device', device_type, 'dma_code', dma_code) AS device_dma,
    COUNT(distinct(a.user_id)) distinct_user_count
FROM
    dsp_impressions a
WHERE
    user_id IS NOT NULL
    GROUP BY 1
```

#### Example Query Results

| device_dma                   | distinct_user_count |
|------------------------------|---------------------|
| [ConnectedDevice, US-DMA444] | 2011129              |
| [Tablet, US-DMA333]          | 1899923             |
| [TV, US-DMA222]              | 2678883              |
| [Phone, US-DMA111]           | 1841111             |
| [Phone, US-DMA555]           | 23451112              |


#### Example IQs

[Instructional Query: DSP Path to Conversion by Device](/marketing-cloud/instructional-queries/010ebf2f898de2059585d432e60b0e86f4891b9f7371f0bd297347024ccf2e20)



## 11. Standard deviation and variance

#### Introduction

AMC provides support for several statistical functions that calculate the standard deviation and variance of either a population or of a sample set of data.

The functions STDDEV_POP() and VAR_POP() calculate the standard deviation and variance of the population, respectively. Use STDEV_POP() and VAR_POP() when the values represent the entire population, and are not a sample. 

The functions STDDEV_SAMP() and STDDEV() are synonyms for the same function. These functions can be used interchangeably because they calculate the standard deviation of a sample set of data, and provide the same results. 

Similarly, the functions VAR_SAMP() and VARIANCE() are synonyms for the same function, and calculate the variance of a sample set of data. Use these functions when the values are a sample, and not for the entire population. 


| Functions                | Calculates...      | Population or Sample? |
|--------------------------|--------------------|-----------------------|
| STDDEV_POP()             | Standard deviation | Population            |
| VAR_POP()                | Variance           | Population            |
| STDDEV_SAMP() / STDDEV() | Standard deviation | Sample                |
| VAR_SAMP() / VARIANCE()  | Variance           | Sample                |




### 11.1 STDDEV_POP(), VAR_POP()

#### Syntax

```
STDDEV_POP(<values>)
VAR_POP(<values>)
```

* \<values\>  A set of numeric values (integer, decimal, or floating-point)


#### Query template

The query below calculates the standard deviation and variation of the clicks from the population of all clicks from cost-per-click (CPC) sponsored ads campaigns. 


```
-- Instructional Query: How to use STDDEV_POP() and  VAR_POP() --

-- The population of clicks from CPC sponsored ads campaigns
WITH click_spend AS (
  SELECT
    -- spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.
    ad_product_type,
    spend / 100000000 AS spend
  FROM
    sponsored_ads_traffic
  WHERE
    clicks > 0
    and line_item_price_type = 'CPC'
)
SELECT
ad_product_type,
  STDDEV_POP(spend) AS STDDEV_POP_click_cost,
  VAR_POP(spend) as VAR_POP_click_cost
FROM
  click_spend
  group by 1
```


#### Example Query Results

| ad_product_type    | stddev_pop_click_cost | var_pop_click_cost |
|--------------------|-----------------------|--------------------|
| sponsored_brands   | 1.55863               | 2.42933            |
| sponsored_products | 1.15449               | 1.33286            |



### 11.2 STDDEV(), VARIANCE()

#### Syntax

```
STDDEV(<values>)
VARIANCE(<values>)
```

* \<values\>  A set of numeric values (integer, decimal, or floating-point)


#### Query template

The query below calculates the standard deviation and variance from a random sample of values for illustrative purpose. 




```
-- Instructional Query: How to use STDDEV(), VARIANCE() --

-- Test data below is a sample of the population for illustrative purposes. 
WITH test_data(test) AS (
  VALUES
  
    (20), 
    (45), 
    (30), 
    (66), 
    (50), 
    (60), 
    (85), 
    (70)

)
SELECT
  STDDEV(test) as STDDEV_sample_stardard_deviation,
  VARIANCE(test) as VARIANCE_sample_variance

    
FROM
  test_data
```


#### Example Query Results



| stddev_sample_stardard_deviation | variance_sample_variance |
|----------------------------------|--------------------------|
| 21.45261  | 460.21429                                       |



### 11.3 STDDEV_SAMP(), VAR_SAMP()

#### Syntax

```
STDDEV_SAMP(<values>)
VAR_SAMP(<values>)
```

* \<values\>  A set of numeric values (integer, decimal, or floating-point)

#### Query template

The query below calculates the standard deviation and variance from a random sample of clicks from cost-per-click (CPC) sponsored ads campaigns. Taking a random sample has the potential to result in better query performance, with statistically similar (but not exact) results from the population.  



```
-- Instructional Query: How to use STDDEV_SAMP() and VAR_SAMP --

--10% random sample of clicks

WITH click_spend AS (
 SELECT
    -- spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.
    ad_product_type,
    spend / 100000000 AS spend
  FROM
    sponsored_ads_traffic
  WHERE
    clicks > 0
    and line_item_price_type = 'CPC'
    and random() <= 0.1
)

SELECT
ad_product_type,
  STDDEV_SAMP(spend) AS STDDEV_SAMP_click_cost,
  VAR_SAMP(spend) AS VAR_SAMP_click_cost
FROM
  click_spend
  group by 1
```

#### Example Query Results

Note that the sample standard deviation and variance results are slightly different from the population in the previous section. 

| ad_product_type    | stddev_samp_click_cost | var_samp_click_cost |
|--------------------|------------------------|---------------------|
| sponsored_brands   | 1.55965                | 2.43012             |
| sponsored_products | 1.15518                | 1.33995             |


## 12. Skewness


#### Introduction

The SKEWNESS function returns the estimate of the skewness of a set of numeric values (integer, decimal, or floating point). Skewness is a measure of the asymmetry of the distribution. The function will return one value per dimension. 

* **Skewed to the right**: A positive skewness value above 0.5 indicates that the skew is positive, also known as ‘skewed to the right’ or ‘right tailed’; this means that most of the distribution is concentrated on the left, and the right tail is longer. 

* **Skewed to the left**: A negative skewness value below -0.5 indicates that the skew is negative, also known as ‘skewed to the left’ or ‘left tailed’; this means that most of the distribution is concentrated on the right, and the left tail is longer. 

* **Symmetrical**: A skewness value around 0 (between -0.5 - 0.5) indicates that the distribution is nearly symmetrical on the left and the right. For example, a normal distribution has a skewness of zero. 

#### Syntax

```
SKEWNESS(<values>)
```

* \<values\>  A set of numeric values (integer, decimal, or floating-point)


#### Query template


```
-- Instructional Query: How to use SKEWNESS --

WITH click_spend AS (
    SELECT
    -- spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.
    ad_product_type,
    spend / 100000000 AS spend
  FROM
    sponsored_ads_traffic
  WHERE
    clicks > 0
    and line_item_price_type = 'CPC'
)
SELECT
        ad_product_type,
  SKEWNESS(spend) as SKEWNESS_click_cost
FROM
  click_spend
  group by 1

```


#### Example Query Results

The example results below for illustrative purposes show that both Sponsored Brands and Sponsored Products clicks are skewed to the right, or positively skewed. Sponsored Brands has a longer right tail, since it’s skewness value is higher about 35% larger than Sponsored Products. 

| ad_product_type    | skewness_click_cost |
|--------------------|---------------------|
| sponsored_brands   | 3.23764             |
| sponsored_products | 2.39026             |


## 13. Percentile and median

### 13.1 PERCENTILE(), APPROX_PERCENTILE()

#### Introduction

The function PERCENTILE() calculates the value at a given percentile of a distribution of values. APPROX_PERCENTILE calculates the approximate percentile of a distribution of values.


#### Syntax

```
PERCENTILE(<values>, <percentile>)
APPROX_PERCENTILE(<values>, <percentile>)
```

* \<values\>  A set of numeric values (integer, decimal, or floating-point)
* \<percentile\> The percentile of the distribution to be found. Value must be between 0 and 1.


### 13.2 MEDIAN()

#### Introduction

MEDIAN() function calculates the middle value of a set of numbers.  

#### Syntax

```
MEDIAN(<values>)
```

* \<values\>  A set of numeric values (integer, decimal, or floating-point)

#### Query template


```
-- Instructional Query: How to use PERCENTILE(), APPROX_PERCENTILE() and MEDIAN()--

WITH click_spend AS (
    SELECT
    -- spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.
    ad_product_type,
    spend / 100000000 AS spend
  FROM
    sponsored_ads_traffic
  WHERE
    clicks > 0
    and line_item_price_type = 'CPC'
)
SELECT
  ad_product_type,
  PERCENTILE(spend, .1) as PERCENTILE10_click_cost,
  PERCENTILE(spend, .9) as PERCENTILE90_click_cost,
  APPROX_PERCENTILE(spend, .1) as APPROX_PERCENTILE10_click_cost,
  APPROX_PERCENTILE(spend, .9) as APPROX_PERCENTILE90_click_cost,
  MEDIAN(spend) as MEDIAN_click_cost

FROM
  click_spend
  group by 1
```

#### Example Query Results

| ad_product_type    | percentile10_click_cost | percentile90_click_cost | approx_percentile10_click_cost | approx_percentile90_click_cost | median_click_cost |
|--------------------|-------------------------|-------------------------|--------------------------------|--------------------------------|-------------------|
| sponsored_products | 0.18                    | 2.57                    | 0.18                           | 2.57                           | 0.81              |
| sponsored_brands   | 0.37                    | 3.258                   | 0.37                           | 3.27                           | 1.09              |


## 14. Unsupported functions


### 14.1 LIMIT

#### Introduction

AMC does not support LIMIT. However, you can use the RANK() function as a workaround to address your use case. 

#### Syntax
```
SELECT RANK() 
OVER(
    ORDER BY <column2>
    )
```

#### Query Template




```
-- Instructional Query: How to use RANK instead of LIMIT to select the top 10 line items --

WITH line_items AS 
(
    SELECT 
        line_item, 
        sum(impressions) as impressions 
    FROM 
        dsp_impressions 
    GROUP BY 
        1
 ), 
    
ranked AS
(
    SELECT 
        line_item, 
        impressions,
        RANK() OVER(ORDER BY impressions DESC) Impression_Rank
    FROM 
        line_items
)

SELECT 
    line_item, 
    impressions 
FROM 
    ranked 
WHERE 
    Impression_Rank <= 10
```

#### Example Query Results

| line_item                                                      | impressions |
|----------------------------------------------------------------|-------------|
| Kitchen Smart \| SD \| All Products \| Awareness               | 201612      |
| Kitchen Smart \| SD \| Toasters \| Awareness                   | 1819123     |
| Kitchen Smart \| SP \| Blenders \| Category Views \| ASIN56789 | 267833      |
| Kitchen Smart \| SP \| Blenders \| Remarketing \| ASIN12345    | 1844111     |
| Kitchen Smart \| SP \| Air Fryers \| Remarketing \| ASIN789    | 234511      |


### 14.2 RIGHT JOIN

AMC does not support RIGHT JOIN. However, you may use 2.4 LEFT JOIN and reverse the side of the table.

### 14.3 GETDATE()

AMC does not support GETDATE() function, which returns the current database system date and time. However, you may use the following as a workaround.

#### Query Template


```
-- Instructional Query: How to use CAST as a workaround for GETDATE() --

SELECT
CAST('today' AS DATE) AS Date_for_today, --for date
CAST('now' as timestamp) AS DateTime_for_now --for datetime
```

#### Example Query Results

|date_for_today|datetime_for_now|
|---|---|
|1/25/23|2023-01-25T01:41:52.281Z|



### 14.4 ORDER BY

AMC does not support ORDER BY clause in a query. However, you may use it within a PARTITION BY clause as explained in section 9.1


## 15. SQL concepts

### 15.1 Non-deterministic queries 

Non-deterministic SQL queries are queries that produce inconsistent results from multiple executions. This means that the output of the query will vary between the different executions of the same code with the same input values, underlying data and other conditions. This behavior is not specific to AMC SQL, but is a characteristic of SQL in general, when the code contains non-deterministic functions or if the code does not specify an explicit sorting order. To write deterministic queries, avoid using non-deterministic functions such as RANDOM(), and provide explicit sorting order. 

The output from a non-deterministic query is not incorrect, but rather it's inconsistent upon multiple runs. For example, if a column has the same value for multiple rows, and that column is used for sorting, then the sorting order can differ on each run of the query. This causes inconsistency in the output. Adding more columns in the ORDER BY clause can help provide consistency in the output, as the same order will be retrieved with each execution. 


SQL functions that may cause your queries to be non-deterministic:

* RANDOM()
* ROW_NUMBER()
* RANK()
* DENSE_RANK()

#### Query Template

##### RANDOM()

The RANDOM() function generates a random decimal number from 0 (inclusive) through 1 (exclusive) or within the specified range. The example query results indicate that the output may be different with each execution.


```
-- Instructional Query: Example of non-determnistic function --
select RANDOM() as hello
```

#### Example Query Results

|**hello**|
|---:|
|0.81028|

|**hello**|
|---:|
|0.49726|




##### ROW_NUMBER()

If two or more rows share the same row number as specified by PARTITION BY and ORDER BY statements, then a random row is selected upon execution. 

If you run the following query template multiple times, you may get results with a different order each time.


```
-- Instructional Query: Example of non-determnistic query --
WITH CTE (campaign_group, campaign_name) AS (
  VALUES
    ('1', 'Campaign_A'),
    ('1', 'Campaign_B'),
    ('1', 'Campaign_C'),
    ('2', 'Campaign_D'),
    ('2', 'Campaign_E'),
    ('2', 'Campaign_F')
)
SELECT
  ROW_NUMBER() OVER (
    ORDER BY
      campaign_group
  ) AS row_id,
  campaign_group,
  campaign_name
FROM
  CTE
```

#### Example Query Results

|**row_id**|**campaign_group**|**campaign_name**|
|---:|---:|:---|
|1|1|Campaign_B|
|2|1|Campaign_C|
|3|1|Campaign_A|
|4|2|Campaign_D|
|5|2|Campaign_E|
|6|2|Campaign_F|


|**row_id**|**campaign_group**|**campaign_name**|
|---:|---:|:---|
|1|1|Campaign_A|
|2|1|Campaign_B|
|3|1|Campaign_C|
|4|2|Campaign_F|
|5|2|Campaign_E|
|6|2|Campaign_D|

You can resolve the inconsistency in the output by adding more columns to the ORDER BY clause. In the example below, we added campaign_name in addition to the ID column. The output of this query will be consistent for each run.


```
-- Instructional Query: Example of converting a non-determnistic query to deterministic by layering more columns in the ORDER BY --

WITH CTE (campaign_group, campaign_name) AS (
VALUES
('1', 'Campaign_A'),
('1', 'Campaign_B'),
('1', 'Campaign_C'),
('2', 'Campaign_D'),
('2', 'Campaign_E'),
('2', 'Campaign_F')
)
SELECT
ROW_NUMBER() OVER (
ORDER BY
campaign_group,
campaign_name
) AS row_id,
campaign_group,
campaign_name
FROM
CTE
```

#### Example Query Results

|**row_id**|**campaign_group**|**campaign_name**|
|---:|---:|:---|
|1|1|Campaign_A|
|2|1|Campaign_B|
|3|1|Campaign_C|
|4|2|Campaign_D|
|5|2|Campaign_E|
|6|2|Campaign_F|

