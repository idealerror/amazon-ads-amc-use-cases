## Metadata

**id:** b393fd9fa60bae71476e111bd8e122adba65525c48a9889fe08a08d37ba13bff
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Understand the impact when shoppers are exposed to Sponsored Display and Sponsored Products ads.
**tags:** []
**use_case_categories:** ['Overlap']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 Purpose 
This instructional query (IQ) demonstrates the impact that Sponsored Products has when used together with Sponsored Display advertising. Specifically, when shoppers are exposed to both Sponsored Display and Sponsored Products ads, what is the impact on the ad-attributed purchase rate? 

### 1.2 Requirements 
Advertisers must have data from Sponsored Products and Sponsored Display in a single AMC instance. Sponsored ad types should have advertised the same products during the same time period. All the ad products should have been running for at least one week during the same time period. Due to Amazon’s 14-day attribution window, wait at least 14 full days after the query’s end date to use this use case in order to capture all conversions. 


## 2. Data Query Instructions

### 2.1 Data Returned
The query from this use case returns the exposure group, unique reach, a count of unique users that purchased, and total purchases. Exposure groups are users who exposed to Sponsored Products only, Sponsored Display only, or both.

### 2.2 Tables Used
* **sponsored_ads_traffic**: filtered to use `ad_group_type ='sponsored_products'` for Sponsored Product campaigns and `ad_group_type ='sponsored_display'` for Sponsored Display campaigns.      

* **amazon_attributed_events_by_traffic_time**: Since the query uses the ‘traffic_time’ attributed table, run the workflow at least 14 full days after the query’s end date. For example, if the time period you select for the workflow has a start date of 11/1 and an end date of 11/30, you should wait until 12/15 or later to run the query. If you decided to run this workflow to evaluate performance during the month of November on 12/1, then query will run, but you will exclude ad-attributed sales from customers that were exposed to the ad during the last two weeks of the flight who will go on to purchase up until 12/14. 


## 3. Data Interpretation Instructions
### 3.1 Example Query Results
This data is for instructional purposes only. Your results will differ.

|exposure_group|unique_reach|users_that_purchased|purchases|
|:---|:---|:---|:---|
|none|19|62|63|
|both|684929|3318|3523|
|SP|1805184|3327|3505|
|SD|2530125|36|38|


### 3.2 Exposure Groups Definitions

|exposure_group|definition|
|:---|:---|
|both|Users exposed to Sponsored Display and Sponsored Products|
|SP|Users exposed to Sponsored Products only|
|SD|Users exposed to Sponsored Display only|



### 3.3 Metrics Defined

|metric|definition|
|:---|:---|
|unique_reach|Number of distinct users reached.|
|users_that_purchased|Number of unique users who purchased at least one time.|
|purchases|Number of times any amount of a promoted product or products are included in a purchase event. Purchase events include video rentals and new Subscribe & Save subscriptions. Sum of purchases from users_that_purchased.|



### 3.4 Insights and Data Interpretation

**What percentage of users were exposed to different groups?**

This insight uses the exposure_group and unique_reach from the raw data. First, create a TOTAL row to calculate the total unique reach in each exposure group in the numerator and the total unique reach in the denominator. See the example below.

|exposure_group|unique_reach|% reach|
|:---|:---|:---|
|both|684929|14%|
|SP|1805184|36%|
|SD|2530125|50%|
|TOTAL|5020238|100%|


**What is the purchase rate of each exposure group?**

For each exposure group, calculate the purchase rate using users_that_purchased in the numerator and impression_reach in the denominator. Note you can also use purchases in the numerator, depending on your use case.

|exposure_group|unique_reach|users_that_purchased|purchase_rate|
|:---|:---|:---|:---|
|both|684929|3318|0.48%|
|SP|1805184|3327|0.18%|
|SD|2530125|36|0.001%|
|||6.24|0.08%|

**Examples of recommendations based on the insights:**

- **Insight and Recommendation 1:** Users exposed to both ad types (Sponsored Display and Sponsored Products) saw a 6.2x higher conversion rate when compared to the average conversion rate of users exposed to one ad type. Run always-on Sponsored Product and Sponsored Display campaigns. 

- **Insight and Recommendation 2:** Sponsored Display ads reached a 2.5M incremental audience. Reach new audiences using Sponsored Display to help improve product discovery.



## 4. Queries

### 4.1 Exploratory Query


```
-- Instructional Query: Exploratory Query for Sponsored Display and Sponsored Products Overlap --
SELECT
  campaign,
  ad_product_type,
  SUM(purchases) AS purchases
FROM
  amazon_attributed_events_by_traffic_time
WHERE
  ad_product_type IN ('sponsored_products', 'sponsored_display') 
  -- OPTIONAL: UPDATE the list below to only retrieve campaigns that tracked a particular set of ASINs.
  -- AND tracked_asin in ('ASIN1234', 'ASIN5678')
GROUP BY
  1,
  2
```

### 4.2 Query Template
The Sponsored Product and Sponsored Display Overlap Query Template is below. Search for the comment ‘UPDATE’ to find the two locations that must be updated in the query. Placeholder Sponsored Display campaign names `('SD_campaign_name1'),('SD_campaign_name2')` and Sponsored Products campaign names `('SP_campaign_name1'), ('SP_campaign_name2')` have been added to the code as examples and must be changed. 



```
-- Instructional Query: Sponsored Products and Sponsored Display Overlap --
-- UPDATE with your Sponsored Display campaign names.
WITH SD (campaign) AS (
  VALUES
    ('SD_campaign_name1'),
('SD_campaign_name2')
),
-- UPDATE the Sponsored Products campaign names. 
SP (campaign) AS (
  VALUES
 ('SP_campaign_name1'),
('SP_campaign_name2')
),
-- all purchases attributed to SD or SP
purchases AS (
  SELECT
    user_id,
    conversion_event_dt,
    UUID() AS conversion_id,
    SUM(
      IF(
        ad_product_type = 'sponsored_display',
        total_purchases_clicks,
        0
      )
    ) AS SD_purchases,
    SUM(
      IF(
        ad_product_type = 'sponsored_products',
        total_purchases_clicks,
        0
      )
    ) AS search_purchases
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    (
      campaign IN (
        SELECT
          campaign
        FROM
          SD
      )
      OR campaign IN (
        SELECT
          campaign
        FROM
          SP
      )
    )
  GROUP BY
    1,
    2,
    3
),
-- one line per user_id with date of last purchase
purchases_last_total AS (
  SELECT
    user_id,
    MAX(conversion_event_dt) AS conversion_event_dt_last,
    SUM(SD_purchases + search_purchases) AS purchases,
    SUM(SD_purchases) AS SD_purchases,
    SUM(search_purchases) AS search_purchases
  FROM
    purchases
  WHERE
    SD_purchases + search_purchases > 0
  GROUP BY
    1
),
-- UNION all table to get all impressions: Sponsored and SD
impressions AS (
  -- Table for Sponsored Products Impressions
  SELECT
    user_id,
    UUID() AS impression_id,
    event_dt AS impression_dt,
    'SP' AS IMP_ad_type,
    SUM(impressions) AS impressions
  FROM
    sponsored_ads_traffic
  WHERE
    impressions > 0
    AND ad_product_type = 'sponsored_products'
    AND campaign IN (
      SELECT
        campaign
      FROM
        SP
    )
  GROUP BY
    1,
    2,
    3
  UNION ALL
  --Table for SD
  SELECT
    user_id,
    UUID() AS impression_id,
    event_dt AS impression_dt,
    'SD' AS IMP_ad_type,
    SUM(impressions) AS impressions
  FROM
    sponsored_ads_traffic
  WHERE
    impressions > 0
    AND ad_product_type = 'sponsored_display'
    AND campaign IN (
      SELECT
        campaign
      FROM
        SD
    )
  GROUP BY
    1,
    2,
    3
),
-- reducing impression table, max 2 rows per user_id
impressions_first_total AS (
  SELECT
    user_id,
    IMP_ad_type,
    MIN(impression_dt) AS impression_dt_first,
    SUM(impressions) AS impressions
  FROM
    impressions
  GROUP BY
    1,
    2
),
/*
 Here, we combine purchases + impressions in one table and add a new dimension
 for impression timing. Note: not all user_ids can be matched between conversions
 and impressions. This is a known issue in our backlog to address.
 */
combined AS (
  SELECT
    imp.user_id AS imp_USER,
    pur.user_id AS pur_USER,
    IMP_ad_type,
    impressions,
    impression_dt_first,
    purchases,
    SD_purchases,
    search_purchases,
    conversion_event_dt_last,
    CASE
      WHEN conversion_event_dt_last IS NULL THEN 'no purchase'
      WHEN conversion_event_dt_last > impression_dt_first THEN 'pre-purchase'
      WHEN conversion_event_dt_last < impression_dt_first THEN 'post-purchase'
      ELSE 'other'
    END AS impression_timing
  FROM
    purchases_last_total pur
    FULL OUTER JOIN impressions_first_total imp ON pur.user_id = imp.user_id
),
-- We bin the data based on the impression timing and separate by ad product
binned_data AS (
  SELECT
    imp_USER,
    pur_USER,
    SUM(
      IF(
        impression_timing = 'no purchase'
        AND IMP_ad_type = 'SD',
        impressions,
        0
      )
    ) AS no_purch_SD_impressions,
    SUM(
      IF(
        impression_timing = 'pre-purchase'
        AND IMP_ad_type = 'SD',
        impressions,
        0
      )
    ) AS pre_purch_SD_impressions,
    SUM(
      IF(
        impression_timing = 'post-purchase'
        AND IMP_ad_type = 'SD',
        impressions,
        0
      )
    ) AS post_purch_SD_impressions,
    SUM(
      IF(
        impression_timing = 'other'
        AND IMP_ad_type = 'SD',
        impressions,
        0
      )
    ) AS other_SD_impressions,
    SUM(IF(IMP_ad_type = 'SD', impressions, 0)) AS total_SD_impressions,
    SUM(
      IF(
        impression_timing = 'no purchase'
        AND IMP_ad_type = 'SP',
        impressions,
        0
      )
    ) AS no_purch_SP_impressions,
    SUM(
      IF(
        impression_timing = 'pre-purchase'
        AND IMP_ad_type = 'SP',
        impressions,
        0
      )
    ) AS pre_purch_SP_impressions,
    SUM(
      IF(
        impression_timing = 'post-purchase'
        AND IMP_ad_type = 'SP',
        impressions,
        0
      )
    ) AS post_purch_SP_impressions,
    SUM(
      IF(
        impression_timing = 'other'
        AND IMP_ad_type = 'SP',
        impressions,
        0
      )
    ) AS other_SP_impressions,
    SUM(IF(IMP_ad_type = 'SP', impressions, 0)) AS total_SP_impressions,
    MAX(purchases) AS purchases,
    MAX(SD_purchases) AS SD_purchases,
    MAX(search_purchases) AS search_purchases
  FROM
    combined
  GROUP BY
    1,
    2
),
users_purchased AS (
  SELECT
    imp_USER,
    pur_USER,
    SUM(no_purch_SD_impressions) AS no_purch_SD_impressions,
    SUM(pre_purch_SD_impressions) AS pre_purch_SD_impressions,
    SUM(no_purch_SP_impressions) AS no_purch_SP_impressions,
    SUM(pre_purch_SP_impressions) AS pre_purch_SP_impressions
  FROM
    binned_data
  WHERE
    purchases > 0
  GROUP BY
    1,
    2
),
users_purchased_exp AS (
  SELECT
    IF(
      (
        pre_purch_SD_impressions > 0
        AND pre_purch_SP_impressions > 0
      )
      OR (
        no_purch_SD_impressions > 0
        AND no_purch_SP_impressions > 0
      ),
      'both',
      IF(
        (
          pre_purch_SD_impressions > 0
          OR no_purch_SD_impressions > 0
        )
        AND (
          pre_purch_SP_impressions = 0
          AND no_purch_SP_impressions = 0
        ),
        'SD',
        IF(
          (
            pre_purch_SD_impressions = 0
            AND no_purch_SD_impressions = 0
          )
          AND (
            pre_purch_SP_impressions > 0
            OR no_purch_SP_impressions > 0
          ),
          'SP',
          IF(
            pre_purch_SD_impressions = 0
            AND no_purch_SD_impressions = 0
            AND pre_purch_SP_impressions = 0
            AND no_purch_SP_impressions = 0,
            'none',
            'NA'
          )
        )
      )
    ) AS exposure_group,
    COUNT(DISTINCT imp_USER) AS users_that_purchased_with_impressions,
    COUNT(DISTINCT pur_USER) AS users_that_purchased
  FROM
    users_purchased
  GROUP BY
    exposure_group
),
/*Create an exposure group that shows when a row was exposed to one, both,
 or neither ad product*/
users_all_exp AS (
  SELECT
    IF(
      (
        pre_purch_SD_impressions > 0
        AND pre_purch_SP_impressions > 0
      )
      OR (
        no_purch_SD_impressions > 0
        AND no_purch_SP_impressions > 0
      ),
      'both',
      IF(
        (
          pre_purch_SD_impressions > 0
          OR no_purch_SD_impressions > 0
        )
        AND (
          pre_purch_SP_impressions = 0
          AND no_purch_SP_impressions = 0
        ),
        'SD',
        IF(
          (
            pre_purch_SD_impressions = 0
            AND no_purch_SD_impressions = 0
          )
          AND (
            pre_purch_SP_impressions > 0
            OR no_purch_SP_impressions > 0
          ),
          'SP',
          IF(
            pre_purch_SD_impressions = 0
            AND no_purch_SD_impressions = 0
            AND pre_purch_SP_impressions = 0
            AND no_purch_SP_impressions = 0,
            'none',
            'NA'
          )
        )
      )
    ) AS exposure_group,
    COUNT(DISTINCT imp_USER) AS unique_reach,
    SUM(purchases) AS purchases,
    SUM(SD_purchases) AS SD_purchases,
    SUM(search_purchases) AS search_purchases,
    SUM(no_purch_SD_impressions) AS no_purch_SD_impressions,
    SUM(pre_purch_SD_impressions) AS pre_purch_SD_impressions,
    SUM(post_purch_SD_impressions) AS post_purch_SD_impressions,
    SUM(other_SD_impressions) AS other_SD_impressions,
    SUM(total_SD_impressions) AS total_SD_impressions,
    SUM(no_purch_SP_impressions) AS no_purch_SP_impressions,
    SUM(pre_purch_SP_impressions) AS pre_purch_SP_impressions,
    SUM(post_purch_SP_impressions) AS post_purch_SP_impressions,
    SUM(other_SP_impressions) AS other_SP_impressions,
    SUM(total_SP_impressions) AS total_SP_impressions
  FROM
    binned_data
  GROUP BY
    exposure_group
)
SELECT
  a.exposure_group,
  unique_reach,
  users_that_purchased,
  purchases
FROM
  users_all_exp a
  LEFT JOIN users_purchased_exp p ON a.exposure_group = p.exposure_group
```
