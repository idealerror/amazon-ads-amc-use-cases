## Metadata

**id:** ac5fe71bb9bcd3178b4a469f36c10d5ac7d64b441bdb09064f9f5835195d67fb
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Understand the number of net-new users who were reached by your campaigns, and which groups of ads are the most effective at expanding the brand's reach (FSI required).
**tags:** ['Paid features']
**use_case_categories:** ['New-to-brand', 'Performance deep dive', 'Reach and frequency']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose 

Net-new reach is a way to measure the number of new users that were reached by your campaigns. Specifically, it is the number of unique users who were exposed to campaigns and had not previously viewed any of the brand’s detail pages in the past several months prior to ad exposure. It measures the ability to reach new potential customers, rather than re-engaging existing or recent customers. This metric is particularly valuable for brands with longer purchase cycles where standard new-to-brand metrics (typically using 1-year lookback windows) may not accurately reflect customer behavior.

### How do these users differ from new-to-brand customers?

Net-new reach refers to the total number of completely new customers reached by campaigns, meaning people who have not interacted with the brand within the past several months, while new-to-brand customers are individuals who may have been reached by campaigns but haven't purchased from them within the past year. 

### Requirements

* **Enrollment (trial or subscription) to Flexible Shopping Insights (FSI) as a part of Amazon Insights**: Paid Features are only available in certain regions.

## Data query instructions 

### Data returned

This query will return the number of net-new users reached by the campaign, as well as the corresponding clicks and sales for that net-new audience. In addition to exploratory queries, there are two main query templates: one for Amazon DSP campaigns and one for sponsored ads campaigns. 

**Note:** If you want to compare net-new reach of different ad products (for example, Amazon DSP vs sponsored ads vs Prime Video ads), you must run the query for each groups of ads. 

### Tables used 

* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This table is used to identify the impressions and clicks from sponsored ads campaigns.
* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): This table is used to identify the impressions from Amazon DSP campaigns.
* [dsp_clicks](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_clicks): This table is used to identify the clicks from Amazon DSP campaigns.  
* [amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): This table is used to identify ad-attributed conversion events.  
* [conversions_all](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/conversions_all_paid): This table contains both ad-exposed and non-ad-exposed conversions for tracked ASINs. This table is used to identify users who had a detail page view before ad exposure.

## Data interpretation instructions 

### Example query results

**This data is for instructional purposes only. Your results will differ.**

| net_new_reach | net_new_impressions | net_new_clicks | net_new_sales | net_new_users_who_clicked | net_new_users_who_purchased | total_impressions | total_reach |
|---------------|---------------------|----------------|---------------|---------------------------|-----------------------------|-------------------|-------------|
| 2122976       | 7626799             | 12530          | 155100.59     | 12034                     | 6585                        | 8166112           | 2339989     |

### Metrics defined

| metric                      | definition                                                                                                                                                         |
|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `net_new_reach`               | The number of unique users who were exposed to the campaign and had not viewed any of the brand's product detail pages in the 6 months prior to their ad exposure. |
| `net_new_impressions`         | Total impressions delivered to the net-new users by the campaign.                                                                                                  |
| `net_new_clicks`              | Total clicks from net-new users reached by the campaign.                                                                                                           |
| `net_new_sales`               | Total sales (in local currency)  from net-new users reached by the campaign.                                                                                       |
| `net_new_users_who_clicked`   | The number of unique users who were part of the net-new reach and clicked on the ads during the current campaign.                                                  |
| `net_new_users_who_purchased` | The number of unique users who were part of the net-new reach and made a purchase during the current campaign.                                                     |
| `total_impressions`           | The total number of impressions delivered by the campaign.                                                                                                         |
| `total_reach`                 | The total number of distinct users reached by the campaign.                                                                                                        |


### Insights and data interpretation

**What percent of impressions of my campaign were delivered to net-new users?**

* 93% of campaign’s impressions were delivered to net-new users. To calculate this, divide the `net_new_impressions` by `total_impressions` (7626799/8166112=93%).

**Which campaign was most effective at reaching net-new users?**

* Prime Video ads (PVa) campaigns reached more net-new users per impression than Sponsored ads and DSP Display campaigns (Please note that this conclusion requires running the queries multiple times for different groups of ads). 
* To calculate net-new users reached per impression, use `net_new_reach/total_impressions`.
* **Sample Recommendation**: Consider allocating greater budget to Prime Video ads to reach net-new users.

## Queries

### Exploratory queries

**4.1.1 Exploratory Query for Amazon DSP campaigns**

Use this exploratory query to make a decision about the Amazon DSP campaign IDs to be added in the net-new reach query template. This query also returns the campaign start date, which is required for the main query.


```
-- Instructional Query: Exploratory Query for Net-new reach (Amazon DSP campaigns)
-- AD PRODUCTS: Amazon DSP

SELECT
  advertiser,
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  sum(impressions) as impressions
FROM
  dsp_impressions
GROUP BY
  advertiser,
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date
```

**4.1.2 Exploratory Query for sponsored ads campaigns**

Use this exploratory query to make a decision about the sponsored ads campaign IDs to be added in the net-new reach query template. This query also returns the campaign start date, which is required for the main query.


```
-- Instructional Query: Exploratory Query for Net-new reach (Sponsored ads campaigns)
-- AD PRODUCTS: Sponsored ads

SELECT
  advertiser,
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  sum(impressions) as impressions
FROM
  sponsored_ads_traffic
GROUP BY
  advertiser,
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date
```

**4.1.3 Exploratory Query to check ASINs available for detail page view conversion event**

Use this exploratory query to check which ASINs exist in conversions_all table for detail page view events. 


```
-- Instructional Query: Exploratory Query for Net-New Reach - check ASINs for detail page view conversion event--
SELECT
event_type_description,
tracked_item,
SUM(conversions) AS conversions
FROM conversions_all
WHERE
conversions > 0
and event_type_description in ('Product detail page viewed')
GROUP BY event_type_description,
tracked_item 
```

### 4.2 Query template: Net-new reach for Amazon DSP campaigns

Use the following query to calculate the net-new reach for Amazon DSP campaigns.

Provide the following custom parameters:

* `campaign_ids`: A list of Amazon DSP campaign ID string values for which you want to calculate net-new reach
* `campaign_start_date`: The earliest start date for all of the campaigns you have selected
* `lookback_days`: The number of days you want the query to look back for net-new users

**Note:** Be sure to set query time window in the Query editor to the number of days (lookback_days) before the campaign start date. So, if the campaign started on 1/1/25 and the lookback window is 182 days (6 months), select 6 months before the campaign start date to ensure that you have queried all of the needed data.


```
-- Instructional Query: Net-new reach by campaign group --
-- AD PRODUCTS: Amazon DSP
-- Enrollment (trial or subscription) to Flexible Shopping Insights (FSI) or Amazon Insights --

/*
-- REQUIRED UPDATE [Custom parameters]: Add values for the custom parameters.
    -- Note that campaign_start_date is required.
    -- Add one or more campaign IDs. It is highly recommended to include at least one campaign ID.
    -- Optionally, add one or more ASINs filters.
 -- OPTIONAL UPDATE [1 OF 1]: Check that the event_type_description matches those returned in the exploratory query.
*/
    
-- gather impressions and clicks  -- 
with imp_data as(
SELECT
    user_id,
    MIN(a.impression_dt_utc) AS impression_dt_first,
    SUM(a.impressions) AS impressions
FROM dsp_impressions a
WHERE
    a.user_id IS NOT NULL
    -- campaign_ids defined via Custom Parameters  
    AND (
      ARRAY_CONTAINS(CUSTOM_PARAMETER('campaign_ids'), campaign_id_string)
      OR CUSTOM_PARAMETER('campaign_ids') IS NULL
      OR CARDINALITY(CUSTOM_PARAMETER('campaign_ids')) = 0
    )                     
    -- campaign_start_date defined via Custom Parameters  
    AND CAST(a.impression_dt_utc AS DATE) >= CUSTOM_PARAMETER('campaign_start_date')
group by user_id
), 

click_data as(
SELECT
    user_id,
    SUM(b.clicks) AS clicks
FROM dsp_clicks b
WHERE
    b.user_id IS NOT NULL
    -- campaign_ids is defined via Custom Parameters      
    AND (
      ARRAY_CONTAINS(CUSTOM_PARAMETER('campaign_ids'), campaign_id_string)
      OR CUSTOM_PARAMETER('campaign_ids') IS NULL
      OR CARDINALITY(CUSTOM_PARAMETER('campaign_ids')) = 0
    )           
    -- campaign_start_date is defined via the Custom Parameters  
    AND CAST(impression_dt_utc AS DATE) >= CUSTOM_PARAMETER('campaign_start_date')
group by user_id
),

impressions as (
SELECT
    a.user_id,
    a.impressions,
    a.impression_dt_first,
    b.clicks
FROM imp_data a 
LEFT JOIN click_data b on a.user_id = b.user_id
),

-- identify users who had detail page view --
converted AS
(
SELECT 
  user_id,
        MAX(event_dt_utc) AS conversion_event_dt_last,
        SUM(conversions) AS dpv
FROM conversions_all
WHERE 
  conversions > 0 
   AND user_id IS NOT NULL
-- OPTIONAL UPDATE [1 OF 1]: Check that event_type_description matches the value returned in the exploratory query.
   AND event_type_description in ('Product detail page viewed')
AND (
   ARRAY_CONTAINS(CUSTOM_PARAMETER('asins'), 'ALL')
   OR ARRAY_CONTAINS(CUSTOM_PARAMETER('asins'), tracked_item)
   OR CUSTOM_PARAMETER('asins') IS NULL
   OR CARDINALITY(CUSTOM_PARAMETER('asins')) = 0
 )

    
GROUP BY user_id
),


sales_conversion as (
SELECT
  user_id,
  SUM(total_product_sales) as total_product_sales
FROM
  amazon_attributed_events_by_conversion_time
WHERE
  total_product_sales > 0
  AND user_id IS NOT NULL 
    -- campaign_ids defined via Custom Parameters    
  AND (
    ARRAY_CONTAINS(CUSTOM_PARAMETER('campaign_ids'), campaign_id_string)
    OR CUSTOM_PARAMETER('campaign_ids') IS NULL
    OR CARDINALITY(CUSTOM_PARAMETER('campaign_ids')) = 0
)
GROUP BY
  user_id
),

-- Classify each user into net new or retageting pool -- 
identify_incr_reach AS 
(
SELECT 
    i.user_id as reach_user,
    b.user_id as dpv_user, 
    i.impressions as imp, 
    IF(SECONDS_BETWEEN(conversion_event_dt_last, impression_dt_first) <= (60*60*24*CUSTOM_PARAMETER('lookback_days')) AND b.conversion_event_dt_last < i.impression_dt_first AND i.impressions>0,
        1, 0) as users_in_rt_pool --6 months (182 days) lookback window. You can update the lookback window based on your use case. For example, if you want to use a 30-day lookback window, update it to 60*60*24*30
FROM impressions i 
LEFT JOIN converted b ON b.user_id = i.user_id
),

-- only include impressions and clicks for net new reach -- 
clicks AS
(
SELECT
    reach_user,
    imp as impressions_from_new,
    clicks as clicks_from_new,
    total_product_sales as total_product_sales_from_new
FROM identify_incr_reach iir
LEFT JOIN impressions sat on sat.user_id = iir.reach_user
LEFT JOIN sales_conversion s on s.user_id = iir.reach_user
WHERE 
    users_in_rt_pool = 0 -- net new
)


SELECT
    count(distinct reach_user) as net_new_reach, 
    SUM(impressions_from_new) as net_new_impressions,
    SUM(clicks_from_new) as net_new_clicks,
    SUM(total_product_sales_from_new) as net_new_sales,
    SUM(IF(clicks_from_new > 0,1, 0)) as net_new_users_who_clicked,
    SUM(IF(total_product_sales_from_new > 0,1, 0)) as net_new_users_who_purchased,
    (SELECT sum(impressions) FROM impressions) as total_impressions,
    (SELECT count(distinct user_id) FROM impressions) as total_reach
FROM clicks
```

### 4.3 Query template: Net-new reach for sponsored ads campaigns

Use the following query to calculate the net-new reach for sponsored ads campaigns.

Provide the following custom parameters:

* `campaign_ids`: A list of Amazon DSP campaign ID string values for which you want to calculate net-new reach
* `campaign_start_date`: The earliest start date for all of the campaigns you have selected
* `lookback_days`: The number of days you want the query to look back for net-new users

**Note:** Be sure to set query time window in the Query editor to the number of days (lookback_days) before the campaign start date. So, if the campaign started on 1/1/25 and the lookback window is 182 days (6 months), select 6 months before the campaign start date to ensure that you have queried all of the needed data.


```
-- Instructional Query: Net-new reach by campaign group --
-- AD PRODUCTS: Sponsored ads
-- Enrollment (trial or subscription) to Flexible Shopping Insights (FSI) or Amazon Insights --
/*
 -- REQUIRED UPDATE [Custom parameters]: Add values for the custom parameters.
    -- Note that campaign_start_date is required.
    -- Add one or more campaign IDs. It is highly recommended to include at least one campaign ID.
    -- Optionally, add one or more ASINs filters.
 -- OPTIONAL UPDATE [1 OF 1]: Check that the event_type_description matches those returned in the exploratory query.
*/

-- gather impressions  -- 
WITH impressions AS
(
SELECT
    user_id,
    MIN(event_date_utc) AS impression_dt_first,
    SUM(impressions) AS impressions,
    SUM (clicks) as clicks
FROM sponsored_ads_traffic a
WHERE
    user_id IS NOT NULL
    -- campaigns defined via Custom Parameters  
    AND (
      ARRAY_CONTAINS(CUSTOM_PARAMETER('campaign_ids'), campaign_id_string)
      OR CUSTOM_PARAMETER('campaign_ids') IS NULL
      OR CARDINALITY(CUSTOM_PARAMETER('campaign_ids')) = 0
    )                    
    -- campaign_start_date defined via the Custom Parameters 
    AND CAST(event_date_utc AS DATE) >= CUSTOM_PARAMETER('campaign_start_date')
GROUP BY user_id
),

-- identify users who had detail page view --
converted AS
(
SELECT 
  user_id,
  MAX(event_dt_utc) AS conversion_event_dt_last,
  SUM(conversions) AS dpv
FROM conversions_all
WHERE 
  conversions > 0 
   AND user_id IS NOT NULL
-- OPTIONAL UPDATE [1 OF 1] Check that event_type_description matches the value returned in the exploratory query. 
   AND event_type_description in ('Product detail page viewed')
 AND (
   ARRAY_CONTAINS(CUSTOM_PARAMETER('asins'), 'ALL')
   OR ARRAY_CONTAINS(CUSTOM_PARAMETER('asins'), tracked_item)
   OR CUSTOM_PARAMETER('asins') IS NULL
   OR CARDINALITY(CUSTOM_PARAMETER('asins')) = 0
 )

GROUP BY user_id
),

sales_conversion as (
SELECT
  user_id,
  SUM(total_product_sales) as total_purchases
FROM
  amazon_attributed_events_by_conversion_time
WHERE
  total_product_sales > 0
  AND user_id IS NOT NULL  
  AND (
    ARRAY_CONTAINS(CUSTOM_PARAMETER('campaign_ids'), campaign_id_string)
    OR CUSTOM_PARAMETER('campaign_ids') IS NULL
    OR CARDINALITY(CUSTOM_PARAMETER('campaign_ids')) = 0
    )
GROUP BY
  user_id
),

-- only include impressions that happened before conversion event time -- 
identify_incr_reach AS 
(
SELECT 
    i.user_id as reach_user,
    b.user_id as dpv_user, 
    i.impressions as imp, 
    IF(SECONDS_BETWEEN(conversion_event_dt_last, impression_dt_first) <= (60*60*24*CUSTOM_PARAMETER('lookback_days')) AND b.conversion_event_dt_last < i.impression_dt_first AND i.impressions>0,
        1, 0) as users_in_rt_pool --OPTIONAL UPDATE [1 OF 1]: By default, the lookback window is 6 months (182 days). Update the lookback window based on your use case. For example, use 60*60*24*30 for a 30-day window.
FROM impressions i 
LEFT JOIN converted b ON b.user_id = i.user_id
),


clicks AS
(
SELECT
    reach_user,
    imp as impressions_from_new,
    clicks as clicks_from_new,
    total_purchases as total_purchases_from_new
FROM identify_incr_reach iir
LEFT JOIN impressions sat on sat.user_id = iir.reach_user
LEFT JOIN sales_conversion s on s.user_id = iir.reach_user
WHERE 
    users_in_rt_pool = 0 -- net new
)


SELECT
    count(distinct reach_user) as new_new_reach, 
    SUM(impressions_from_new) as new_new_impressions,
    SUM(clicks_from_new) as new_new_clicks,
    SUM(total_purchases_from_new) as new_new_sales,
    SUM(IF(clicks_from_new > 0,1, 0)) as new_new_users_who_clicked,
    SUM(IF(total_purchases_from_new > 0,1, 0)) as new_new_users_who_purchased,
    (SELECT sum(impressions) FROM impressions) as total_impressions,
    (SELECT count(distinct user_id) FROM impressions) as total_reach
FROM clicks
```
