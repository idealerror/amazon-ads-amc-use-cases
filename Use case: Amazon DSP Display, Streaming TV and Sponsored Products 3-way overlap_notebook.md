## Metadata

**id:** 2bb8b4ee5bf73bd88afe7da2596e4b02a84fa67ea8719647367cfac1ebed4cbc
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Measure the impact of a full-funnel strategy across Display, Streaming TV, and Sponsored Products
**tags:** []
**use_case_categories:** ['Overlap']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction 
### 1.1 Purpose 
What is the impact of running Sponsored Products together with Streaming TV  (formerly called Over the Top or OTT) and Display advertising? Specifically, when shoppers are exposed to Display, Streaming TV, Sponsored Ads or any combination of aforementioned ad products, what is the impact on ad-attributed purchase rates? This is an expansion of the Sponsored Products and DSP Display overlap, and DSP Display and Streaming TV overlap instructional queries (IQ). This IQ will allow you to analyze reach and performance across up to three different custom defined groupings, measuring the impact of a full-funnel strategy across multiple ad tactics, including DSP Display, Streaming TV and Sponsored Products. 

### 1.2 Requirements
Advertisers must have data from DSP Display, Streaming TV, and Sponsored Products in a single AMC instance. All three ad types should have advertised the same products during the same time period. All three ad products should have been running for at least one week during the same time period. Due to Amazon’s 14-day attribution window, wait at least 14 full days after the query’s end date to use this use case in order to capture all conversions. 


## 2. Data Query Instructions 
The query from this use case returns the exposure group, unique reach, a count of unique users that purchased, and total purchases. Exposure groups are users who exposed to Sponsored Products only, Display only, Streaming TV only, Sponsored Products & Display, Sponsored Products & Streaming TV, Streaming TV & Display or all three. 

### 2.1 Tables Used 

- **sponsored_ads_traffic**: filtered to use ad_group_type ='sponsored_products'. Currently, we only have released sponsored products to all customers, so this should be the only data in the table. However, if your instance has Sponsored Brands or Sponsored Display data in it, this filter is required.

- **dsp_impressions**: this table contains impression data for DSP Display and Streaming TV. 

- **amazon_attributed_events_by_traffic_time**: since the query uses the ‘traffic_time’ attributed table, run the workflow at least 14 full days after the query’s end date. For example, if the time period you select for the workflow has a start date of 11/1 and an end date of 11/30, you should wait until 12/15 or later to run the query. If you decided to run this workflow to evaluate performance during the month of November on 12/1, then query will run, but you will exclude ad-attributed sales from customers that were exposed to the ad during the last two weeks of the flight who will go on to purchase up until 12/14. 

### 2.2 Query Template 

The query is located in the appendix of this page. Scroll down to see the query template.

Search for the comment “UPDATE” to find the 3 locations where you need to update in the query. The campaign_id for DSP Display and Streaming TV must be updated to your campaign ID(s). The current placeholder is (111122222). The Sponsored Products campaign name(s) must be updated to your campaign name(s). The current placeholder is (‘SP_campaign_name1’).


## 3. Data Interpretation Instructions 
The query template has comments throughout to help guide interpretation of each individual section. Please note that user_id is a highly sensitive field, so we sometimes take special actions to prevent potentially exposing data. For example, we need to take the binned_data and 1) find all users that purchased and group them by exposure (users_purchased_exp) and 2) sum all the impressions by exposure group (users_all_exp). Attempting to combine these actions will cause a privacy
violation and result in an error. We define exposure group here as being served ads for Streaming TV, Display, Sponsored Products, Display + Streaming TV, Streaming TV + Sponsored Products, Display + Sponsored Products, combination of all three or none. You can change these exposure groupings to include other ad products such as Sponsored Brands or another product when they are released in AMC.  


![Exposure Groups](https://m.media-amazon.com/images/G/01/adsamciql/exposuregroupsstreamingtv.jpg)


### 3.1 CFIDs 
We lack Customer Facing IDs (CFIDs) for Sponsored Products. This is a limitation that is being actively worked on. What this means is that filtering based on campaign or advertiser ids is not possible, as only the internal ids currently exist. To
filter to a campaign or advertiser, please use the name of the advertiser or campaign until CFIDs are available.

### 3.2 Example Query Results
An example of the results from the SQL query is below. This data is for in instructional purposes only. Your results will differ. 

| exposure\_group | impression\_reach | users\_that\_purchased | purchases | sales   |
| --------------- | ----------------- | ---------------------- | --------- | ------- |
| all             | 14,507            | 567                    | 687       | 17,175  |
| none            | 15,363            | 123                    | 245       | 6,125   |
| p2&p3           | 56,810            | 1,363                  | 1,900     | 47,500  |
| p1&p2           | 345,710           | 9,334                  | 10,560    | 264,000 |
| p1&p3           | 78,970            | 1,500                  | 2,478     | 61,950  |
| p3              | 69,800            | 1,256                  | 1,698     | 42,450  |
| p2              | 500,854           | 9,416                  | 11,236    | 280,900 |
| p1              | 722,219           | 8,667                  | 10,065    | 251,625 |

### 3.3 Exposure Groups Definitions
| exposure group | definition                                                                                                                                           |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| all            | Users exposed to Display, Streaming TV and Sponsored Products                                                                                                 |
| none           | Users that purchased, but did not have an impression recorded under their user id when the impression was served. Ignore this row for your insights. |
| p2&p3          | Users exposed to Streaming TV and Sponsored Products                                                                                                          |
| p1&p2          | Users exposed to Display and Streaming TV                                                                                                                     |
| p1&p3          | Users exposed to Display and Sponsored Products                                                                                                      |
| p3             | Users exposed to Sponsored Products only                                                                                                             |
| p2             | Users exposed to Streaming TV                                                                                                                                 |
| p1             | Users exposed to Display                                                                                                                             |

### 3.4 Metrics Defined 

| exposure group         | definition                                                                                                                                                                                                                 |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| unique\_reach          | Number of distinct users reached.                                                                                                                                                                      |
| users\_that\_purchased | Number of unique users who purchased at least one time.                                                                                                                                                                     |
| purchases              | Number of times any amount of a promoted product or products are included in a purchase event. Purchase events include video rentals and new Subscribe & Save subscriptions. Sum of purchases from users\_that\_purchased. |




### 3.5 Insights and Data Interpretation 
In the sections below, the ‘none’ group will be ignored. 

- **What percentage of users were exposed to different groups?** 

This insight uses the exposure_group and unique_reach from the raw data. First, create a TOTAL row to calculate the total unique reach in each exposure group in the numerator and the total unique reach in the denominator. See the example below. 

| exposure\_group | impression\_reach | % reach |
| --------------- | ----------------- | ------- |
| all             | 14,507            | 0.80%   |
| p2&p3           | 56,810            | 3.15%   |
| p1&p2           | 345,710           | 19.16%  |
| p1&p3           | 78,970            | 4.38%   |
| p3              | 69,800            | 3.87%   |
| p2              | 500,854           | 27.76%  |
| p1              | 722,219           | 40.03%  |
| TOTAL           | 1,804,233         | 100%    |


- **What is the purchase rate of each exposure group?** 

For each exposure group, calculate the purchase rate using users_that_purchased in the numerator and impression_reach in the denominator. Note you can also use purchases in the numerator, depending on your use case. 

| exposure\_group | impression\_reach | users\_that\_purchased | purchase rate |
| --------------- | ----------------- | ---------------------- | ------------- |
| all             | 14,507            | 567                    | 3.91%         |
| p2&p3           | 56,810            | 1,363                  | 2.40%         |
| p1&p2           | 345,710           | 9,334                  | 2.70%         |
| p1&p3           | 78,970            | 1,500                  | 1.90%         |
| p3              | 69,800            | 1,256                  | 1.80%         |
| p2              | 500,854           | 9,416                  | 1.88%         |
| p1              | 722,219           | 8,667                  | 1.20%         |


- **When users are exposed to all three ad types, how much more likely are they to purchase?** 

First, group users who exposed to the same number of ad types together by summing up impression_reach and users_that_purchased. Then you can calculate the purchase rate similar to the previous insight by using users_that_purchased in the numerator and impression_reach in the denominator. 

| exposure\_group | impression\_reach | users\_that\_purchased | purchase rate |
| --------------- | ----------------- | ---------------------- | ------------- |
| 3 ad types      | 14,507            | 567                    | 3.91%         |
| 2 ad types      | 481,490           | 12,197                 | 2.53%         |
| 1 ad types      | 1,292,873         | 19,339                 | 1.50%         |

Below are some examples of insight you can extract from this data: 

- Users exposed to all three ad types (Display, Streaming TV and Sponsored Products) saw 2.6x higher conversion rate compared to the average conversion rate of users exposed to one ad type 
- Users exposed to two ad types saw 1.7x higher conversion rate compared to the average conversion rate of users exposed to one ad type 
- You can also extract more granular insights at individual exposure group level. For example, users reached via Display, Streaming TV and Sponsored Products were 1.5x more likely to purchase than those reached by Display and Streaming TV (p1 & p2) and 2.2x more likely to purchase than those reached by Sponsored Products only (p3). 

Example visual: 

![purchase rate by exposure group](https://m.media-amazon.com/images/G/01/adsamciql/exposuregrouppr.jpg)


## 4. Query Template

Search for the comment “UPDATE” to find the 3 locations where you need to update in the query. The campaign_id for DSP Display and Streaming TV must be updated to your campaign ID(s). The current placeholder is (111122222). The Sponsored Products campaign name(s) must be updated to your campaign name(s). The current placeholder is (‘SP_campaign_name1’).


```
-- Instructional Query: DSP Display, Streaming TV and Sponsored Products Three Way Overlap

-- Exposure Group Selection: P1-Display, P2-Streaming TV, P3-Sponsored Products

--------------------------------------------------------------
-- Table for ad attributed purchases
-- Change display campaign (campaign_id) and sponsored products campaign (campaign name) here
--------------------------------------------------------------


with p1_ids as -- P1 defined as Display in this example
(SELECT campaign_id FROM
(VALUES
/*UPDATE DSP Display campaign_ids here*/ 
(111122222)) as b(campaign_id) 
),

p2_ids as -- P2 defined as Streaming TV in this example
(SELECT campaign_id FROM
(VALUES
/*UPDATE Streaming TV campaign_ids here*/ 
(111122222),
(111122222)) as b(campaign_id) 
),

p3_ids as -- P3 defined as Search in this example
(SELECT campaign FROM
(VALUES
/*UPDATE Sponsored Ads campaign names here. The name must be used. We do not have a customer-facing campaign_id available yet for Sponsored Ads*/ 
('SP_campaign_name1'),
('SP_campaign_name2'),
('SP_campaign_name3'),
('SP_campaign_name4')) as b(campaign)
),

purchases_temp as (
SELECT
  user_id
  ,campaign_id
  ,campaign
  ,conversion_event_dt
  ,sum(purchases) as purchases
  ,sum(total_purchases_clicks) as total_purchases_clicks
  ,sum(product_sales) as sales
FROM amazon_attributed_events_by_traffic_time a
WHERE (campaign_id IN (SELECT campaign_id from p1_ids) OR
       campaign_id IN (SELECT campaign_id from p2_ids) OR
       campaign IN (SELECT campaign from p3_ids))
GROUP BY 1,2,3,4
),

purchases as (
-- Use this temp table to select DSP Display/Streaming TV campaigns only
SELECT
  user_id
  ,conversion_event_dt
  , SUM(purchases) AS p1_purchases
  , 0 AS p2_purchases
  , 0 AS p3_purchases
  , SUM(sales) as sales
FROM purchases_temp a
WHERE campaign_id IN (SELECT campaign_id from p1_ids)
GROUP BY 1,2

UNION ALL

-- Use this temp table to select DSP Display/Streaming TV campaigns only
SELECT
  user_id
  ,conversion_event_dt
  , 0 AS p1_purchases
  , SUM(purchases) AS p2_purchases
  , 0 AS p3_purchases
  , SUM(sales) as sales
FROM purchases_temp a
WHERE campaign_id IN (SELECT campaign_id from p2_ids)
GROUP BY 1,2

UNION ALL

-- Use this temp table to select Sponsored Ads campaigns only
SELECT
  user_id
  ,conversion_event_dt
  , 0 AS p1_purchases
  , 0 AS p2_purchases
  , SUM(total_purchases_clicks) AS p3_purchases
  , SUM(sales) as sales
FROM purchases_temp a
JOIN (SELECT campaign FROM p3_ids) b on a.campaign = b.campaign
GROUP BY 1,2
),


-- Reducing table purchases to a table with one row per user_id with date of last purchase
purchases_last_total as
(
SELECT
user_id
, max(conversion_event_dt)                  AS conversion_event_dt_last
, sum(p1_purchases + p2_purchases + p3_purchases) AS purchases
, sum(p1_purchases)                         AS p1_purchases
, sum(p2_purchases)                         AS p2_purchases
, sum(p3_purchases)                         AS p3_purchases
, sum(sales)                                AS sales
from purchases
WHERE p1_purchases+p2_purchases+p3_purchases > 0
GROUP BY 1
),


impressions_temp as (
SELECT
user_id
, campaign_id
, campaign
, UUID()           AS impression_id
, impression_dt
, impressions AS impressions
FROM dsp_impressions a
WHERE (campaign_id IN (SELECT campaign_id from p1_ids) OR
       campaign_id IN (SELECT campaign_id from p2_ids))
AND impressions > 0

UNION all

SELECT 
user_id
, 0 as campaign_id
, a.campaign
, UUID()           AS impression_id
, event_dt         AS impression_dt
, impressions AS impressions
FROM sponsored_ads_traffic a
JOIN (SELECT campaign FROM p3_ids) b on a.campaign = b.campaign
WHERE impressions > 0
),


-- Table for all impressions
-- Union of table with Display impressions, Streaming TV impressions and SP impressions
    impressions AS (

SELECT user_id
     , impression_id
     , impression_dt    
     , 'p1' AS IMP_ad_type
     , sum(impressions) AS impressions
FROM impressions_temp
WHERE campaign_id IN (SELECT campaign_id from p1_ids)
GROUP BY 1, 2, 3, 4

UNION ALL

SELECT user_id
     , impression_id
     , impression_dt
     , 'p2' AS IMP_ad_type
     , sum(impressions) AS impressions
FROM impressions_temp
WHERE campaign_id IN (SELECT campaign_id from p2_ids)
GROUP BY 1, 2, 3, 4

UNION ALL

-- Table Sponsored Ads impressions

SELECT user_id
     , impression_id
     , impression_dt
     , 'p3'             AS IMP_ad_type
     , sum(impressions) AS impressions
FROM impressions_temp a
JOIN (SELECT campaign FROM p3_ids) b on a.campaign = b.campaign
GROUP BY 1, 2, 3, 4),


-- Reducing table impressions to a table with max three rows per user_id
impressions_first_total as
(
SELECT
user_id
,imp_ad_type
,min(impression_dt) as impression_dt_first
,sum(impressions) as impressions
from impressions
GROUP BY 1,2
),

----------------------------------------------------
--Here, we combine purchases + impressions in one table and add a new dimension for impression timing. Please note: not all purchaser user_ids can be assigned to the correct impressions and will show up in the None bucket later. This is a known issue in our backlog to address
----------------------------------------------------
combined as (
SELECT
  imp.user_id as imp_user
  ,pur.user_id as pur_user
  ,imp.imp_ad_type
  ,pur.purchases
  ,pur.p1_purchases
  ,pur.p2_purchases
  ,pur.p3_purchases
  ,imp.impressions
  ,pur.sales
    ,CASE WHEN conversion_event_dt_last IS NULL THEN 'no purchase'
    WHEN conversion_event_dt_last > impression_dt_first THEN 'pre-purchase'
    WHEN conversion_event_dt_last < impression_dt_first THEN 'post-purchase'
    ELSE 'other'
    END AS impression_timing
FROM purchases_last_total pur
FULL OUTER JOIN impressions_first_total imp ON pur.user_id = imp.user_id
),


-- We bin the data based on the impression timing and separate by ad product
binned_data as (
SELECT
 imp_user
 , pur_user
  ,SUM(IF(impression_timing = 'no purchase' AND imp_ad_type = 'p1', impressions, 0)) as no_purch_p1_impressions
  ,SUM(IF(impression_timing = 'pre-purchase' AND imp_ad_type = 'p1', impressions, 0)) as pre_purch_p1_impressions
  ,SUM(IF(impression_timing = 'post-purchase' AND imp_ad_type = 'p1', impressions, 0)) as post_purch_p1_impressions
  ,SUM(IF(impression_timing = 'other' AND imp_ad_type = 'p1', impressions, 0)) as other_p1_impressions
  ,SUM(IF(imp_ad_type = 'p1', impressions, 0)) as total_p1_impressions
  ,SUM(IF(impression_timing = 'no purchase' AND imp_ad_type = 'p2', impressions, 0)) as no_purch_p2_impressions
  ,SUM(IF(impression_timing = 'pre-purchase' AND imp_ad_type = 'p2', impressions, 0)) as pre_purch_p2_impressions
  ,SUM(IF(impression_timing = 'post-purchase' AND imp_ad_type = 'p2', impressions, 0)) as post_purch_p2_impressions
  ,SUM(IF(impression_timing = 'other' AND imp_ad_type = 'p2', impressions, 0)) as other_p2_impressions
  ,SUM(IF(imp_ad_type = 'p2', impressions, 0)) as total_p2_impressions
  ,SUM(IF(impression_timing = 'no purchase' AND imp_ad_type = 'p3', impressions, 0)) as no_purch_p3_impressions
  ,SUM(IF(impression_timing = 'pre-purchase' AND imp_ad_type = 'p3', impressions, 0)) as pre_purch_p3_impressions
  ,SUM(IF(impression_timing = 'post-purchase' AND imp_ad_type = 'p3', impressions, 0)) as post_purch_p3_impressions
  ,SUM(IF(impression_timing = 'other' AND imp_ad_type = 'p3', impressions, 0)) as other_p3_impressions
  ,SUM(IF(imp_ad_type = 'p3', impressions, 0)) as total_p3_impressions
  ,MAX(purchases) as purchases
  ,MAX(p1_purchases) as p1_purchases
  ,MAX(p2_purchases) as P2_purchases
  ,MAX(p3_purchases) as p3_purchases
  ,MAX(sales) as sales
FROM combined
GROUP BY 1,2
),

users_purchased as
(
SELECT
imp_user
, pur_user
, no_purch_p1_impressions
, pre_purch_p1_impressions
, post_purch_p1_impressions
, other_p1_impressions
, total_p1_impressions
, no_purch_p2_impressions
, pre_purch_p2_impressions
, post_purch_p2_impressions
, other_p2_impressions
, total_p2_impressions
, no_purch_p3_impressions
, pre_purch_p3_impressions
, post_purch_p3_impressions
, other_p3_impressions
, total_p3_impressions
, purchases
from binned_data
WHERE purchases > 0
),

users_purchased_exp as
(
SELECT
IF((pre_purch_p1_impressions > 0 AND pre_purch_p2_impressions > 0 AND pre_purch_p3_impressions > 0) OR  (no_purch_p1_impressions > 0 AND no_purch_p2_impressions > 0 AND no_purch_p3_impressions > 0 ), 'all',
IF((pre_purch_p1_impressions > 0 AND pre_purch_p2_impressions > 0 AND pre_purch_p3_impressions = 0) OR  (no_purch_p1_impressions > 0 AND no_purch_p2_impressions > 0 AND no_purch_p3_impressions = 0 ), 'p1&p2',
IF((pre_purch_p1_impressions > 0 AND pre_purch_p2_impressions = 0 AND pre_purch_p3_impressions > 0) OR  (no_purch_p1_impressions > 0 AND no_purch_p2_impressions = 0 AND no_purch_p3_impressions > 0 ), 'p1&p3',
IF((pre_purch_p1_impressions = 0 AND pre_purch_p2_impressions > 0 AND pre_purch_p3_impressions > 0) OR  (no_purch_p1_impressions = 0 AND no_purch_p2_impressions > 0 AND no_purch_p3_impressions > 0 ), 'p2&p3',
IF((pre_purch_p1_impressions > 0 OR no_purch_p1_impressions > 0) AND (pre_purch_p2_impressions = 0 AND no_purch_p2_impressions = 0) AND (pre_purch_p3_impressions = 0 AND no_purch_p3_impressions = 0), 'p1',
IF((pre_purch_p1_impressions = 0 AND no_purch_p1_impressions = 0) AND (pre_purch_p2_impressions > 0 OR no_purch_p2_impressions > 0) AND (pre_purch_p3_impressions = 0 AND no_purch_p3_impressions = 0), 'p2',
IF((pre_purch_p1_impressions = 0 AND no_purch_p1_impressions = 0) AND (pre_purch_p2_impressions = 0 AND no_purch_p2_impressions = 0) AND (pre_purch_p3_impressions > 0 OR no_purch_p3_impressions > 0), 'p3',
IF((pre_purch_p1_impressions = 0 AND no_purch_p1_impressions = 0) AND (pre_purch_p2_impressions = 0 AND no_purch_p2_impressions = 0) AND (pre_purch_p3_impressions = 0 AND no_purch_p3_impressions = 0) , 'none', 'NA'))))))))
AS exposure_group
,COUNT(distinct imp_user) as users_that_purchased_with_impressions
,COUNT(distinct pur_user) as users_that_purchased
FROM users_purchased
GROUP BY exposure_group
),


-- Create an exposure group that shows when a row was exposed to one, two, three, or neither ad product
users_all_exp as
(SELECT
  IF((pre_purch_p1_impressions > 0 AND pre_purch_p2_impressions > 0 AND pre_purch_p3_impressions > 0) OR  (no_purch_p1_impressions > 0 AND no_purch_p2_impressions > 0 AND no_purch_p3_impressions > 0 ), 'all',
  IF((pre_purch_p1_impressions > 0 AND pre_purch_p2_impressions > 0 AND pre_purch_p3_impressions = 0) OR  (no_purch_p1_impressions > 0 AND no_purch_p2_impressions > 0 AND no_purch_p3_impressions = 0 ), 'p1&p2',
  IF((pre_purch_p1_impressions > 0 AND pre_purch_p2_impressions = 0 AND pre_purch_p3_impressions > 0) OR  (no_purch_p1_impressions > 0 AND no_purch_p2_impressions = 0 AND no_purch_p3_impressions > 0 ), 'p1&p3',
  IF((pre_purch_p1_impressions = 0 AND pre_purch_p2_impressions > 0 AND pre_purch_p3_impressions > 0) OR  (no_purch_p1_impressions = 0 AND no_purch_p2_impressions > 0 AND no_purch_p3_impressions > 0 ), 'p2&p3',
  IF((pre_purch_p1_impressions > 0 OR no_purch_p1_impressions > 0) AND (pre_purch_p2_impressions = 0 AND no_purch_p2_impressions = 0) AND (pre_purch_p3_impressions = 0 AND no_purch_p3_impressions = 0), 'p1',
  IF((pre_purch_p1_impressions = 0 AND no_purch_p1_impressions = 0) AND (pre_purch_p2_impressions > 0 OR no_purch_p2_impressions > 0) AND (pre_purch_p3_impressions = 0 AND no_purch_p3_impressions = 0), 'p2',
  IF((pre_purch_p1_impressions = 0 AND no_purch_p1_impressions = 0) AND (pre_purch_p2_impressions = 0 AND no_purch_p2_impressions = 0) AND (pre_purch_p3_impressions > 0 OR no_purch_p3_impressions > 0), 'p3',
  IF((pre_purch_p1_impressions = 0 AND no_purch_p1_impressions = 0) AND (pre_purch_p2_impressions = 0 AND no_purch_p2_impressions = 0) AND (pre_purch_p3_impressions = 0 AND no_purch_p3_impressions = 0) , 'none', 'NA'))))))))
        AS exposure_group
,COUNT(distinct imp_user) as impression_reach
,SUM(purchases) as purchases
,SUM(p1_purchases) as p1_purchases
,SUM(p2_purchases) as P2_purchases
,SUM(p3_purchases) as p3_purchases
,SUM(no_purch_p1_impressions) as no_purch_p1_impressions
,SUM(pre_purch_p1_impressions) as pre_purch_p1_impressions
,SUM(post_purch_p1_impressions) as post_purch_p1_impressions
,SUM(other_p1_impressions) as other_p1_impressions
,SUM(total_p1_impressions) as total_p1_impressions
,SUM(no_purch_p2_impressions) as no_purch_p2_impressions
,SUM(pre_purch_p2_impressions) as pre_purch_p2_impressions
,SUM(post_purch_p2_impressions) as post_purch_p2_impressions
,SUM(other_p2_impressions) as other_p2_impressions
,SUM(total_p2_impressions) as total_p2_impressions
,SUM(no_purch_p3_impressions) as no_purch_p3_impressions
,SUM(pre_purch_p3_impressions) as pre_purch_p3_impressions
,SUM(post_purch_p3_impressions) as post_purch_p3_impressions
,SUM(other_p3_impressions) as other_p3_impressions
,SUM(total_p3_impressions) as total_p3_impressions
,SUM(sales) as sales
FROM binned_data
GROUP BY exposure_group
)

SELECT
a.exposure_group
,users_that_purchased
,impression_reach
,purchases
,sales
FROM users_all_exp a
LEFT JOIN users_purchased_exp p
ON a.exposure_group = p.exposure_group
```
