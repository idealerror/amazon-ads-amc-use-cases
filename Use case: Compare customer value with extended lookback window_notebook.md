## Metadata

**id:** 6d1db59ab1011915a640b9c69228ab93be57b6ef6e0163bf4a203d2b90216159
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Measure the lift on customer value between new-to-brand and existing customers.
**tags:** ['Paid features']
**use_case_categories:** ['Custom attribution', 'Customer value', 'New-to-brand', 'Performance deep dive']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose 
What is long-term customer value when moving beyond 14-day ad-attributed metrics such as ROAS? This instructional query uses Flexible Shopping Insights (FSI) to include non ad-attributed conversions and expands beyond 14-day attribution metrics like short-term ROAS, incorporating indicators to capture the customer journey, such as repeat purchases. Through evaluating customer value by segment type with extended lookback window, you can glean insights to optimize remarketing strategies through AMC audience creation. Leveraging creating custom audience in AMC will help turn new-to-brand into repeat customers to drive high-value purchases for sustained growth and maximum returns on investment.

![](https://m.media-amazon.com/images/G/01/adsamciql/compare_customer_value_breakdown_sm.png)


![](https://m.media-amazon.com/images/G/01/adsamciql/compare_customer_value_ntb_existing_flow_sm.png)

### Requirements 

* **Subscribe to Flexible Amazon shopping insights.** Paid Features are only available in certain regions. If you're unable to see the Paid Features tab, reach out to your AdTech Account Executive.
* Have customer ASIN purchases on the Amazon e-commerce site (for example, amazon.com) to query the dataset.

## Data query instructions 

### Data returned

The query returns the segment group, count of unique users in each segment group, total unit sales, total product sales and average customer value. Exposure groups are users who were exposed to Amazon DSP or sponsored ads campaigns and made the purchase. 

### Tables used 

* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): This table provides the user_id and impression_dt to identify who was exposed and when segmented by Amazon DSP campaign_id.
* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This table provides the user_id and impression_dt to identify who was exposed and when segmented by sponsored ads.
* [amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): Due to the 14-day attribution window, wait two weeks after the end of the campaign to run this query to capture all conversions.
* [conversions_all](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/conversions_all_paid): Flexible Shopping Insights data is accessible through this table and it contains both ad-exposed and non-ad-exposed conversions for tracked ASINs. 

## Data interpretation instructions 

### Example query results

__This data is for instructional purposes only. Your results will differ.__

**Exploratory query results: Time between purchases**

This query defines time between purchases as the duration of time between consecutive purchases made by a customer regardless of ad exposure. A similar IQ for ad-attributed conversions with standard lookback is available: [IQ: Time to Conversion](/marketing-cloud/instructional-queries/e4b1c626c51e5fb3365f76c54d3e3ec6da9c739b86c4a8b6614b776e208d9368).

In the example result below, the median repeat purchase window is 60 - 90 days based off cumulative percentage. 


| time_between_purchases | unique_user_count | purchase_percent | cumulative_percent |
|------------------------|-------------------|------------------|--------------------|
| 1 | < 1 day            | 50422             | 3.61             | 3.61               |
| 2 | 1 - 3 days         | 32938             | 2.36             | 5.97               |
| 3 | 3 - 5 days         | 27419             | 1.96             | 7.93               |
| 4 | 5 - 10 days        | 49534             | 3.55             | 11.48              |
| 5 | 10 - 15 days       | 39203             | 2.81             | 14.29              |
| 6 | 15 - 20 days       | 37237             | 2.67             | 16.96              |
| 7 | 20 - 25 days       | 40023             | 2.87             | 19.83              |
| 8 | 25 - 30 days       | 43889             | 3.15             | 22.98              |
| 9 | 30 - 35 days       | 45011             | 3.23             | 26.21              |
| 10 | 35 - 40 days      | 45507             | 3.26             | 29.47              |
| 11 | 40 - 45 days      | 47977             | 3.44             | 32.91              |
| 12 | 45 - 50 days      | 47316             | 3.39             | 36.3               |
| 13 | 50 - 55 days      | 46999             | 3.37             | 39.67              |
| 14 | 55 - 60 days      | 48127             | 3.45             | 43.12              |
| 15 | 60 - 90 days      | 215843            | 15.47            | 58.59              |
| 16 | 90 - 120 days     | 176424            | 12.64            | 71.23              |
| 17 | 120 - 150 days    | 133302            | 9.55             | 80.78              |
| 18 | 150 - 180 days    | 97331             | 6.97             | 87.75              |
| 19 | 180+ days         | 170943            | 12.25            | 100                |



**Example query template results**


| user_group                 | total_users | total_product_sales | total_units_sold |  sales_per_user  |  units_per_user  |
|----------------------------|-------------|---------------------|------------------|------------------|------------------|
| 14days_attributed          | 2933        | \$72,801             | 4,300            | \$24.82           | 1.5              |
| 14days_attributed_existing | 1691        | \$46,734             | 2,750            | \$27.64           | 1.6              |
| 14days_attributed_ntb      | 1289        | \$26,067             | 1,550            | \$20.22           | 1.2              |
| extended_all               | 56797       | \$2,155,786          | 102,176          | \$37.96           | 1.8              |
| ntb                        | 24273       | \$542,121            | 26,950           | \$22.33           | 1.1              |
| ntb_oneoff                 | 19459       | \$414,170            | 20,426           | \$21.28           | 1                |
| ntb_repeat                 | 3553        | \$142,816            | 6,442            | \$40.20           | 1.8              |
| existing                   | 36494       | \$1,613,666          | 75,226           | \$44.22           | 2.1              |
| existing_oneoff            | 20295       | \$455,453            | 22,014           | \$22.44           | 1.1              |
| existing_repeat            | 11863       | \$693,493            | 30,634           | \$58.46           | 2.6              |



### Metrics defined

**Table: Metrics for Exploratory query: Time between purchases**



| metric                 | definition                                                                                                                                                                 |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `time_between_purchases` | The amount of time between purchases. The time has been converted to minutes, hours and days from seconds in the query template. You may adjust it based on your use case. |
| `unique_user_count`      | The total number of unique users that purchased within the time interval.                                                                                                  |
| `purchase_percent`       | The percentage of unique users who made repeat purchases within the interval relative to the total number of unique users who made repeat purchases.                       |



**Table: Metrics for Comparing customer value by segment type with extended lookback window**


| metric              | definition                                                                                                                                                                                                 |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `user_group`          | Users categorized by their attribution group, distinguishing between new and existing users, and tracking whether their purchases are one-off or repeat. Refer to the table below for all possible values. |
| `total_users`         | Total unique customers that made a purchase on Amazon.                                                                                                                                                     |
| `total_product_sales` | The total sales (in local currency) for promoted ASINs purchased by customers on Amazon.                                                                                                                   |
| `total_units_sold`    | The total quantity of promoted ASINs purchased by customers on Amazon.                                                                                                                                     |
| `sales_per_user`      | Average spending per user = (total_product_sales / unique_user_count)                                                                                                                                      |
|  `units_per_user`     | Average purchased units per user = (total_units_sold / unique_user_count)                                                                                                                                  |
| `ntb`                 | Unique new-to-brand users that purchased.                                                                                                                                                                  |
| `ntb_oneoff`          | Unique new-to-brand users that made one-time purchase.                                                                                                                                                     |
| `ntb_repeat`          | Unique new-to-brand users that made another purchase after initial purchase.                                                                                                                               |
| `existing`            | Unique existing users that purchased.                                                                                                                                                                      |
| `existing_oneoff`     | Unique existing  users that made one-time purchase.                                                                                                                                                        |
| `existing_repeat`     | Unique existing  users that made another purchase after initial purchase.                                                                                                                                  |



**Table: Definitions for ‘user_group’ values**


| user_group values        | definition                                                                          |
|----------------------------|-------------------------------------------------------------------------------------|
| 14days_attributed          | Unique total users that purchased within standard 14-day attribution window.        |
| 14days_attributed_existing | Unique existing users that purchased within standard 14-day attribution window.     |
| 14days_attributed_ntb      | Unique new-to-brand users that purchased within standard 14-day attribution window. |
| ntb                        | Unique new-to-brand users that purchased.                                           |
| ntb_oneoff                 | Unique new-to-brand users that made one-time purchase.                              |
| ntb_repeat                 | Unique new-to-brand users that made another purchase after initial purchase.        |
| existing                   | Unique existing users that purchased.                                               |
| existing_oneoff            | Unique existing  users that made one-time purchase.                                 |
| existing_repeat            | Unique existing  users that made another purchase after initial purchase.           |



### KPI calculations

What metrics can I calculate from the query results?
Based on the output from the query, you can calculate success KPIs such as new-to-brand rate and average existing customer value. 

For example, here is formula for the new-to-brand rate:

* New-to-brand Rate = (ntb total_users  / all users) * 100%

Using the example query results, the new-to-brand rate would be calculated as: 
 (24273 / 56797) * 100% = 42.7%



### Insights and data interpretation

We are comparing users who were exposed to the campaign in 14-day attribution with users exposed to the campaign then purchased in extended window to understand:

1. What is long-term customer value when moving beyond 14-day ad-attributed metrics such as ROAS?
2. Did new-to-brand customers make another purchase after the initial purchase in the extended window? 
3. What is the average customer value?


**What are average customer values observed when the attribution is beyond 14 days?**

When extending to 60-day attribution and including non ad-attributed conversions, customers spent $37.96 on average, 1.53x more than customers who purchased within 14-day attribution window. 

When extending to 60-day attribution, existing customers exposed to the campaign spent $44.22 on average, 1.98x more than new-to-brand customers on average.

**What is the value of new-to-brand repeat customers observed when using the 60-day attribution window?**

15% of new-to-brand customers exposed to the campaign made another purchase in next 60 days with the average customer value as $40.20, 1.62x more than customers who purchased within 14-day attribution window. 

**Action/Recommendations:**

This extended window allows for capturing more repeat purchases and higher customer value, thus maximizing the return on investment for marketing efforts. Advertisers can create custom AMC audience segments to remarket new-to-brand customers to drive repeat purchases and build loyalty.

## Queries

### Exploratory query

Use the following query below to calculate the median time between purchases. This information can help determine the extended window used in the query template.



```
-- Instructional Query: Exploratory Query to Identify Time between Purchases
-- AD PRODUCTS: Amazon DSP and/or sponsored ads
-- Subscription to or active trial of Amazon Flexible Shopping Insights (FSI) is required.
/*
 ------- Customization Instructions -------
 1) Set the "Date range" in the Query Editor to define the time range.
 a) Recommended time range is at least 6 months to measure the time between purchases.
 2) REQUIRED UPDATE: Update ASINs under "tracked_asin"
 3) OPTIONAL UPDATE: Uncomment tracked_asin if you are looking for time between repeat purchases for the same ASIN.
*/
  
WITH all_conversions AS (
SELECT 
    user_id
    -- , tracked_asin 
    , event_dt_utc
    , SUM(total_product_sales) AS product_sales
    , SUM(conversions) AS conversions

FROM conversions_all
    
WHERE 
    conversions > 0 
    AND user_id IS NOT NULL
    AND event_type_description IN ('Product purchased')     
    
    -- REQUIRED UPDATE: Update ASINs
    AND tracked_asin IN ('ASIN1111','ASIN2222','ASIN3333')

GROUP BY 
    user_id
    -- , tracked_asin
    , event_dt_utc
),

-- Count conversions by user and ASIN
conversions AS (
SELECT 
    user_id
    -- , tracked_asin
    , CASE WHEN SUM(conversions) > 1 THEN 1 ELSE 0 END AS repeat_purchasers  
    , SUM(conversions) AS conversions  
FROM 
    all_conversions 
  
GROUP BY 
    user_id
    -- , tracked_asin
),

-- All conversions from repeat purchasers
repeat_conversions AS (
SELECT
    user_id
    -- , tracked_asin
    , event_dt_utc
    , RANK() OVER (PARTITION BY user_id ORDER BY event_dt_utc) AS purchase_rank

FROM all_conversions

WHERE user_id IN (
    SELECT user_id FROM conversions WHERE repeat_purchasers = 1
    )
),

-- Time between purchases by user
customer_conversions AS (
SELECT
    a.user_id
   , SECONDS_BETWEEN(a.event_dt_utc, b.event_dt_utc) AS time_between_purchases

FROM repeat_conversions a
    JOIN repeat_conversions b
        ON a.user_id = b.user_id
            AND a.purchase_rank = b.purchase_rank - 1

GROUP BY 1,2),


final AS (
SELECT 
 CASE
    WHEN time_between_purchases <= 86400 THEN '1 | < 1 day' --
    WHEN time_between_purchases <= 259200 THEN '2 | 1 - 3 days' --
    WHEN time_between_purchases <= 432000 THEN '3 | 3 - 5 days' --
    WHEN time_between_purchases <= 864000 THEN '4 | 5 - 10 days' --
    WHEN time_between_purchases <= 1296000 THEN '5 | 10 - 15 days'
    WHEN time_between_purchases <= 1728000 THEN '6 | 15 - 20 days'
    WHEN time_between_purchases <= 2160000 THEN '7 | 20 - 25 days'
    WHEN time_between_purchases <= 2592000 THEN '8 | 25 - 30 days'
    WHEN time_between_purchases <= 3024000 THEN '9 | 30 - 35 days'
    WHEN time_between_purchases <= 3456000 THEN '10 | 35 - 40 days'
    WHEN time_between_purchases <= 3888000 THEN '11 | 40 - 45 days'
    WHEN time_between_purchases <= 4320000 THEN '12 | 45 - 50 days'
    WHEN time_between_purchases <= 4752000 THEN '13 | 50 - 55 days'
    WHEN time_between_purchases <= 5184000 THEN '14 | 55 - 60 days'
    WHEN time_between_purchases <= 7776000 THEN '15 | 60 - 90 days'
    WHEN time_between_purchases <= 10368000 THEN '16 | 90 - 120 days'
    WHEN time_between_purchases <= 12960000 THEN '17 | 120 - 150 days'
    WHEN time_between_purchases <= 15552000 THEN '18 | 150 - 180 days'
    ELSE '19 | 180+ days'
    END AS time_between_purchases
  
    , COUNT(distinct user_id) AS unique_user_count
    
FROM customer_conversions
WHERE time_between_purchases >= 0
GROUP BY time_between_purchases
)

-- final output data
SELECT
  time_between_purchases,
  SUM(unique_user_count) AS unique_user_count,
  (
    SUM(unique_user_count) / (
      SELECT
        SUM(unique_user_count)
      FROM
        final
    )
  ) * 100 AS purchase_percent
FROM
  final
GROUP BY time_between_purchases
```

### Query template: Comparing customer value by segment type with extended lookback window


```
-- Instructional Query: Comparing customer value by segment type with extended lookback window
-- AD PRODUCTS: Amazon DSP and/or sponsored ads
-- Subscription to or active trial of Amazon Flexible Shopping Insights (FSI) is required.
/*
 ------- Customization Instructions -------
 1) Recommended: Set the campaign start and end date as the "Date range" in the Query Editor base.
 2) Search for `REQUIRED UPDATE` to find the required changes and 'OPTIONAL UPDATE' to customize this query.
*/

-- REQUIRED UPDATE: LIST DSP CAMPAIGN ID STRINGs
WITH 
dsp (campaign_id_strings) AS (
  VALUES
(11111111111),
(22222222222),
(33333333333)
), 

    /*
-- OPTIONAL UPDATE: SPONSORED ADS  
sa (campaign_name) AS (
  VALUES    
('campaign_name1'),
('campaign_name2'),
('campaign_name3')
    ),
-- SPONSORED ADS END --   
   */
   
-- REQUIRED UPDATE: Update Promoted ASINs or Halo ASINs
tracked_ASIN (tracked_ASIN) AS (
  VALUES
('ASIN1111'),
('ASIN2222')
),

-- Customers exposed to the campaign
exposed AS (
SELECT
    user_id
    , MIN(impression_dt_utc) AS impression_dt_utc_first
    , SUM(impressions) AS impressions    
FROM dsp_impressions

WHERE
    user_id IS NOT NULL    
    AND campaign_id IN (SELECT campaign_id_strings FROM dsp)      
    AND impressions > 0
    
GROUP BY 1
    
-- OPTIONAL UPDATE: SPONSORED ADS     
    /*
UNION ALL 
    
SELECT
    user_id
    , MIN(event_dt_utc) AS impression_dt_utc_first    
    ,sum(impressions) AS imp

FROM sponsored_ads_traffic
 
WHERE impressions > 0

    AND campaign IN (SELECT campaign_name FROM sa)   
    
GROUP BY 1

   */
),

-- 14-day attributed purchases
attributed_14days_purchases AS (
SELECT
    user_id
    , new_to_brand
    , SUM(product_sales) AS product_sales    
    , SUM(units_sold) AS units_sold  
    
FROM amazon_attributed_events_by_conversion_time
    
WHERE 
    (
        campaign_id IN (SELECT campaign_id_strings FROM dsp) 
        -- OPTIONAL UPDATE: SPONSORED ADS  
       /*
        or campaign IN (SELECT campaign_name FROM sa)
        */
        )

    AND purchases > 0  
    AND tracked_ASIN IN (SELECT tracked_ASIN FROM tracked_ASIN)    
    AND user_id IS NOT NULL     
  
GROUP BY 1,2
),

extended_purchases_all AS (
SELECT
    a.user_id
    , a.event_date_utc
    
    , CASE WHEN a.new_to_brand = TRUE THEN 1 ELSE 0 END AS ntb_cust
    , CASE WHEN a.new_to_brand = FALSE THEN 1 ELSE 0 END AS existing_cust
    
    , CASE WHEN (a.new_to_brand = TRUE AND COUNT(a.conversion_id) OVER (PARTITION BY a.user_id) = 1) THEN 1 ELSE 0 END AS ntb_oneoff_cust    
    , CASE WHEN (a.new_to_brand = FALSE AND a.event_date_utc > MIN(CASE WHEN a.new_to_brand = TRUE THEN a.event_date_utc END) OVER (PARTITION BY a.user_id)) THEN 1 ELSE 0 END AS ntb_repeat_cust

    , CASE WHEN (a.new_to_brand = FALSE AND COUNT(a.conversion_id) OVER (PARTITION BY a.user_id) = 1 
                                          AND COUNT(CASE WHEN a.new_to_brand = TRUE THEN 1 END) OVER (PARTITION BY a.user_id) = 0) 
           THEN 1 ELSE 0 END AS existing_oneoff_cust
    , CASE WHEN (a.new_to_brand = FALSE AND a.event_date_utc > MIN(CASE WHEN a.new_to_brand = FALSE THEN a.event_date_utc END) OVER (PARTITION BY a.user_id)) THEN 1 ELSE 0 END AS existing_repeat_cust

    
    , SUM(a.total_units_sold) AS total_units_sold
    , SUM(a.total_product_sales) AS total_product_sales
    
    , SUM(CASE WHEN a.new_to_brand = True then a.total_units_sold else 0 end) AS ntb_total_units_sold
    , SUM(CASE WHEN a.new_to_brand = True then a.total_product_sales else 0 end) AS ntb_total_product_sales 
    
FROM
    TABLE(
      EXTEND_TIME_WINDOW('conversions_all', 'P0D', 'P60D') -- REQUIRED UPDATE: Extend the window to next 60 days from the end of campaign period
    ) a
    
    LEFT JOIN exposed b ON a.user_id = b.user_id
    
WHERE 
    a.user_id IS NOT NULL
    AND b.impression_dt_utc_first IS NOT NULL
    AND b.impression_dt_utc_first <= a.event_date_utc
    AND a.event_type_description IN ('Product purchased') 
    AND a.total_units_sold IS NOT null
    AND a.tracked_ASIN IN (SELECT tracked_ASIN FROM tracked_ASIN) 
    
GROUP BY 1,2,3,4,5,6,7,8
)

SELECT
    '14days_attributed' AS segment_group
    , COUNT(DISTINCT user_id) AS total_users 
    
    , sum(product_sales) AS total_product_sales
    , sum(units_sold) AS total_units_sold
    , sum(product_sales) / COUNT(DISTINCT user_id) AS sales_per_user
    , sum(units_sold) / COUNT(DISTINCT user_id) AS units_per_user  
 
FROM attributed_14days_purchases    
WHERE user_id IS NOT NULL
    AND units_sold > 0
    
UNION ALL 

SELECT
    '14days_attributed_ntb' AS segment_group
    , COUNT(DISTINCT user_id) AS total_users 
    
    , sum(product_sales) AS total_product_sales
    , sum(units_sold) AS total_units_sold
    , sum(product_sales) / COUNT(DISTINCT user_id) AS sales_per_user
    , sum(units_sold) / COUNT(DISTINCT user_id) AS units_per_user  
 
FROM attributed_14days_purchases    
WHERE user_id IS NOT NULL
    AND units_sold > 0
    AND new_to_brand = TRUE
    
UNION ALL 

SELECT
    '14days_attributed_existing' AS segment_group
    , COUNT(DISTINCT user_id) AS total_users 
    
    , sum(product_sales) AS total_product_sales
    , sum(units_sold) AS total_units_sold
    , sum(product_sales) / COUNT(DISTINCT user_id) AS sales_per_user
    , sum(units_sold) / COUNT(DISTINCT user_id) AS units_per_user  
 
FROM attributed_14days_purchases    
WHERE user_id IS NOT NULL
    AND units_sold > 0
    AND new_to_brand = FALSE
    
UNION ALL

SELECT
    'extended_all' AS segment_group

    , COUNT(DISTINCT user_id) AS total_users
    , sum(total_product_sales) AS total_product_sales
    , sum(total_units_sold) AS total_units_sold
    , sum(total_product_sales) / COUNT(DISTINCT user_id) AS sales_per_user
    , sum(total_units_sold) / COUNT(DISTINCT user_id) AS units_per_user    
   
FROM extended_purchases_all    
WHERE user_id IS NOT NULL

UNION ALL 

SELECT
    'ntb' AS segment_group
    
    , COUNT(DISTINCT user_id) AS total_users 
    , sum(ntb_total_product_sales) AS total_product_sales
    , sum(ntb_total_units_sold) AS total_units_sold
    , sum(ntb_total_product_sales) / COUNT(DISTINCT user_id) AS sales_per_user 
    , sum(ntb_total_units_sold) / COUNT(DISTINCT user_id) AS units_per_user    
    
FROM extended_purchases_all    
WHERE user_id IS NOT NULL
    AND ntb_cust = 1
    
UNION ALL 

SELECT
    'ntb_oneoff' AS segment_group
    
    , COUNT(DISTINCT user_id) AS total_users 
    , SUM(ntb_total_product_sales) AS total_product_sales 
    , SUM(ntb_total_units_sold) AS total_units_sold 
    , sum(ntb_total_product_sales) / COUNT(DISTINCT user_id) AS sales_per_user 
    , sum(ntb_total_units_sold) / COUNT(DISTINCT user_id) AS units_per_user    
    
FROM extended_purchases_all    
WHERE user_id IS NOT NULL
    AND ntb_oneoff_cust = 1
        
UNION ALL 

SELECT
    'ntb_repeat' AS segment_group
    
    , COUNT(DISTINCT user_id) AS total_users 
    , SUM(total_product_sales) AS total_product_sales 
    , SUM(total_units_sold) AS total_units_sold 
    , SUM(total_product_sales) / COUNT(DISTINCT user_id) AS sales_per_user  
    , sum(total_units_sold) / COUNT(DISTINCT user_id) AS units_per_user          
    
FROM extended_purchases_all    
WHERE user_id IS NOT NULL
    AND ntb_repeat_cust = 1
    
UNION ALL 

SELECT
    'existing' AS segment_group
    
    , COUNT(DISTINCT user_id) AS total_users 
    , SUM(total_product_sales) AS total_product_sales 
    , SUM(total_units_sold) AS total_units_sold 
    , SUM(total_product_sales) / COUNT(DISTINCT user_id) AS sales_per_user    
    , sum(total_units_sold) / COUNT(DISTINCT user_id) AS units_per_user              
    
FROM extended_purchases_all    
WHERE user_id IS NOT NULL
    AND existing_cust = 1
        
UNION ALL 

SELECT
    'existing_oneoff' AS segment_group
    
    , COUNT(DISTINCT user_id) AS total_users 
    , SUM(total_product_sales) AS total_product_sales 
    , SUM(total_units_sold) AS total_units_sold 
    , SUM(total_product_sales) / COUNT(DISTINCT user_id) AS sales_per_user   
    , sum(total_units_sold) / COUNT(DISTINCT user_id) AS units_per_user              
    
FROM extended_purchases_all    
WHERE user_id IS NOT NULL
    AND existing_oneoff_cust = 1
    
UNION ALL 

SELECT
    'existing_repeat' AS segment_group
    
    , COUNT(DISTINCT user_id) AS total_users 
    , SUM(total_product_sales) AS total_product_sales 
    , SUM(total_units_sold) AS total_units_sold 
    , SUM(total_product_sales) / COUNT(DISTINCT user_id) AS sales_per_user 
    , sum(total_units_sold) / COUNT(DISTINCT user_id) AS units_per_user              
    
FROM extended_purchases_all    
WHERE user_id IS NOT NULL
    AND existing_repeat_cust = 1
```
