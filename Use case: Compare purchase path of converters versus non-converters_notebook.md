## Metadata

**id:** 9dff32201298d25a5cb6bdff831926cdbbaac0f5ea526f822312e20f338f3540
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Examine the engagement of users that purchased following either campaign exposure, detail page view, or branded search versus those that did not purchase (FSI required).
**tags:** ['Paid features']
**use_case_categories:** ['Reach and frequency', 'Path-to-conversion']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---


## 1. Introduction
### 1.1 Purpose

This IQ helps advertisers evaluate engagement patterns between the two groups following either first campaign exposure, detail page view, or branded search. Evaluate the differences between the two groups and optimize your media mix, impression frequency, and remarketing strategy. 

This IQ answers questions such as: 

* What are the average days to purchase? 
* What ad products are driving purchases? 
* What is the ideal impression level per ad product? 

![diagram 1](https://m.media-amazon.com/images/G/01/adsamciql/compare_path_1_med.png)
![diagram 2](https://m.media-amazon.com/images/G/01/adsamciql/compare_path_3_med.png)

### Requirements

* **On-Amazon conversions:** This query relies on ASIN conversions on Amazon. It is therefore limited to advertisers with on-Amazon conversions.
* **Enrollment (trial or subscription) to Flexible Shopping Insights (FSI) as a part of Amazon Insights**: Paid Features are only available in certain regions.

## Data query instructions

### Data returned

The query results are segmented by users that had a purchase conversion (`customer_type = Purchase`) following campaign exposure versus those that did not convert (`customer_type = No Purchase`). 
 
The query returns:

* Average days to purchase per user
* Average number of ad product types exposed per user
* Average number of impression events per user
* Average number of impression events per ad product type per user
* Average number of brand detail page views per user
* Average number of brand products considered per user

### Tables used

* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This data source is used for sponsored ads traffic. From this table, we are using user_id and impression_dt to identify who was exposed and when, segmented by campaign_id. 
* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): This data source is used for Amazon DSP traffic. From this table, we are using user_id and impression_dt to identify who was exposed and when, segmented by campaign_id. 
* [conversions_all](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/conversions_all_paid): We use this data source to identify purchase conversion events. 

## Data interpretation instructions

### Example query results

**This data is for instructional purposes only. Your results will differ.**

| **key_metric**                      | **customer_type** | **campaign_group** | **stats** |
|-------------------------------------|-------------------|--------------------|-----------|
| ad_products_exposed_per_user        | No Purchase       | NA                 | 1.35      |
| ad_products_exposed_per_user        | Purchase          | NA                 | 2.98      |
| days_to_purchase_per_user           | No Purchase       | NA                 |           |
| days_to_purchase_per_user           | Purchase          | NA                 | 14.87     |
| dpvs_per_user                       | No Purchase       | NA                 | 1.91      |
| dpvs_per_user                       | Purchase          | NA                 | 3.65      |
| impressions_per_user_per_ad_product | No Purchase       | DSP                | 1.24      |
| impressions_per_user_per_ad_product | No Purchase       | OLV                | 0.8       |
| impressions_per_user_per_ad_product | No Purchase       | Prime              | 0.13      |
| impressions_per_user_per_ad_product | No Purchase       | SB                 | 0.38      |
| impressions_per_user_per_ad_product | No Purchase       | SD                 | 0         |
| impressions_per_user_per_ad_product | No Purchase       | SP                 | 1.68      |
| impressions_per_user_per_ad_product | Purchase          | DSP                | 9.03      |
| impressions_per_user_per_ad_product | Purchase          | OLV                | 0.15      |
| impressions_per_user_per_ad_product | Purchase          | Prime              | 0.04      |
| impressions_per_user_per_ad_product | Purchase          | SB                 | 3.75      |
| impressions_per_user_per_ad_product | Purchase          | SD                 | 0.21      |
| impressions_per_user_per_ad_product | Purchase          | SP                 | 39.82     |
| total_impressions_per_user          | No Purchase       | NA                 | 4.23      |
| total_impressions_per_user          | Purchase          | NA                 | 52.98     |
| products_viewed_per_user            | No Purchase       | NA                 | 1.51      |
| products_viewed_per_user            | Purchase          | NA                 | 2.24      |


### Metrics defined


| **metric**                          | **definition**                                                      |
|-------------------------------------|---------------------------------------------------------------------|
| `days_to_purchase_per_user`           | Average number of days between first ad exposure event to purchase |
| `ad_products_exposed_per_user`        | Average number of ad product types exposed per user                |
| `total_impressions_per_user`          | Average number impression events per user                          |
| `impressions_per_user_per_ad_product` | Average number impression events per ad product type per user      |
| `dpvs_per_user`                       | Average number of brand detail page views per user                 |
| `products_viewed_per_user`            | Average number of brand product views per user                     |


### Insights and data interpretation

**How many ad products are purchasers exposed to? How does that compare to those who did not purchase?** 

On average purchasers are exposed to three ad products (indicated by `ad_products_exposed_per_user`), where as those who did not purchase are exposed to one. Continue multi-media strategy and diversify product mix to reach customer at numerous touch points in the purchase journey.

## Queries

This IQ contains the following queries:

* 4.1 Exploratory query to identify Amazon DSP campaigns
* 4.2 Query template: Compare purchase path from first impression
* 4.3 Query template: Compare purchase path from first search
* 4.4 Query template: Compare purchase path from first detail page view

### Exploratory query to identify Amazon DSP campaigns

Use this query to identify Amazon DSP campaign IDs for use in the queries below. Note that you do not need to identify sponsored ads campaigns; the queries below will grab data for all sponsored ads campaigns, so that you don’t have to enter a long list of campaign IDs.


```
-- Instructional Query: Compare purchase path of converters versus non-converters: (Amazon DSP campaigns) -- 
-- AD PRODUCTS: Amazon DSP
SELECT
advertiser_id,
campaign_id_string,
campaign,
campaign_start_date,
campaign_end_date,
SUM(impressions) AS total_impressions
FROM 
dsp_impressions
GROUP BY 1,2,3,4,5
```

### Query template: Compare purchase path from first impression

Run this query to evaluate the differences between customers who purchased after ad exposure compared to those that did not purchase.

Note that this query works for Amazon DSP campaigns, sponsored ads campaigns, or both. If you have Amazon DSP campaigns, define the campaign groupings and the campaign IDs identified in the exploratory query. 


```
-- Instructional Query: Compare purchase path of converters versus non-converters: From first impression to purchase --
-- AD PRODUCTS: Amazon DSP and/or sponsored ads
-- Enrollment (trial or subscription) to Flexible Shopping Insights (FSI) as a part of Amazon Insights --
/*
 ------- Customization Instructions -------
 Search REQUIRED UPDATE and enter information:
 1) REQUIRED UPDATE [1 of 3]: Enter Amazon DSP Campaign Groupings: Define the group of Amazon DSP campaigns by campaign type.
 2) REQUIRED UPDATE [2 of 3]: Enter Amazon DSP Campaign IDs: Insert campaign ids of defined Amazon DSP campaigns above.
 3) REQUIRED UPDATE [3 of 3]: Enter Impression Threshold: Remove outliers from analysis. 
 4) NOTE: You do not have to enter sponsored ads campaigns; this query will grab data for all of those campaigns.
*/
 
 
WITH campaign_group (campaign_id_string, campaign_group) AS (
 /* REQUIRED UPDATE [1 of 3]: Enter Amazon DSP Campaign Groupings: Define the group of Amazon DSP campaigns by campaign type. */
 VALUES
 ('910000000000000000', 'OLV'),
 ('910000000000000000', 'DSP')
),

dsp_campaigns (campaign_id_string) AS (
 /* REQUIRED UPDATE [2 of 3]: Enter Amazon DSP Campaign IDs: Insert campaign ids of defined Amazon DSP campaigns above. */
 VALUES
 ('910000000000000000'),
 ('910000000000000000')
),

-- Grab Amazon DSP and sponsored ads Impressions
impressions AS (
 SELECT
 COALESCE(campaign_group, 'Amazon DSP') AS campaign_group
 ,'DSP' AS product_type
 , user_id
 , MIN(impression_dt_utc) AS impression_dt_first
 , MAX(impression_dt_utc) AS impression_dt_last
 , SUM(impressions) AS impressions
 FROM 
 dsp_impressions i 
 INNER JOIN 
 campaign_group G ON g.campaign_id_string = i.campaign_id_string
 WHERE 
 user_id IS NOT NULL
 GROUP BY
 1,2,3
 UNION ALL
 SELECT
 ad_product_type AS campaign_group
 ,'sponsored ads' AS product_type
 , user_id
 , MIN(event_dt_utc) AS impression_dt_first
 , MAX(event_dt_utc) AS impression_dt_last
 , SUM(impressions) AS impressions
 FROM 
 sponsored_ads_traffic 
 WHERE 
 user_id IS NOT NULL
 AND
 ad_product_type IS NOT NULL 
 GROUP BY
 1,2,3
),

-- Remove users with over 150 impressions - eliminating users that skew results
impressions_removed AS (
 SELECT 
 user_id
 , SUM(impressions) as impressions
 FROM
 impressions
 GROUP BY 
 1
 HAVING 
/* REQUIRED UPDATE [3 of 3]: Enter Impression Threshold: Remove outliers from analysis. */
 impressions < 150
),

-- Determine purchases. dedupe conversions : 1 row per user.
converted AS ( 
 SELECT 
 user_id
 , MIN(event_dt_utc) AS first_purchase_dt
 , SUM(total_product_sales) AS total_product_sales
 FROM 
 conversions_all c
 WHERE 
 event_type_description in ('Product purchased') 
 AND 
 conversions > 0
 GROUP BY 
 1
),

-- Determine Impressions prior to purchase
impressions_combined AS (
 SELECT
 i.user_id AS impression_id 
 , i.impression_dt_first
 , i.impression_dt_last
 , i.campaign_group
 , i.impressions
 , c.user_id AS conversion_id 
 , c.first_purchase_dt 
 , CASE WHEN c.user_id IS NULL THEN 'No Purchase'
 ELSE 'Purchase'
 END as customer_type
 FROM 
 impressions i
 LEFT JOIN
 converted c ON i.user_id = c.user_id
 WHERE
 i.user_id IN (SELECT user_id FROM impressions_removed)
 AND
 ((c.user_id IS NOT NULL AND i.impression_dt_first < c.first_purchase_dt) 
 OR 
 c.user_id IS NULL)
),

-- Rank impressions dates to determine First overall impression date, regardless of ad_product/campaign_group
First_Imp_ranked AS ( 
 SELECT 
 impression_id AS user_id 
 , impression_dt_first
 , campaign_group
 , first_purchase_dt 
 , customer_type
 , ROW_NUMBER() OVER (
 PARTITION BY impression_id
 ORDER BY 
 impression_dt_first ASC) as impression_order
 FROM 
 impressions_combined
),

-- Select First Impression date overall
First_Imp AS ( 
 SELECT 
 user_id 
 , impression_dt_first
 , first_purchase_dt 
 , campaign_group
 , customer_type
 , impression_order
 , seconds_between(impression_dt_first,first_purchase_dt) AS days_diff
 FROM 
 First_Imp_ranked
 WHERE
 impression_order = 1
),

-- Determine days between first impression to purchase --
days_to_purchase AS (
 SELECT
 customer_type
 , SUM(days_diff) / 86400 / COUNT(user_id) AS days_to_purchase
 FROM
 First_Imp 
 GROUP BY
 1
),

-- Deduplicate impressions table to determine impressions by customer_type and number of campaign groups exposed
impressions_deduplicate AS (
 SELECT 
 i.impression_id
 , customer_type
 , COUNT(DISTINCT(i.campaign_group)) AS campaign_groups_exposed
 , SUM(i.impressions) AS total_impressions
 FROM
 impressions_combined i
 GROUP BY
 1,2
),

-- exposure stats overall --
exposure_stats AS (
 SELECT
 customer_type
 , SUM(total_impressions) / COUNT(impression_id) AS impressions_per_user
 , SUM(campaign_groups_exposed) / COUNT(impression_id) AS exposure_groups_per_user
 FROM
 impressions_deduplicate 
 GROUP BY
 1
),

-- Deduplicate impressions table to determine impressions by campaign_group
impressions_deduplicate_campaign AS (
 SELECT 
 customer_type
 , campaign_group
 , SUM(impressions) as impressions
 FROM
 impressions_combined i
 GROUP BY
 1,2
),

-- Deduplicate impressions table to determine impressions by customer_type
impressions_deduplicate_customer_type AS (
 SELECT 
 customer_type
 , COUNT(DISTINCT(impression_id)) as user_count
 FROM
 impressions_combined i
 GROUP BY
 1
),

-- Comnined deduped campaign and customer_type tables
impressions_deduplicate_combined AS (
 SELECT 
 a.customer_type
 , a.campaign_group
 , a.impressions
 , b.user_count
 FROM
 impressions_deduplicate_campaign a
 LEFT JOIN 
 impressions_deduplicate_customer_type b ON a.customer_type = b.customer_type
),

-- exposure stats by campaign_group --
exposure_stats_campaign AS (
 SELECT
 customer_type
 , campaign_group
 , user_count
 , impressions
 , impressions/user_count as impressions_user
 FROM 
 impressions_deduplicate_combined
),

-- Determine all DPVs. Dedupe conversions : 1 row per user.
converted_DPV AS ( 
 SELECT 
 user_id
 , tracked_item
 , event_dt_utc AS conversion_dt
 , event_type_description
 , 1 AS conversion 
 FROM 
 conversions_all c
 WHERE 
 event_subtype in ('detailPageView') 
 AND 
 conversions > 0
 AND 
 tracked_item IS NOT NULL
 AND 
 user_id IN (SELECT impression_id FROM impressions_deduplicate)
),

-- Determine DPV before purchase 
DPV_combined AS (
 SELECT
 a.user_id
 , a.customer_type
 , a.impression_dt_first
 , a.first_purchase_dt 
 , b.user_id AS dpv_id
 , b.conversion_dt
 , b.tracked_item
 , COALESCE(b.conversion,0) AS conversion
 , seconds_between(impression_dt_first,conversion_dt) AS days_diff_dpv
 FROM 
 First_Imp a
 LEFT JOIN
 converted_dpv b ON a.user_id = b.user_id
 WHERE
 a.customer_type = 'Purchase'
 AND
 b.conversion_dt BETWEEN impression_dt_first AND a.first_purchase_dt 
),

-- Determine DPV without purchase or no DPV at all following impression
DPV_combined_no_purchase AS (
 SELECT
 a.user_id
 , a.customer_type
 , a.impression_dt_first
 , a.first_purchase_dt 
 , b.user_id AS dpv_id
 , b.conversion_dt
 , b.tracked_item
 , COALESCE(b.conversion,0) AS conversion
 FROM 
 First_Imp a
 LEFT JOIN
 converted_dpv b ON a.user_id = b.user_id
 WHERE
 a.customer_type = 'No Purchase'
 AND
 b.conversion_dt > impression_dt_first 
),

-- DPV deduplicate
DPV_deduplicate AS (
 SELECT 
 user_id
 , customer_type
 , SUM(conversion) AS dpvs
 FROM
 DPV_combined
 GROUP BY
 1,2
 UNION ALL
 SELECT 
 user_id
 , customer_type
 , SUM(conversion) AS dpvs
 FROM
 DPV_combined_no_purchase
 GROUP BY
 1,2
),

-- DPV stats --
DPV_stats AS (
 SELECT
 customer_type
 , SUM(dpvs) / COUNT(user_id) AS dpvs_per_user
 FROM 
 DPV_deduplicate
 GROUP BY
 1
),
 
--DPV products viewed
DPV_products_deduplicate AS (
 SELECT 
 user_id
 , customer_type
 , COUNT(distinct(tracked_item)) AS products_viewed
 FROM 
 DPV_combined
 WHERE 
 tracked_item IS NOT NULL
 GROUP BY 
 1,2
 UNION ALL
 SELECT 
 user_id
 , customer_type
 , COUNT(distinct(tracked_item)) AS products_viewed
 FROM 
 DPV_combined_no_purchase
 WHERE 
 tracked_item IS NOT NULL
 GROUP BY 
 1,2
),

-- DPVv2: products viewed per user stats --
DPV_stats_2 AS (
 SELECT
 customer_type
 , SUM(products_viewed) / COUNT(user_id) AS products_viewed_per_user
 FROM 
 DPV_products_deduplicate
 GROUP BY
 1
),

--- Combine all stat tables into one final table ---
combined_total_stats AS (
 SELECT
 'days_to_purchase_per_user' as key_metric
 , customer_type 
 , 'NA' campaign_group
 , days_to_purchase as stats
 FROM
 days_to_purchase
 UNION ALL
 SELECT
 'ad_products_exposed_per_user' as key_metric 
 , customer_type 
 , 'NA' campaign_group
 , exposure_groups_per_user as stats
 FROM
 exposure_stats 
 UNION ALL
 SELECT
 'total_impressions_per_user' as key_metric 
 , customer_type 
 , 'NA' campaign_group
 , impressions_per_user as stats
 FROM
 exposure_stats 
 UNION ALL
 SELECT
 'impressions_per_user_per_ad_product' as key_metric 
 , customer_type 
 , campaign_group
 , impressions_user as stats
 FROM
 exposure_stats_campaign
 UNION ALL 
 SELECT
 'dpvs_per_user' as key_metric 
 , customer_type
 , 'NA' campaign_group
 , dpvs_per_user as stats
 FROM
 dpv_stats 
 UNION ALL
 SELECT
 'products_viewed_per_user' as key_metric 
 , customer_type
 , 'NA' campaign_group
 , products_viewed_per_user as stats
 FROM
 dpv_stats_2 
)
 SELECT
 key_metric
 , customer_type
 , campaign_group
 , stats
 FROM
 combined_total_stats 
```

### Query template: Compare purchase path from first search

Run this query to evaluate the differences between customers who purchased after making a branded search compared to those that did not purchase.

Note that this query works for Amazon DSP campaigns, sponsored ads campaigns, or both. If you have Amazon DSP campaigns, define the campaign groupings and the campaign IDs identified in the exploratory query. 


```
-- Instructional Query: Compare Purchase Path of Converters versus Non-Converters: First Search to Purchase --
-- AD PRODUCTS: Amazon DSP and/or sponsored ads
-- Enrollment (trial or subscription) to Flexible Shopping Insights (FSI) or Amazon Insights

/*
 ------- Customization Instructions -------
 Search REQUIRED UPDATE and enter information:
 1) REQUIRED UPDATE [1 of 3]: Enter Amazon DSP Campaign Groupings: Define the group of Amazon DSP campaigns by campaign type.
 2) REQUIRED UPDATE [2 of 3]: Enter Amazon DSP Campaign IDs: Insert campaign ids of defined Amazon DSP campaigns above.
 3) REQUIRED UPDATE [3 of 3]: Enter the branded search keyword. 
 */

WITH campaign_group (campaign_id_string, campaign_group) AS (
  /* REQUIRED UPDATE [1 of 3]: Enter Amazon DSP Campaign Groupings: Define the group of Amazon DSP campaigns by campaign type. */
  VALUES
    ('910000000000000000', 'OLV'),
    ('910000000000000000', 'DSP')
),

dsp_campaigns (campaign_id_string) AS (
  /* REQUIRED UPDATE [2 of 3]: Enter Amazon DSP Campaign IDs: Insert campaign ids of defined Amazon DSP campaigns above. */
  VALUES
    ('910000000000000000'),
    ('910000000000000000')
),

-- Grab Amazon DSP and sponsored ads Impressions
impressions AS (
  SELECT
    COALESCE(campaign_group, 'Amazon DSP') AS campaign_group
   ,'DSP' AS product_type
   , user_id
   , impression_dt_utc AS impression_dt
   , impressions AS impressions
  FROM
    dsp_impressions i
  INNER JOIN
     campaign_group G ON g.campaign_id_string = i.campaign_id_string
  WHERE
    user_id IS NOT NULL
  UNION ALL
   SELECT
     ad_product_type AS campaign_group
    ,'sponsored ads' AS product_type
    , user_id
    , event_dt_utc AS impression_dt
    , impressions AS impressions
   FROM
     sponsored_ads_traffic i
  WHERE
    user_id IS NOT NULL
),

-- Grab First Branded Searches
searches AS (
  SELECT
    user_id,
    MIN(event_dt_utc) as first_search
  FROM
    conversions_all
  WHERE
    user_id IS NOT NULL
  AND
     event_subtype = 'searchConversion'
  AND
     conversions > 0
  AND
   /* REQUIRED UPDATE [3 of 3]: Enter Keywords */
     (tracked_item SIMILAR TO '(?i)brand')
  GROUP BY
     1
),

-- Grab First Purchase event
converted AS (
  SELECT
   user_id,
   MIN(event_dt_utc) as first_purchase_dt
  FROM
   conversions_all
  WHERE
   user_id IS NOT NULL
  AND
   event_type_description = 'Product purchased'
  AND
    event_category = 'purchase'
  AND
    conversions > 0
  GROUP BY
    1
),

-- Determine first search prior to first purchase
searches_combined AS (
   SELECT
         s.user_id AS search_id
       , s.first_search
       , c.user_id AS conversion_id
       , c.first_purchase_dt
       , CASE WHEN c.user_id IS NULL THEN 'No Purchase'
         ELSE 'Purchase'
         END as customer_type
    FROM
         searches s
    LEFT JOIN
         converted c ON s.user_id = c.user_id
    WHERE
        (c.user_id IS NOT NULL AND s.first_search < c.first_purchase_dt)
    OR
        c.user_id IS NULL
),

-- Determine days between first search and purchase
search_days AS (
     SELECT
         search_id
       , first_search
       , first_purchase_dt
       , customer_type
       , seconds_between(first_search,first_purchase_dt) AS days_diff_search
     FROM
         searches_combined
    WHERE
         customer_type = 'Purchase'
),

-- Search Stat: number of users who purchased --
Search_stat AS (
   SELECT
     customer_type
    , COUNT(search_id) as Shoppers
   FROM
     searches_combined
   GROUP BY
     1
),

-- Search Stat_2: days between purchase. --
Search_stat_2 AS (
   SELECT
     customer_type
    , SUM(days_diff_search) / 86400 / COUNT(search_id) as days_between_purchase
   FROM
     search_days
   GROUP BY
     1
),

-- Determine impressions and searches before purchase for purchased customers
impressions_combined AS (
   SELECT
         a.search_id
       , a.customer_type
       , a.first_search
       , a.first_purchase_dt
       , b.user_id AS impression_id
       , b.impression_dt
       , b.campaign_group
       , COALESCE(b.impressions,0) as impressions
    FROM
         searches_combined a
    LEFT JOIN
         impressions b ON a.search_id = b.user_id
    WHERE
         a.customer_type = 'Purchase'
    AND
         b.impression_dt BETWEEN a.first_search AND a.first_purchase_dt
),

-- Determine impressions and searches before purchase for those customers who did not purchase
impressions_combined_no_purchase AS (
   SELECT
         a.search_id
       , a.customer_type
       , a.first_search
       , a.first_purchase_dt
       , b.user_id AS impression_id
       , b.impression_dt
       , b.campaign_group
       , COALESCE(b.impressions,0) as impressions
    FROM
         searches_combined a
    LEFT JOIN
         impressions b ON a.search_id = b.user_id
    WHERE
         a.customer_type = 'No Purchase'
    AND
         b.impression_dt > a.first_search
),

-- Deduplicate impressions table - total
impressions_deduplicate_total AS (
    SELECT 
        impression_id
      , customer_type
      , COUNT(DISTINCT(campaign_group)) AS campaign_groups_exposed
      , SUM(impressions) AS total_impressions
   FROM
     impressions_combined
   GROUP BY
     1,2
  UNION ALL
  SELECT 
        impression_id
      , customer_type
      , COUNT(DISTINCT(campaign_group)) AS campaign_groups_exposed
      , SUM(impressions) AS total_impressions
   FROM
     impressions_combined_no_purchase
   GROUP BY
     1,2
),

-- exposure stats overall --
exposure_stats AS (
   SELECT
      customer_type
    , SUM(total_impressions) / COUNT(impression_id) AS impressions_per_user
    , SUM(campaign_groups_exposed) / COUNT(impression_id) AS exposure_groups_per_user
   FROM
      impressions_deduplicate_total
   GROUP BY
    1
),

-- Deduplicate impressions table by campaign group
impressions_deduplicate_campaign AS (
    SELECT 
        impression_id
      , customer_type
      , campaign_group
      , SUM(impressions) AS total_impressions
   FROM
     impressions_combined
   GROUP BY
     1,2,3
  UNION ALL
  SELECT 
        impression_id
      , customer_type
      , campaign_group
      , SUM(impressions) AS total_impressions
   FROM
     impressions_combined_no_purchase
   GROUP BY
     1,2,3
),

-- Deduplicate impressions table to determine impressions by campaign_group
impressions_deduplicate_campaign_new AS (
    SELECT 
    customer_type
    , campaign_group
    , SUM(total_impressions) as impressions
    FROM
     impressions_deduplicate_campaign i
   GROUP BY
     1,2
),

-- Deduplicate impressions table to determine impressions by customer_type
impressions_deduplicate_customer_type AS (
    SELECT 
    customer_type
    , COUNT(DISTINCT(impression_id)) as user_count
    FROM
     impressions_deduplicate_campaign i
   GROUP BY
     1
),

-- Combine deduped campaign and customer_type tables
impressions_deduplicate_combined AS (
    SELECT 
    a.customer_type
    , a.campaign_group
    , a.impressions
    , b.user_count
    FROM
     impressions_deduplicate_campaign_new a
    LEFT JOIN  
    impressions_deduplicate_customer_type b ON a.customer_type = b.customer_type
),

-- exposure stats by campaign_group --
exposure_stats_campaign AS (
    SELECT
    customer_type
     , campaign_group
     , user_count
     , impressions
     , impressions/user_count as impressions_user
   FROM 
    impressions_deduplicate_combined
),

--- Combine all stat tables into one final table ---
combined_total_stats AS (
   SELECT
      'total_users' as key_metric
      , customer_type
      , 'NA' campaign_group
      , shoppers as stats
   FROM
     search_stat
   UNION ALL
   SELECT
      'days_to_purchase_per_user' as key_metric
      , customer_type
      , 'NA' campaign_group
      , days_between_purchase as stats
   FROM
     search_stat_2
   UNION ALL
   SELECT
      'total_impressions_per_user' as key_metric
      , customer_type
      , 'NA' campaign_group
      , impressions_per_user as stats
   FROM
     exposure_stats
   UNION ALL
   SELECT
      'total_ad_product_exposure_per_user' as key_metric
      , customer_type
      , 'NA' campaign_group
      , exposure_groups_per_user as stats
   FROM
     exposure_stats
   UNION ALL
      SELECT
      'impressions_per_user_per_ad_product' as key_metric 
      , customer_type    
      , campaign_group
      , impressions_user as stats
   FROM
     exposure_stats_campaign
)
  
     SELECT
     key_metric
     , customer_type
     , campaign_group
     , stats
   FROM
     combined_total_stats
```

### Query template: Compare purchase path from first detail page view

Run this query to evaluate the differences between customers who purchased after making a detail page view compared to those that did not purchase.

Note that this query works for Amazon DSP campaigns, sponsored ads campaigns, or both. If you have Amazon DSP campaigns, define the campaign groupings and the campaign IDs identified in the exploratory query.


```
-- Instructional Query: Compare purchase path of converters versus non-converters: First detail page view -- 
-- AD PRODUCTS: Amazon DSP and/or sponsored ads
-- Enrollment (trial or subscription) to Flexible Shopping Insights (FSI) as a part of Amazon Insights
/*
 ------- Customization Instructions -------
 1) REQUIRED UPDATE [1 of 2]: Enter Amazon DSP Campaign Groupings: Define the group of Amazon DSP campaigns by campaign type.
 2) REQUIRED UPDATE [2 of 2]: Enter Amazon DSP Campaign IDs: Insert campaign ids of defined Amazon DSP campaigns above
 3) NOTE: You do not have to enter sponsored ads campaigns; this query will grab data for all of them.
 */
 
 WITH campaign_group (campaign_id_string, campaign_group) AS (
  /* REQUIRED UPDATE [1 of 2]: Enter Amazon DSP Campaign Groupings: Define the group of Amazon DSP campaigns by campaign type. */
  VALUES
    ('910000000000000000', 'OLV'),
    ('910000000000000000', 'DSP')
),

dsp_campaigns (campaign_id_string) AS (
  /* REQUIRED UPDATE [2 of 2]: Enter Amazon DSP Campaign IDs: Insert campaign ids of defined Amazon DSP campaigns above. */
  VALUES
    ('910000000000000000'),
    ('910000000000000000')
),

-- Grab Amazon DSP and sponsored ads Impressions
impressions AS (
  SELECT
    COALESCE(campaign_group, 'Amazon DSP') AS campaign_group
   ,'DSP' AS product_type
   , user_id
   , impression_dt_utc AS impression_dt
   , impressions AS impressions
  FROM 
    dsp_impressions i 
  INNER JOIN 
     campaign_group G ON g.campaign_id_string = i.campaign_id_string
  WHERE 
    user_id IS NOT NULL
  UNION ALL
   SELECT
     ad_product_type AS campaign_group
    ,'sponsored ads' AS product_type
    , user_id
    , event_dt_utc AS impression_dt
    , impressions AS impressions
   FROM 
     sponsored_ads_traffic  
   WHERE 
     user_id IS NOT NULL
   AND
     ad_product_type IS NOT NULL 
),

-- Determine purchases. dedupe conversions : 1 row per user.
converted AS (    
   SELECT 
      user_id
    , MIN(event_dt_utc) AS first_purchase_dt
    , SUM(total_product_sales) AS total_product_sales
   FROM 
     conversions_all c
   WHERE 
     event_type_description in ('Product purchased') 
   AND 
     conversions > 0
   GROUP BY 
     1
),

-- Determine all DPVs. Dedupe conversions : 1 row per user.
DPV AS (    
   SELECT 
      user_id
    , tracked_item
    , event_dt_utc AS conversion_dt
    , event_type_description
    , 1 AS conversion    
   FROM
    conversions_all
   WHERE 
    event_subtype in ('detailPageView') 
   AND 
     tracked_item IS NOT NULL
   AND
    conversions > 0
),

-- Determine first_DPV
first_DPV AS (    
   SELECT 
      user_id
    , MIN(conversion_dt) as first_dpv_dt
   FROM 
     dpv
   GROUP BY
     1
),

-- Determine DPV before purchase 
DPV_combined_first AS (
   SELECT
         a.user_id
       , a.first_dpv_dt
       , b.first_purchase_dt        
       , b.user_id AS conv_id
       , CASE WHEN b.user_id IS NULL THEN 'No Purchase'
         ELSE 'Purchase'
         END as customer_type
       , seconds_between(first_dpv_dt,first_purchase_dt) AS days_diff_dpv
    FROM 
         first_dpv a
    LEFT JOIN
         converted b ON a.user_id = b.user_id
),

-- Determine days between first impression to purchase --
days_to_purchase AS (
    SELECT
      customer_type
    , SUM(days_diff_dpv) / 86400 / COUNT(user_id) AS days_to_purchase
    FROM
      DPV_combined_first 
    GROUP BY
       1
),

-- Determine all DPVs between purchase
DPV_combined_purchase AS (
    SELECT
         a.user_id
       , a.first_dpv_dt
       , a.customer_type
       , a.first_purchase_dt               
       , b.tracked_item
       , b.conversion
       , b.conversion_dt
    FROM 
       DPV_combined_first a
    LEFT JOIN
       DPV b ON a.user_id = b.user_id
    WHERE
         a.customer_type = 'Purchase'
    AND
        b.conversion_dt BETWEEN a.first_dpv_dt AND first_purchase_dt
),

-- Determine all DPVs between for non purchasers
DPV_combined_no_purchase AS (
SELECT
         a.user_id
       , a.first_dpv_dt
       , a.customer_type
       , a.first_purchase_dt               
       , b.tracked_item
       , b.conversion
       , b.conversion_dt
    FROM 
       DPV_combined_first a
    LEFT JOIN
       DPV b ON a.user_id = b.user_id
    WHERE
         a.customer_type = 'No Purchase'
    AND
        b.conversion_dt > a.first_dpv_dt
),

-- DPV deduplicate
DPV_deduplicate AS (
    SELECT 
       user_id
     , customer_type
     , SUM(conversion) AS dpvs
   FROM
     DPV_combined_purchase
   GROUP BY
     1,2
  UNION ALL
  SELECT 
       user_id
     , customer_type
     , SUM(conversion) AS dpvs
   FROM
     DPV_combined_no_purchase
   GROUP BY
     1,2
),

-- DPV stats --
DPV_stats AS (
   SELECT
       customer_type
     , SUM(dpvs) / COUNT(user_id) AS dpvs_per_user
   FROM 
     DPV_deduplicate
   GROUP BY
     1
),
    
--DPV products viewed
DPV_products_deduplicate AS (
   SELECT 
      user_id
     , customer_type
     , COUNT(distinct(tracked_item)) AS products_viewed
    FROM 
       DPV_combined_purchase
    WHERE 
        tracked_item IS NOT NULL
    GROUP BY 
        1,2
   UNION ALL
  SELECT 
      user_id
     , customer_type
     , COUNT(distinct(tracked_item)) AS products_viewed
    FROM 
       DPV_combined_no_purchase
    WHERE 
        tracked_item IS NOT NULL
    GROUP BY 
        1,2
),

-- DPVv2: products viewed per user stats --
DPV_stats_2 AS (
   SELECT
     customer_type
    , SUM(products_viewed) / COUNT(user_id) AS products_viewed_per_user
   FROM 
     DPV_products_deduplicate
   GROUP BY
     1
),

-- Determine Impressions prior to purchase
impressions_combined AS (
   SELECT
         c.first_dpv_dt
       , c.first_purchase_dt        
       , c.customer_type
       , i.user_id AS impression_id         
       , i.impression_dt
       , i.campaign_group
       , i.impressions
    FROM 
         DPV_combined_first c 
    LEFT JOIN
         impressions i ON c.user_id = i.user_id       
    WHERE
        (i.user_id IS NOT NULL AND i.impression_dt BETWEEN first_dpv_dt AND c.first_purchase_dt) 
    OR
        (i.user_id IS NOT NULL AND c.first_purchase_dt IS NULL)   
    OR        
        i.user_id IS NULL
),

-- Deduplicate impressions table to determine impressions by customer_type and number of campaign groups exposed
impressions_deduplicate AS (
    SELECT 
        i.impression_id
      , customer_type
      , COUNT(DISTINCT(i.campaign_group)) AS campaign_groups_exposed
      , SUM(i.impressions) AS total_impressions
   FROM
     impressions_combined i
   GROUP BY
     1,2
),

-- exposure stats overall --
exposure_stats AS (
   SELECT
      customer_type
    , SUM(total_impressions) / COUNT(impression_id) AS impressions_per_user
    , SUM(campaign_groups_exposed) / COUNT(impression_id) AS exposure_groups_per_user
   FROM
      impressions_deduplicate 
   GROUP BY
    1
),

-- Deduplicate impressions table to determine impressions by campaign_group
impressions_deduplicate_campaign AS (
    SELECT 
    customer_type
    , campaign_group
    , SUM(impressions) as impressions
    FROM
     impressions_combined i
   GROUP BY
     1,2
),

-- Deduplicate impressions table to determine impressions by customer_type
impressions_deduplicate_customer_type AS (
    SELECT 
    customer_type
    , COUNT(DISTINCT(impression_id)) as user_count
    FROM
     impressions_combined i
   GROUP BY
     1
),

-- Combined deduped campaign and customer_type tables
impressions_deduplicate_combined AS (
    SELECT 
    a.customer_type
    , a.campaign_group
    , a.impressions
    , b.user_count
    FROM
     impressions_deduplicate_campaign a
    LEFT JOIN  
    impressions_deduplicate_customer_type b ON a.customer_type = b.customer_type
),

-- exposure stats by campaign_group --
exposure_stats_campaign AS (
    SELECT
    customer_type
     , campaign_group
     , user_count
     , impressions
     , impressions/user_count as impressions_user
   FROM 
    impressions_deduplicate_combined
),

--- Combine all stat tables into one final table ---
combined_total_stats AS (
   SELECT
      'days_to_purchase_per_user' as key_metric
      , customer_type     
      , 'NA' campaign_group
      , days_to_purchase as stats
   FROM
     days_to_purchase
   UNION ALL
   SELECT
      'ad_products_exposed_per_user' as key_metric 
      , customer_type    
      , 'NA' campaign_group
      , exposure_groups_per_user as stats
   FROM
     exposure_stats 
   UNION ALL
   SELECT
      'total_impressions_per_user' as key_metric 
      , customer_type    
      , 'NA' campaign_group
      , impressions_per_user as stats
   FROM
     exposure_stats 
 UNION ALL
   SELECT
      'impressions_per_user_per_ad_product' as key_metric 
      , customer_type    
      , campaign_group
      , impressions_user as stats
   FROM
     exposure_stats_campaign
 UNION ALL   
   SELECT
      'dpvs_per_user' as key_metric     
      , customer_type
      , 'NA' campaign_group
      , dpvs_per_user as stats
   FROM
     dpv_stats 
   UNION ALL
   SELECT
      'products_viewed_per_user' as key_metric     
      , customer_type
      , 'NA' campaign_group
      , products_viewed_per_user as stats
   FROM
     dpv_stats_2     
)
     SELECT
     key_metric
     , customer_type
     , campaign_group
     , stats
   FROM
     combined_total_stats 
```
