## Metadata

**id:** 99848f9f4a2aa1d0ef07d6e15720c129e737dcfa329ae54de2589b07c4724e48
**collection:** None
**state:** FEATURED
**deprecation_metadata:** None
**description:** Calculate ad reach and frequency over a certain period of time.
**tags:** ['Template analytics']
**use_case_categories:** ['Reach and frequency']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 
### Purpose 
Use this instructional query to learn how to use AMC to calculate reach, the number of users who were served an impression, and impression frequency, the number of times an ad is served to the same users. By analyzing reach and frequency data for your campaigns, you can better understand how many people were shown your ads and how frequently the same people were shown them over a certain period of time.

Frequency can be defined over any time period of your choosing: frequency over lifespan of a campaign, over multiple campaigns, over a month, or over a week, and so on. This definition will impact how you interpret results later. If you are trying to compare frequency distributions of multiple campaigns, please note that tactics may have a considerable impact on distributions because campaigns might feature different products or have unique goals.

The following templates are provided:
1) Impression reach and frequency for your Amazon DSP campaigns only
2) Impression reach and frequency for your sponsored ads campaigns only (Sponsored Products, Sponsored Brands, Sponsored Display, and/or Sponsored Television) 
3) Omni-channel impression reach and frequency, which pulls impression frequency for campaigns across your desired Amazon channels (Amazon DSP, Sponsored Products, Sponsored Brands, Sponsored Display, and/or Sponsored Television).


If you are interested in measuring optimal impression frequency for Amazon DSP by various conversion metrics, such as purchases, units_sold, product_sales, add_to_cart, and detail_page_views, refer to another instructional query on [DSP Impression Frequency and Conversions](2b13b611c86b51a98ea3027822557df0b8a1a994272e2173a3b426214cf8f714). 

### Requirements
To run the :
- Sponsored ads only query, advertisers should have data for sponsored ads (Sponsored Products, Sponsored Brands, Sponsored Display, and/or Sponsored Television) campaigns.
- Amazon DSP only query, advertisers need to have campaigns delivered with Amazon DSP.
- Omni-channel query, advertisers should have data for both Amazon DSP and sponsored ads (Sponsored Products, Sponsored Brands, Sponsored Display, and/or Sponsored Television) campaigns in order to understand de-duped reach and frequency across campaigns.

Note that, in order to use these query templates, these campaigns do not necessarily need to be measuring or optimizing towards the same conversion event, and again, conversion events can include either on- and off-Amazon events.



## Template

### DSP reach and impression frequency

This template generates reach and impression frequency metrics for DSP campaigns only.


```
-- TEMPLATE -- Instructional Query: DSP Reach and Impression Frequency --
WITH 
impressions AS (
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    dsp_impressions
  WHERE

   (ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaigns'), campaign_id_string)
        OR CUSTOM_PARAMETER('dsp_campaigns') IS NULL
    OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaigns')) = 0)
  
   -- exclude users without a known user_id to prevent them from being grouped together.
    AND user_id IS NOT NULL
  GROUP BY
    advertiser,
    user_id
),




cte1 AS
(
SELECT

  advertiser,
  -- OPTIONAL UPDATE frequency bucket if necessary. The default maximum frequency bucket is 10, which will result in 10 frequency buckets. --
  IF(
    impressions < 10,
    CONCAT('frequency_', impressions),
    'frequency_10+'
  ) AS frequency_buckets,
  COUNT(DISTINCT user_id) AS users_in_bucket,
  SUM(impressions) AS impressions_in_bucket
FROM
  impressions
GROUP BY
  advertiser,
  frequency_buckets),
  
  cte2 as (
 SELECT

 advertiser,
 frequency_buckets,
 users_in_bucket,
 impressions_in_bucket,
 
      SUM(users_in_bucket) OVER (partition by advertiser) as sum_users_advertiser,
  
  
  CASE WHEN frequency_buckets = 'frequency_1' THEN '1' 
   WHEN frequency_buckets = 'frequency_2' THEN '2' 
   WHEN frequency_buckets = 'frequency_3' THEN '3' 
   WHEN frequency_buckets = 'frequency_4' THEN '4' 
   WHEN frequency_buckets = 'frequency_5' THEN '5' 
   WHEN frequency_buckets = 'frequency_6' THEN '6' 
   WHEN frequency_buckets = 'frequency_7' THEN '7' 
   WHEN frequency_buckets = 'frequency_8' THEN '8' 
   WHEN frequency_buckets = 'frequency_9' THEN '9' 
   WHEN frequency_buckets = 'frequency_10+' THEN '10+' 
 END AS frequency
  from cte1
  
  )
  
  SELECT
   'DSP Impression Reach and Frequency' as AMC_template_name,
CAST (BUILT_IN_PARAMETER('TIME_WINDOW_START') AS DATE) as Start_Date,
CAST (BUILT_IN_PARAMETER('TIME_WINDOW_END') AS DATE) as End_Date,
advertiser,
 frequency_buckets,
 users_in_bucket,
 impressions_in_bucket,
 ROUND ((users_in_bucket/sum_users_advertiser*100),2) AS reach_percentage,
   CONCAT (
    ROUND ((users_in_bucket/sum_users_advertiser*100),2)
     
    ,
      '% of users were exposed to the ad ',
      frequency, CASE when frequency = 1 then ' time' else
      ' times' END
    ) AS insight1
  
  from CTE2
  
```

### Omni-channel reach and impression frequency

This template generates reach and impression frequency metrics for campaigns across your desired Amazon channels (Amazon DSP, Sponsored Products, Sponsored Brands, and/or Sponsored Display).



```
-- TEMPLATE -- Instructional Query: Omni-channel Reach & Frequency --
WITH impressions_stacked AS (
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    dsp_impressions
  WHERE
    user_id IS NOT NULL
    AND    (
            ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaigns'), campaign_id_string)
            OR CUSTOM_PARAMETER('dsp_campaigns') IS NULL
            OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaigns')) = 0
    )
  GROUP BY
    1,
    2
  UNION ALL
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    sponsored_ads_traffic
  WHERE
    user_id IS NOT NULL
    AND (
            CUSTOM_PARAMETER('sa_campaigns') IS NULL
            OR CARDINALITY(CUSTOM_PARAMETER('sa_campaigns')) = 0
            OR ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaigns'), campaign_id_string)
    )
  GROUP BY
    1,
    2
),
impressions_collapsed AS (
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    impressions_stacked
  GROUP BY
    1,
    2
),
cte1 AS (
  SELECT
    advertiser,
    -- OPTIONAL UPDATE frequency bucket if necessary. The default maximum frequency bucket is 10, which will result in 10 frequency buckets. --
    IF(
      impressions < 10,
      CONCAT('frequency_', impressions),
      'frequency_10+'
    ) AS frequency_bucket,
    COUNT(DISTINCT user_id) AS users_in_bucket,
    SUM(impressions) AS impressions_in_bucket
  FROM
    impressions_collapsed
  GROUP BY
    1,
    2
),
cte2 AS (
  SELECT
    advertiser,
    frequency_bucket,
    users_in_bucket,
    impressions_in_bucket,
    SUM(users_in_bucket) OVER (PARTITION BY advertiser) AS sum_users_advertiser,
    CASE
      WHEN frequency_bucket = 'frequency_1' THEN '1'
      WHEN frequency_bucket = 'frequency_2' THEN '2'
      WHEN frequency_bucket = 'frequency_3' THEN '3'
      WHEN frequency_bucket = 'frequency_4' THEN '4'
      WHEN frequency_bucket = 'frequency_5' THEN '5'
      WHEN frequency_bucket = 'frequency_6' THEN '6'
      WHEN frequency_bucket = 'frequency_7' THEN '7'
      WHEN frequency_bucket = 'frequency_8' THEN '8'
      WHEN frequency_bucket = 'frequency_9' THEN '9'
      WHEN frequency_bucket = 'frequency_10+' THEN '10+'
    END AS frequency
  FROM
    cte1
)
SELECT
  'Omni-channel Impression Reach and Frequency' AS AMC_template_name,
  CAST (BUILT_IN_PARAMETER('TIME_WINDOW_START') AS DATE) AS Start_Date,
  CAST (BUILT_IN_PARAMETER('TIME_WINDOW_END') AS DATE) AS End_Date,
  advertiser,
  frequency_bucket,
  users_in_bucket,
  impressions_in_bucket,
  ROUND ((users_in_bucket / sum_users_advertiser * 100), 2) AS reach_percentage,
  CONCAT (
    ROUND ((users_in_bucket / sum_users_advertiser * 100), 2),
    '% of users were exposed to the ad ',
    frequency,
    CASE
      WHEN frequency = 1 THEN ' time'
      ELSE ' times'
    END
  ) AS insight1
FROM
  CTE2
```

## Data Query Instructions 
### Tables Used 
- [**dsp_impressions**](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions) : used to gather Amazon DSP impression data for the DSP-only version of the reach and frequency IQ

- [**sponsored_ads_traffic**](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): used to gather sponsored ads impression data for the sponsored ads-only version of the reach and frequency IQ.

**Note**: Both dsp_impressions and sponsored_ads_traffic tables are used together for the Omni-channel query.



## Data Interpretation Instructions 
### Example Query Results 
This data is for instructional purposes only. Your results will differ.

| advertiser       | frequency\_buckets | users\_in\_bucket | impressions\_in\_bucket |
| ---------------- | ------------------ | ----------------- | ----------------------- |
| Accent Athletics | frequency\_1       | 85234             | 85234                   |
| Accent Athletics | frequency\_2       | 23459             | 46918                   |
| Accent Athletics | frequency\_3       | 12345             | 37035                   |
| Accent Athletics | frequency\_4       | 8764              | 35056                   |
| Accent Athletics | frequency\_5       | 2364              | 11820                   |
| Accent Athletics | frequency\_6       | 1230              | 7380                    |
| Accent Athletics | frequency\_7       | 987               | 6909                    |
| Accent Athletics | frequency\_8       | 789               | 6312                    |
| Accent Athletics | frequency\_9       | 458               | 4122                    |
| Accent Athletics | frequency\_10      | 349               | 2792                    |
| Accent Athletics | frequency\_10+     | 609               | 5481                    |


## Data Interpretation Instructions 
### Example Query Results 
This data is for instructional purposes only. Your results will differ.

|**amc_template_name**|**start_date**|**end_date**|**advertiser**|**frequency_buckets**|**users_in_bucket**|**impressions_in_bucket**|**reach_percentage**|**insight**|
|---|---|---|---|---|---|---|---|---|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_1|85234|85234|62.40|62.4 of the users were exposed to the ad 1 time|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_2|23459|46918|17.18|17.18 of the users were exposed to the ad 2 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_3|12345|37035|9.04|9.04 of the users were exposed to the ad 3 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_4|8764|35056|6.42|6.42 of the users were exposed to the ad 4 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_5|2364|11820|1.73|1.73 of the users were exposed to the ad 5 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_6|1230|7380|0.90|0.9 of the users were exposed to the ad 6 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_7|987|6909|0.72|0.72 of the users were exposed to the ad 7 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_8|789|6312|0.58|0.58 of the users were exposed to the ad 8 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_9|458|4122|0.34|0.34 of the users were exposed to the ad 9 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_10|349|2792|0.26|0.26 of the users were exposed to the ad 10 times|
|Omni-channel Impression Reach and Frequency|7/9/23|7/16/23|Accent Athletics|frequency_10+|609|5481|0.45|0.45 of the users were exposed to the ad 10+ times|


### Metrics Defined 

| column name             | definition                                                                                                                                                                                     |
| :----------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| frequency\_bucket       | Frequencies are grouped into different bucket from 1 - 10+ default. Frequency\_01 means users were exposed to an ad 1 time. Frequency\_10+ means users were exposed to an ad 10 or more times. |
| users\_in\_bucket       | Number of distinct users in the frequency bucket.                                                                                                                                              |
| impressions\_in\_bucket | Number of impressions in the frequency bucket.                                                                                                                                                 |


### Insights and Data Interpretations 

- What percentage of users were exposed to different frequency buckets? 

The following insight uses the frequency_buckets and users_in_bucket from the raw data. First, create a Total row to calculate the total number of users in all buckets. Then use the total users in each frequency bucket as the numerator and the total users as the denominator to calculate the percentage of users. See an example below. This data shows around 90% of users were exposed your ad 3 or fewer times. 

Adding conversion metrics, such as purchases and purchase rate, will make this insight more meaningful (See [DSP Impression Frequency and Conversions](2b13b611c86b51a98ea3027822557df0b8a1a994272e2173a3b426214cf8f714)). For example, if the frequency for the optimal conversion rate is 8, then you should increase the impression frequency cap to increase the frequency rate.  


| frequency\_buckets | users\_in\_bucket | percentage of users |
| ------------------ | ----------------- | ------------------- |
| frequency\_1       | 85234             | 62%                 |
| frequency\_2       | 23459             | 17%                 |
| frequency\_3       | 12345             | 9%                  |
| frequency\_4       | 8764              | 6%                  |
| frequency\_5       | 2364              | 2%                  |
| frequency\_6       | 1230              | 1%                  |
| frequency\_7       | 987               | 1%                  |
| frequency\_8       | 789               | 1%                  |
| frequency\_9       | 458               | 0%                  |
| frequency\_10      | 349               | 0%                  |
| frequency\_10+     | 609               | 0%                  |
| Total              | 136588            | 100%                |


- What was the average impression frequency per user? 

This insight uses the users_in_bucket (reach) and impressions_in_bucket columns. SUM up both metrics and divide the total impressions (numerator) by the total users (denominator) to get the average impression frequency per user: 1.8 = 249059 / 136588.   


- How many unique users have I reached across all of my Amazon media?

Across all of my Amazon campaigns (Amazon DSP, Sponsored Products, and Sponsored Display) I have reached ~3.5 million unique users over the time period of my query.



## Queries
### Exploratory Query
Use the query below to identify the campaign filters to add to the query template. 


```
-- Instructional Query: Exploratory Query to identify campaigns and campaign_ids for Sponsored Ads and DSP respectively --
SELECT
'DSP' as ad_product_type,
  advertiser,
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6
  
UNION ALL

SELECT
ad_product_type,
  advertiser,
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  SUM(impressions) AS impressions
FROM
  sponsored_ads_traffic
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6
```

```
-- TEMPLATE -- Exploratory Query to identify campaigns and campaign_ids for Sponsored Ads and DSP respectively.
SELECT
CASE
  WHEN ad_product_type IS NULL THEN 'DSP'
    ELSE ad_product_type
  END AS ad_product_type,
  advertiser,
  CONCAT('campaign_id: ', campaign_id_string) AS campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  SUM(impressions) AS impressions
FROM
  amazon_attributed_events_by_traffic_time
WHERE
  ARRAY_CONTAINS(
    CUSTOM_PARAMETER('ad_product_type'),
    COALESCE(ad_product_type, 'DSP')
  )
  AND (
    (CUSTOM_PARAMETER('tracked_asin') IS NULL)
    OR CARDINALITY(CUSTOM_PARAMETER('tracked_asin')) = 0
    OR ARRAY_CONTAINS(CUSTOM_PARAMETER('tracked_asin'), tracked_asin)
  )
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6
```

### Query template: Sponsored ads reach and impression frequency
Search for 'UPDATE' to find the query locations that must be updated. 
- First, update the campaign_id(s) and remove the placeholder campaign_id values in the custom parameter section. 
- Second, the default maximum frequency bucket is 10, which will result in 10 frequency buckets. The 10th bucket will represent users who were exposed to an ad 10 or more times. You may adjust the bin number depending on your campaign. For example, 80% of users may be in frequency bin 1, and 95% of users in frequency bins less than 8. In this case, you may want to limit your analysis to only looking at the first 8 bins. 


```
-- Instructional Query: Sponsored ads reach and impression frequency -- 
-- AD PRODUCTS: Sponsored ads
/*
 ------- Customization Instructions -------
 1) Run the exploratory query first to identify campaign ID string values to filter the results
 2) REQUIRED UPDATE [1 OF 1]: Update the sa_campaigns custom parameter with the sponsored ads campaign ID string values. Delete any unneeded placeholder values.
 3) OPTIONAL UPDATE [1 of 1]: Increase or decrease the maximum frequency buckets  
 */

WITH impressions AS (
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    sponsored_ads_traffic
  WHERE
    ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaigns'), campaign_id_string) -- exclude users without a known user_id to prevent them from being grouped together.
    AND user_id IS NOT NULL
  GROUP BY
    advertiser,
    user_id
)
SELECT
  advertiser,
  -- OPTIONAL UPDATE frequency bucket if necessary. The default maximum frequency bucket is 10, which will result in 10 frequency buckets. --
  IF(
    impressions < 10,
    CONCAT('frequency_', impressions),
    'frequency_10+'
  ) AS frequency_buckets,
  COUNT(DISTINCT user_id) AS users_in_bucket,
  SUM(impressions) AS impressions_in_bucket
FROM
  impressions
GROUP BY
  advertiser,
  frequency_buckets
```

### Query template: Amazon DSP reach and impression frequency
Search for 'UPDATE' to find the query locations that must be updated. 
- First, update the campaign ID string values and remove the placeholder campaign ID string values in the custom parameter section.
- Second, the default maximum frequency bucket is 10, which will result in 10 frequency buckets. The 10th bucket will represent users who were exposed to an ad 10 or more times. You may adjust the bin number depending on your campaign. For example, 80% of users may be in frequency bin 1, and 95% of users in frequency bins less than 8. In this case, you may want to limit your analysis to only looking at the first 8 bins.


```
-- Instructional Query: Amazon DSP Reach and Impression Frequency --
-- AD PRODUCTS: Amazon DSP
/*
 ------- Customization Instructions -------
 1) Run the exploratory query first to identify campaign ID string values to filter the results
 2) REQUIRED UPDATE [1 OF 1]: Update the dsp_campaigns custom parameter with the Amazon DSP campaign ID string values. Delete any unneeded placeholder values.
 3) OPTIONAL UPDATE [1 of 1]: Increase or decrease the maximum frequency buckets  
 */
WITH impressions AS (
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    dsp_impressions
  WHERE
    ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaigns'), campaign_id_string) -- exclude users without a known user_id to prevent them from being grouped together.
    AND user_id IS NOT NULL
  GROUP BY
    advertiser,
    user_id
)
SELECT
  advertiser,
  -- OPTIONAL UPDATE frequency bucket if necessary. The default maximum frequency bucket is 10, which will result in 10 frequency buckets. --
  IF(
    impressions < 10,
    CONCAT('frequency_', impressions),
    'frequency_10+'
  ) AS frequency_buckets,
  COUNT(DISTINCT user_id) AS users_in_bucket,
  SUM(impressions) AS impressions_in_bucket
FROM
  impressions
GROUP BY
  advertiser,
  frequency_buckets
```

### Query template: Omni-channel reach and impression frequency for Amazon DSP and sponsored ads
- First, update the campaign ID string values and remove the placeholder campaign ID string values in the custom parameter section. 
- Second, the default maximum frequency bucket is 10, which will result in 10 frequency buckets. The 10th bucket will represent users who were exposed to an ad 10 or more times. You may adjust the bin number depending on your campaign. For example, 80% of users may be in frequency bin 1, and 95% of users in frequency bins less than 8. In this case, you may want to limit your analysis to only looking at the first 8 bins.


```
-- Instructional Query: Omni-channel Reach & Frequency -- 
-- AD PRODUCTS: Sponsored ads and/or Amazon DSP
/*
 ------- Customization Instructions -------
 1) Run the exploratory query first to identify campaign ID string values to filter the results
 2) REQUIRED UPDATE [1 OF 1]: Update the dsp_campaigns and sa_campaigns custom parameter with the Amazon DSP and sponsored ads campaign ID string values. Delete any unneeded placeholder values.
 3) OPTIONAL UPDATE [1 of 1]: Increase or decrease the maximum frequency buckets  
 */

WITH impressions_stacked AS (
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    dsp_impressions
  WHERE
    user_id IS NOT NULL
    AND ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaigns'), campaign_id_string)
  GROUP BY
    1,
    2
  UNION ALL
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    sponsored_ads_traffic
  WHERE
    user_id IS NOT NULL
    AND ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaigns'), campaign_id_string)
  GROUP BY
    1,
    2
),
impressions_collapsed AS (
  SELECT
    advertiser,
    user_id,
    SUM(impressions) AS impressions
  FROM
    impressions_stacked
  GROUP BY
    1,
    2
)
SELECT
  advertiser,
  -- OPTIONAL UPDATE frequency bucket if necessary. The default maximum frequency bucket is 10, which will result in 10 frequency buckets. --
  IF(
    impressions < 10,
    CONCAT('frequency_', impressions),
    'frequency_10+'
  ) AS frequency_bucket,
  COUNT(DISTINCT user_id) AS users_in_bucket,
  SUM(impressions) AS impressions_in_bucket
FROM
  impressions_collapsed
GROUP BY
  1,
  2
```
