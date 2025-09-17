## Metadata

**id:** 5f82ae6822efd44936e51034147238712b0f7fd945507aad3a9d2f5a7d0ce580
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Understand the impact when shoppers are exposed to Sponsored Products and DSP ads.
**tags:** []
**use_case_categories:** ['Overlap']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction

### 1.1 Purpose
This instructional query (IQ) demonstrates the impact that Sponsored Products has when used together with DSP Display (Display) advertising. Specifically, when shoppers are exposed to both Display and Sponsored Products ads, what is the impact on ad-attributed purchase rate?

### 1.2 Requirements 
Advertisers must have data from both Sponsored Products and Display in a single AMC instance. Both ad types should have advertised the same products during the same time period. Both ad products should have been running for at least one week during the same time period. Wait at least 14 full days after the query’s end date to use this use case. See an explanation in section 2.6. 


## 2. Data Query Instructions

### 2.1 Data Returned 
The query from this use case returns the exposure group, unique reach, a count of unique users that purchased, and total purchases. Exposure groups are Sponsored Products only, Display only, or both. See section 3 for details.

### 2.2 Tables Used 
- **sponsored_ads_traffic:** Use this filter `ad_product_type = 'sponsored_products'` to get Sponsored Products traffic. Currently, we only have released sponsored products to all customers, so this should be the only data in the table. However, if your instance has sponsored brands data in it, this filter is required. 
- **dsp_impressions** 
- **amazon_attributed_events_by_traffic_time**: Since the query uses the `traffic_time` attributed table, run the workflow at least 14 full days after the query’s end date. For example, if the time period you select for the workflow has a start date of 11/1 and an end date of 11/30, you should wait until 12/15 or later to run the query. If you decided to run this workflow to evaluate performance during the month of November on 12/1, then query will run, but you will exclude ad-attributed sales from customers that were exposed to the ad during the last two weeks of the flight who will go on to purchase up until 12/14.

### 2.3 Query Template 

The query is located in the appendix of this page. Scroll down to see the query template.

Search for the comment ‘UPDATE’ to find the two locations that must be updated in the query. Placeholder Display campaign IDs `(1111111111),(2222222222)` and Sponsored Products campaign names `('SP_campaign_name1', 'SP_campaign_name2')` have been added to the code as examples and must be changed.


### 2.5 CFIDs 

We lack Customer Facing IDs (CFIDs) for Sponsored Products. This is a limitation that is being actively worked on. What this means is that filtering based on campaign or advertiser ids is not possible, as only the internal ids currently exist. To filter to a campaign or advertiser, please use the name of the advertiser or campaign
until CFIDs are available.

### 2.6 Time Considerations 

Currently, it is impossible to predict how much time any given query will take. One reason for this is that query time is related to number of advertisers, and the amount of traffic and conversions for each advertiser. One advertiser may have more data than ten others, and having thirty advertisers will cause the query to pull
more data. When developing a query or running it for the first time, run it on a very small window of time (such as a day) to ensure that it can complete. Then attempt a week or a month. Running on a month of data is unlikely but possible for some instances with few advertisers or instances with low amounts of overall data. If you run into a specific blocker, please reach out to the AMC team to determine if we can help. The AMC engineering team is working on performance improvements throughout 2021.


## 3. Data Interpretation Instructions

### 3.1 Example Query Results

3.1.1 An example of data from the SQL query is below. This data is for instructional purposes only. Your results will differ. 

| **exposure\_group** | **unique\_reach** | **users\_that\_purchased** | **purchases** |
| ----------------------- | --------------------- | ------------------------------ | ----------------- |
| both                | 201,820               | 5,590                          | 6,280             |
| none                | 40                    | 310                            | 345               |
| SP                  | 807,175               | 8,680                          | 10,160            |
| DISPLAY             | 2,036,761             | 1,842                          | 1,921             |

### 3.2 Exposure Groups Defined 

| **exposure\_group** | **definition**                                                                                                                                                                                                       |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| both                | Users exposed to both Sponsored Products and Display ads.                                                                                                                                                            |
| none                | Users that purchased, but did not have an impression recorded under their user id when the impression was served. Ignore this row for your insights.                                                                 |
| SP                  | This group includes users exposed to Sponsored Products only and users who were exposed to both Sponsored Products and Display but the Display impression happened after a conversion and therefore is not relevant. |
| DISPLAY             | This group includes users exposed to Display only and users who were exposed to both Sponsored Products and Display but the Sponsored Products impression happened after a conversion and therefore is not relevant. |


### 3.3 Metrics Defined 

| **metric**             | **definition**                                                                                                                                                                                                                |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| unique\_reach          | Number of distinct users reached. Each unique user is assigned to one exposure group only.                                                                                                                                    |
| users\_that\_purchased | Number of unique users who purchased at least one time.                                                                                                                                                                       |
| purchases              | The number of times any amount of a promoted product or products are included in a purchase event. Purchase events include video rentals and new Subscribe & Save subscriptions. Sum of purchases from users\_that\_purchased. |


### 3.4 Insights and Data Interpretation 

In the sections below, the ‘none’ group will be ignored.

#### **3.4.1 What percentage of users were exposed to Display only, Sponsored Products only, and both?** 
This insight uses the exposure_group and unique_reach from the query data.
- First, create a TOTAL row to calculate the total unique reach across all exposure groups.
- Second, calculate the % reach using the unique reach in each exposure group in the numerator and the total unique reach in the denominator. See the example below.

| **exposure\_group** | **unique\_reach** | **% reach** |
| ----------------------- | --------------------- | --------------- |
| both                | 201,820               | 6.63%           |
| SP                  | 807,175               | 26.50%          |
| DISPLAY             | 2,036,761             | 66.87%          |
| TOTAL               | 3,045,756             | 100%            |

#### **3.4.2 What is the purchase rate of each exposure group?**
For each exposure group, calculate the purchase rate using users_that_purchased in the numerator and unique_reach in the denominator.

| **exposure\_group** | **unique\_reach** | **users\_that\_purchased** | **purchase rate** |
| ----------------------- | --------------------- | ------------------------------ | --------------------- |
| both                | 201,820               | 5,590                          | 2.77%                 |
| SP                  | 807,175               | 8,680                          | 1.08%                 |
| DISPLAY             | 2,036,761             | 1,842                          | 0.09%                 |

Note that you can also use purchases in the numerator.

#### **3.4.3 When users are exposed to Sponsored Products and Display, how much more likely are they to purchase?** 

We can compare the ‘both’ group to two groups to understand the impact that exposure to both ad products has on purchase rate.
- When users are exposed to Sponsored Products and Display, they are `(2.77%/1.08%) = 2.56x` more likely to purchase when compared to users exposed to Sponsored Products only.
- When users are exposed to Sponsored Products and Display, they are `(2.77%/ 0.09%) = 30.78x` more likely to purchase when compared to users exposed to Display only.




## 4. Query Template

Search for the comment ‘UPDATE’ to find the two locations that must be updated in the query. Placeholder Display campaign IDs `(11111111111),(2222222222)` and Sponsored Products campaign names `('SP_campaign_name1', 'SP_campaign_name2')` have been added to the code as examples and must be changed.



```
-- Instructional Query: Sponsored Products and DSP Display Overlap -- 

-- UPDATE with your Display campaign ID(s)
with Display (campaign_id) AS (
    VALUES (11111111111),(2222222222)
  ), 

-- UPDATE the Sponsored Products campaign. The name must be used. We do not have a customer-facing campaign_id available yet for Sponsored Ads.
SP (campaign) AS (
    VALUES ('SP_campaign_name1'),('SP_campaign_name2')
  ),   
  
  
-- all purchases attributed to Display or SP
purchases AS (
    SELECT user_id
         , conversion_event_dt
         , UUID() AS conversion_id
         , SUM(if(ad_product_type IS NULL, purchases, 0)) AS display_purchases
         , SUM(IF(ad_product_type = 'sponsored_products',
            total_purchases_clicks, 0)) AS search_purchases
         FROM amazon_attributed_events_by_traffic_time
         WHERE (
         campaign_id IN (SELECT campaign_id FROM Display)
         OR
         campaign IN (SELECT campaign FROM SP ))
    GROUP BY 1, 2, 3
),

-- one line per user_id with date of last purchase
     purchases_last_total as
         (
             SELECT user_id
                  , max(conversion_event_dt) AS conversion_event_dt_last
                  , sum(display_purchases + search_purchases) AS purchases
                  , sum(display_purchases) AS display_purchases
                  , sum(search_purchases) AS search_purchases
             FROM purchases
             WHERE display_purchases + search_purchases > 0
             GROUP BY 1
         ),

-- UNION all table to get all impressions: Sponsored and Display
     impressions AS (

         -- Table for Sponsored Products Impressions
         SELECT user_id
              , UUID()           AS impression_id
              , event_dt         AS impression_dt
              , 'SP'             AS IMP_ad_type
              , sum(impressions) AS impressions
         FROM sponsored_ads_traffic
         WHERE impressions > 0
           AND ad_product_type = 'sponsored_products'
        AND campaign IN (SELECT campaign from SP)
         GROUP BY 1, 2, 3

         UNION ALL

         --Table for Display
         SELECT user_id
              , UUID()           AS impression_id
              , impression_dt    AS impression_dt
              , 'DISPLAY'        AS IMP_ad_type
              , sum(impressions) AS impressions
         FROM dsp_impressions
         WHERE impressions > 0
         AND campaign_id IN (SELECT campaign_id FROM Display)
         GROUP BY 1, 2, 3
     ),


-- reducing impression table, max 2 rows per user_id
     impressions_first_total as
         (
             SELECT user_id
                  , IMP_ad_type
                  , min(impression_dt) AS impression_dt_first
                  , sum(impressions)   AS impressions
             from impressions
             GROUP BY 1, 2
         ),

/*
Here, we combine purchases + impressions in one table and add a new dimension
for impression timing. Note: not all user_ids can be matched between conversions
and impressions. This is a known issue in our backlog to address.
*/

     combined AS (
         SELECT imp.user_id AS imp_USER
              , pur.user_id AS pur_USER
              , IMP_ad_type
              , impressions
              , impression_dt_first
              , purchases
              , display_purchases
              , search_purchases
              , conversion_event_dt_last
              , CASE
                    WHEN conversion_event_dt_last IS NULL THEN 'no purchase'
                    WHEN conversion_event_dt_last > impression_dt_first THEN
                    'pre-purchase'
                    WHEN conversion_event_dt_last < impression_dt_first THEN
                    'post-purchase'
                    ELSE 'other'
             END            AS impression_timing
         FROM purchases_last_total pur
                  FULL OUTER JOIN impressions_first_total imp
                                  ON pur.user_id = imp.user_id
     ),
     -- We bin the data based on the impression timing and separate by ad product
     binned_data AS (
         SELECT imp_USER
              , pur_USER
              , SUM(IF(impression_timing = 'no purchase'
                    AND IMP_ad_type = 'DISPLAY', impressions,
                       0)) AS no_purch_DISPLAY_impressions
              , SUM(IF(impression_timing = 'pre-purchase'
                    AND IMP_ad_type = 'DISPLAY', impressions,
                       0)) AS pre_purch_DISPLAY_impressions
              , SUM(IF(impression_timing = 'post-purchase'
                    AND IMP_ad_type = 'DISPLAY', impressions,
                       0)) AS post_purch_DISPLAY_impressions
              , SUM(IF(impression_timing = 'other'
                    AND IMP_ad_type = 'DISPLAY', impressions,
                       0)) AS other_DISPLAY_impressions
              , SUM(IF(IMP_ad_type = 'DISPLAY', impressions, 0))
                    AS total_DISPLAY_impressions
              , SUM(IF(impression_timing = 'no purchase'
                    AND IMP_ad_type = 'SP', impressions,
                       0)) AS no_purch_SP_impressions
              , SUM(IF(impression_timing = 'pre-purchase'
                    AND IMP_ad_type = 'SP', impressions,
                       0)) AS pre_purch_SP_impressions
              , SUM(IF(impression_timing = 'post-purchase'
                    AND IMP_ad_type = 'SP', impressions,
                       0)) AS post_purch_SP_impressions
              , SUM(
                 IF(impression_timing = 'other'
                    AND IMP_ad_type = 'SP', impressions, 0)) AS other_SP_impressions
              , SUM(IF(IMP_ad_type = 'SP', impressions, 0))  AS total_SP_impressions
              , MAX(purchases)                               AS purchases
              , MAX(display_purchases)                       AS display_purchases
              , MAX(search_purchases)                        AS search_purchases

         FROM combined
         GROUP BY 1, 2
     ),

 users_purchased as
         (SELECT imp_USER
             , pur_USER
             , SUM(no_purch_DISPLAY_impressions)  AS no_purch_DISPLAY_impressions
             , SUM(pre_purch_DISPLAY_impressions) AS pre_purch_DISPLAY_impressions
             , SUM(no_purch_SP_impressions)       AS no_purch_SP_impressions
             , SUM(pre_purch_SP_impressions)      AS pre_purch_SP_impressions
             FROM binned_data
             WHERE purchases > 0
             GROUP BY 1, 2),

 users_purchased_exp AS
        (SELECT IF(
           (pre_purch_DISPLAY_impressions > 0 AND pre_purch_SP_impressions > 0) OR
             (no_purch_DISPLAY_impressions > 0 AND no_purch_SP_impressions > 0),
              'both',
                 IF(
                 (pre_purch_DISPLAY_impressions > 0
                  OR no_purch_DISPLAY_impressions > 0)
                AND (pre_purch_SP_impressions = 0 AND no_purch_SP_impressions = 0),
               'DISPLAY',
                 IF(
                  (pre_purch_DISPLAY_impressions = 0
                   AND no_purch_DISPLAY_impressions = 0) AND
                   (pre_purch_SP_impressions > 0
                    OR no_purch_SP_impressions > 0), 'SP',
                  IF(
                     pre_purch_DISPLAY_impressions = 0 AND
                     no_purch_DISPLAY_impressions = 0 AND
                     pre_purch_SP_impressions = 0 AND
                     no_purch_SP_impressions = 0, 'none', 'NA')
                           )))   AS exposure_group
            , COUNT(DISTINCT imp_USER) AS users_that_purchased_with_impressions
            , COUNT(DISTINCT pur_USER) AS users_that_purchased
             FROM users_purchased
             GROUP BY exposure_group
         ),

/*Create an exposure group that shows when a row was exposed to one, both,
or neither ad product*/

users_all_exp as
(SELECT IF(
 (pre_purch_DISPLAY_impressions > 0 AND pre_purch_SP_impressions > 0) OR
   (no_purch_DISPLAY_impressions > 0 AND no_purch_SP_impressions > 0), 'both',
     IF(
   (pre_purch_DISPLAY_impressions > 0 OR no_purch_DISPLAY_impressions > 0) AND
      (pre_purch_SP_impressions = 0 AND no_purch_SP_impressions = 0), 'DISPLAY',
         IF(
         (pre_purch_DISPLAY_impressions = 0 AND no_purch_DISPLAY_impressions = 0)
         AND (pre_purch_SP_impressions > 0 OR no_purch_SP_impressions > 0), 'SP',
             IF(
                pre_purch_DISPLAY_impressions = 0
                AND no_purch_DISPLAY_impressions = 0
                AND pre_purch_SP_impressions = 0
                AND no_purch_SP_impressions = 0, 'none', 'NA')
                    ))) AS exposure_group

           , COUNT(DISTINCT imp_USER)            AS unique_reach
           , SUM(purchases)                      AS purchases
           , sum(display_purchases)              AS display_purchases
           , sum(search_purchases)               AS search_purchases
           , SUM(no_purch_DISPLAY_impressions)   AS no_purch_DISPLAY_impressions
           , SUM(pre_purch_DISPLAY_impressions)  AS pre_purch_DISPLAY_impressions
           , SUM(post_purch_DISPLAY_impressions) AS post_purch_DISPLAY_impressions
           , SUM(other_DISPLAY_impressions)      AS other_DISPLAY_impressions
           , SUM(total_DISPLAY_impressions)      AS total_DISPLAY_impressions
           , SUM(no_purch_SP_impressions)        AS no_purch_SP_impressions
           , SUM(pre_purch_SP_impressions)       AS pre_purch_SP_impressions
           , SUM(post_purch_SP_impressions)      AS post_purch_SP_impressions
           , SUM(other_SP_impressions)           AS other_SP_impressions
           , SUM(total_SP_impressions)           AS total_SP_impressions
          FROM binned_data
          GROUP BY exposure_group
         )

SELECT a.exposure_group
     , unique_reach
     , users_that_purchased
     , purchases

FROM users_all_exp a
         LEFT JOIN users_purchased_exp p
                   ON a.exposure_group = p.exposure_group

```
