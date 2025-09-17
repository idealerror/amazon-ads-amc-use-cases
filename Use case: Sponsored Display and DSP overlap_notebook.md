## Metadata

**id:** 27dd225a890c2cfa2d8d2b733ecbf3075df94fd0866b08a3f9af31fd0b2d9392
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Understand the impact when shoppers are exposed to Sponsored Display and DSP ads.
**tags:** []
**use_case_categories:** ['Overlap']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 Purpose 
This instructional query (IQ) demonstrates the impact that Sponsored Display has when used together with DSP advertising. Specifically, when shoppers are exposed to both DSP and Sponsored Display ads, what is the impact on the ad-attributed purchase rate? 

### 1.2 Requirements 
Advertisers must have data from both Sponsored Display and DSP in a single AMC instance. Both ad types should have advertised the same products during the same time period. Both ad products should have been running for at least one week during the same time period. Wait at least 14 full days after the reporting period’s end date to use this IQ. 


## 2. Data Query Instructions
### 2.1 Data Returned
The query from this use case returns the exposure group, unique reach, a count of unique users that purchased, and total purchases. Exposure groups are Sponsored Display only, Display only, or both.

### 2.2 Tables Used
* **dsp_impressions**
* **sponsored_ads_traffic:** Use the filter ad_group_type = 'sponsored_display' to get Sponsored Display traffic. 
* **amazon_attributed_events_by_traffic_time**: Sponsored Display uses the date of the traffic and not the date of the conversion to report attributed conversions. Refer to [Attributed events and conversion tables](https://advertising.amazon.com/help/GHNTMYL5DJBJEQC9) in Support Center to learn more. Since the query uses the traffic_time attributed table, run the workflow at least 14 full days after the query’s end date. For example, if the time period you select for the workflow has a start date of 11/1 and an end date of 11/30, you should wait until 12/15 or later to run the query. If you decided to run this workflow to evaluate performance during the month of November on 12/1, then query will run, but you will exclude ad-attributed sales from customers that were exposed to the ad during the last two weeks of the flight who will go on to purchase up until 12/14.




## 3. Data Interpretation Instructions
### 3.1 Example Query Results
This data is for instructional purposes only. Your results will differ.

|exposure_group|unique_reach|users_that_purchased|purchases|
|---|---|---|---|
|DSP|1492486|431|569|
|none|12|39|76|
|both|188655|1967|3023|
|SD|279306|234|393|


### 3.2 Metrics Defined 
|dimensions and metrics|definition|
|:---|:---|
|DSP|This group includes users exposed to Display only and users who were exposed to both Sponsored Display and DSP but the Sponsored Display impression happened after a conversion and therefore is not relevant.|
|SD|This group includes users exposed to Sponsored Display only and users who were exposed to both Sponsored Display and  DSP but the DSP impression happened after a conversion and therefore is not relevant.|
|both|Users exposed to both Sponsored Display and DSP ads.|
|none|Users that purchased, but did not have an impression recorded under their user id when the impression was served. Ignore this row for your insights.|
|unique_reach|Number of unique users reached in this exposure group.|
|users_that_purchased|Number of unique users in this exposure group that purchased the tracked ASINs.|
|purchases|Ad attributed purchases from this exposure group.|



### 3.3 Insights and Data Interpretation 

In the sections below, the ‘none’ group will be ignored.

**What percentage of users were exposed to DSP only, Sponsored Display only and both?**

This insight uses the exposure_group and unique_reach from the query data.

* First, create a TOTAL row to calculate the total unique reach across all exposure groups.
* Second, calculate the % reach using the unique reach in each exposure group in the numerator and the total unique reach in the denominator. See the example below.

|exposure_group|unique_reach|% reach|
|:---|:---|:---|
|DSP|1,492,486|76.13%|
|both|188,655|9.62%|
|SD|279,306|14.25%|
|Total|1,960,447|100.00%|

**What is the purchase rate of each exposure group?**

For each exposure group, calculate the purchase rate using users_that_purchased in the numerator and unique_reach in the denominator. Note that you can also use purchases in the numerator.

|exposure_group|unique_reach|users_that_purchased|purchase rate|
|:---|:---|:---|:---|
|DSP|1,492,486|431|0.03%|
|both|188,655|1967|1.04%|
|SD|279,306|234|0.08%|

**When users were exposed to both Sponsored Display and DSP ad products, how much more likely are they to purchase?**

Compare the purchase rate of the ‘both’ group to the other two groups. 

* When users are exposed to Sponsored Display and DSP, they are (1.04%/0.08%) = 13x more likely to purchase when compared to users exposed to Sponsored Display only.
* When users are exposed to Sponsored Display and DSP, they are (1.04%/ 0.03%) = 35x more likely to purchase when compared to users exposed to DSP only.




## 4. Queries

### 4.1 Exploratory Query
Use this exploratory query to make a decision about the optional filters to add to the Sponsored Display and DSP Overlap Query Template.


```
-- Instructional Query: Exploratory Query for Sponsored Display and DSP Overlap Query -- 

SELECT
  campaign,
  campaign_id,
  CASE
    WHEN ad_product_type IS NULL THEN 'DSP'
    ELSE ad_product_type
  END AS ad_product_type,
  SUM(purchases) AS purchases
FROM
  amazon_attributed_events_by_traffic_time
WHERE
  (ad_product_type = 'sponsored_display'
  OR ad_product_type IS NULL)
  -- OPTIONAL: UPDATE the list below to only retrieve campaigns that tracked a particular set of ASINs.
  -- AND tracked_asin in ('ASIN1234', 'ASIN5678')
GROUP BY
  1,
  2,
  3


```

### 4.2 Query Template 
The Sponsored Display and DSP Overlap Query Template is below. Based on the results from the exploratory query above, update the CTE in the following template to filter relevant DSP and Sponsored Display campaigns. Search ‘UPDATE’ for all customization instructions. 


```
-- Instructional Query: Sponsored Display and DSP Overlap Query --
-- UPDATE with your DSP campaign ID(s)
WITH DSP (campaign_id) AS (
  VALUES
    (1111111111111),
    (2222222222222)
),
-- UPDATE the Sponsored Display campaigns. The name must be used. We do not have a customer-facing campaign_id available yet for Sponsored Ads.
SD (campaign) AS (
  VALUES
    ('SD_campaign_name_1'),
    ('SD_campaign_name_2')
),
-- all purchases attributed to DSP or SD
purchases AS (
  SELECT
    user_id,
    conversion_event_dt,
    UUID() AS conversion_id,
    SUM(if(ad_product_type IS NULL, purchases, 0)) AS dsp_purchases,
    SUM(
      IF(
        ad_product_type = 'sponsored_display',
        purchases_clicks,
        0
      )
    ) AS search_purchases
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    (
      campaign_id IN (
        SELECT
          campaign_id
        FROM
          DSP
      )
      OR campaign IN (
        SELECT
          campaign
        FROM
          SD
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
    SUM(dsp_purchases + search_purchases) AS purchases,
    SUM(dsp_purchases) AS dsp_purchases,
    SUM(search_purchases) AS search_purchases
  FROM
    purchases
  WHERE
    dsp_purchases + search_purchases > 0
  GROUP BY
    1
),
-- UNION all table to get all impressions: Sponsored and DSP
impressions AS (
  -- Table for Sponsored Display Impressions
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
  UNION ALL
  --Table for DSP
  SELECT
    user_id,
    UUID() AS impression_id,
    impression_dt AS impression_dt,
    'DSP' AS IMP_ad_type,
    SUM(impressions) AS impressions
  FROM
    dsp_impressions
  WHERE
    impressions > 0
    AND campaign_id IN (
      SELECT
        campaign_id
      FROM
        DSP
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
    dsp_purchases,
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
        AND IMP_ad_type = 'DSP',
        impressions,
        0
      )
    ) AS no_purch_DSP_impressions,
    SUM(
      IF(
        impression_timing = 'pre-purchase'
        AND IMP_ad_type = 'DSP',
        impressions,
        0
      )
    ) AS pre_purch_DSP_impressions,
    SUM(
      IF(
        impression_timing = 'post-purchase'
        AND IMP_ad_type = 'DSP',
        impressions,
        0
      )
    ) AS post_purch_DSP_impressions,
    SUM(
      IF(
        impression_timing = 'other'
        AND IMP_ad_type = 'DSP',
        impressions,
        0
      )
    ) AS other_DSP_impressions,
    SUM(IF(IMP_ad_type = 'DSP', impressions, 0)) AS total_DSP_impressions,
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
    MAX(purchases) AS purchases,
    MAX(dsp_purchases) AS dsp_purchases,
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
    SUM(no_purch_DSP_impressions) AS no_purch_DSP_impressions,
    SUM(pre_purch_DSP_impressions) AS pre_purch_DSP_impressions,
    SUM(no_purch_SD_impressions) AS no_purch_SD_impressions,
    SUM(pre_purch_SD_impressions) AS pre_purch_SD_impressions
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
        pre_purch_DSP_impressions > 0
        AND pre_purch_SD_impressions > 0
      )
      OR (
        no_purch_DSP_impressions > 0
        AND no_purch_SD_impressions > 0
      ),
      'both',
      IF(
        (
          pre_purch_DSP_impressions > 0
          OR no_purch_DSP_impressions > 0
        )
        AND (
          pre_purch_SD_impressions = 0
          AND no_purch_SD_impressions = 0
        ),
        'DSP',
        IF(
          (
            pre_purch_DSP_impressions = 0
            AND no_purch_DSP_impressions = 0
          )
          AND (
            pre_purch_SD_impressions > 0
            OR no_purch_SD_impressions > 0
          ),
          'SD',
          IF(
            pre_purch_DSP_impressions = 0
            AND no_purch_DSP_impressions = 0
            AND pre_purch_SD_impressions = 0
            AND no_purch_SD_impressions = 0,
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
        pre_purch_DSP_impressions > 0
        AND pre_purch_SD_impressions > 0
      )
      OR (
        no_purch_DSP_impressions > 0
        AND no_purch_SD_impressions > 0
      ),
      'both',
      IF(
        (
          pre_purch_DSP_impressions > 0
          OR no_purch_DSP_impressions > 0
        )
        AND (
          pre_purch_SD_impressions = 0
          AND no_purch_SD_impressions = 0
        ),
        'DSP',
        IF(
          (
            pre_purch_DSP_impressions = 0
            AND no_purch_DSP_impressions = 0
          )
          AND (
            pre_purch_SD_impressions > 0
            OR no_purch_SD_impressions > 0
          ),
          'SD',
          IF(
            pre_purch_DSP_impressions = 0
            AND no_purch_DSP_impressions = 0
            AND pre_purch_SD_impressions = 0
            AND no_purch_SD_impressions = 0,
            'none',
            'NA'
          )
        )
      )
    ) AS exposure_group,
    COUNT(DISTINCT imp_USER) AS unique_reach,
    SUM(purchases) AS purchases,
    SUM(dsp_purchases) AS dsp_purchases,
    SUM(search_purchases) AS search_purchases,
    SUM(no_purch_DSP_impressions) AS no_purch_DSP_impressions,
    SUM(pre_purch_DSP_impressions) AS pre_purch_DSP_impressions,
    SUM(post_purch_DSP_impressions) AS post_purch_DSP_impressions,
    SUM(other_DSP_impressions) AS other_DSP_impressions,
    SUM(total_DSP_impressions) AS total_DSP_impressions,
    SUM(no_purch_SD_impressions) AS no_purch_SD_impressions,
    SUM(pre_purch_SD_impressions) AS pre_purch_SD_impressions,
    SUM(post_purch_SD_impressions) AS post_purch_SD_impressions,
    SUM(other_SD_impressions) AS other_SD_impressions,
    SUM(total_SD_impressions) AS total_SD_impressions
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
