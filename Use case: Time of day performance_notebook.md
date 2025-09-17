## Metadata

**id:** caf95b14e59ff7e933edd8d9146f79566f49e8fccbdcd92ddff55ee4c0dee4a7
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Analyze ad performance by hour and day of the week.
**tags:** []
**use_case_categories:** ['Performance deep dive']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 Purpose
Analyze how your ads perform throughout the day and week. This instructional query lets you view the performance for Amazon DSP and sponsored ads by hour of the day and day of the week. If you are selling a product or a service within a particular region or country, targeting your ads to specific hours of the day and/or to specific days of the week might improve performance. 

### 1.2 Requirements 
To use this query, advertisers must have active campaigns on Amazon DSP and/or sponsored ads during the time period queried. 


## 2. Data query instructions 
### 2.1 Data returned
This query will return campaign performance metrics such as impressions, reach, cost, sales, purchases and roas for different parts of the day, hours of the day and day of the week. 

### 2.2 Tables used 
- **[dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions)** includes all DSP impressions.
- **[sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic)** includes Sponsored Products, Sponsored Display and Sponsored Brands impressions. 
- **[amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic)**: Due to the 14-day attribution window, wait two weeks past the end of the campaign to run this query to capture all conversions.


### 2.3 Query template 
The query is located in the appendix of this page. Scroll down to see the query template.



## 3. Data interpretation instructions 
### 3.1 Example query results 
|**advertiser**|**campaign**|**ad_product_type**|**impression_hour**|**impression_date**|**day_part**|**day_of_week**|**unique_reach**|**impressions**|**ad_spend**|**acos**|**roas**|**users_that_purchased**|**sales_amount**|**sales_amount_brand**|**purchases**|**units_sold**|**purchase_rate**|
|:---|:---|:---|---:|---:|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
|Kitchen Smart|SOV_Q3_2021 Cross Screen Display Package SOV|DSP|17|9/5/2021|Late Afternoon|1|4487|6522|74.97|12.45|8.03|83|602.21|602.21|117|5|0.0261|
|Kitchen Smart|SOV_Q3_2021 Cross Screen Display Package SOV|DSP|20|9/6/2021|Late Afternoon|2|4743|6889|79.18|14.93|6.70|72|530.34|530.34|98|6|0.0207|
|Kitchen Smart|SOV_Q3_2021 Cross Screen Display Package SOV|DSP|18|9/6/2021|Late Afternoon|2|4717|6922|79.56|16.45|6.08|69|483.6|483.6|98|4|0.0208|
|Kitchen Smart|SOV_Q3_2021 Cross Screen Display Package SOV|DSP|15|9/7/2021|Afternoon|3|5065|7345|84.43|16.53|6.05|71|510.59|510.59|100|5|0.0197|
|Kitchen Smart|SOV_Q3_2021 Cross Screen Display Package SOV|DSP|16|9/7/2021|Afternoon|3|4612|6712|77.15|15.24|6.56|68|506.07|506.07|103|4|0.0223|
|Kitchen Smart|SOV_Q3_2021 Cross Screen Display Package SOV|DSP|14|9/8/2021|Afternoon|4|5248|7651|87.94|17.55|5.70|65|501.02|501.02|99|5|0.0189|


### 3.2 Metrics defined 
|**Metric**|**Definition**|
|:---|:---|
|impression_hour|hour of the day when the impression was served (1 - 24)|
|impression_date|date when the impression was served|
|day_part|day part based on when the impression was served. In the query, impression hours before 9 are considered 'Early Morning'; impression hours before 13 are considered morning; impression hours before 17 are considered Afternoon; impression hours before 21 are considered 'Late Afternoon'; impression hours before 24 are considered 'Evening'.|
|day_of_week|day of the week when the impression was served (1/Sunday - 7/Staturday).|
|reach|Number of unique users reached.|
|impressions|Total impressions by hour of the day and day of the week.|
|ad_spend|How much advertisers pay to serve an ad.|
|acos|advertising cost of sales is a metric used to measure Amazon advertising campaigns. The formula is (ad spend / ad revenue) x100.|
|roas|Return on ad spend (ROAS) is the inverse of Amazon ACOS. It is calculated by dividing ad revenue by ad spend.|
|users_that_purchased|Number of unique users who purchased at least one time.|
|sales_amount|The total sales (in local currency) of promoted ASINs purchased by customers on Amazon after delivering an ad.|
|sales_amount_brand|The total sales (in local currency) of promoted ASINs and ASINs from the same brands as promoted ASINs purchased by customers on Amazon after delivering an ad.|
|purchases|The number of times any amount of a promoted product or products are included in a purchase event. Purchase events include video rentals and new Subscribe & Save subscriptions. Sum of purchases from users_that_purchased.|
|purchase_rate|This is calculated by using users_that_purchased in the numerator and unique_reach in the denominator.|


### 3.3 Insights and data interpretation 
Based on the data above, Kitchen Smart’s Display campaign performed better during earlier part of the week with 1.3x higher purchase rate on Monday compared to Thursday. Additionally, Kitchen Smart’s Display campaign saw the highest purchase rate in late afternoon vs. other part of the day. 



## 4. Query templates
Query templates are provided below for:

1. Amazon DSP and sponsored ads
2. Amazon DSP only
3. Sponsored ads only


### 4.1 Query template: Amazon DSP and sponsored ads
This query template will return both Amazon DSP and sponsored ads results. 


```
-- Instructional query: Time of day performance for both DSP and sponsored ads --
WITH impressions AS (
  SELECT
    user_id,
    SUM(impressions) AS impressions,
    -- total_cost is reported in millicents. Divide by 100,000 to get the cost in dollars/your currency.
    SUM(total_cost / 100000) AS ad_spend,
    advertiser,
    impression_hour,
    impression_date,
    impression_dt_utc,
    campaign
  FROM
    dsp_impressions
  GROUP BY
    user_id,
    impression_hour,
    impression_date,
    impression_dt_utc,
    advertiser,
    campaign
  UNION ALL
  SELECT
    user_id,
    SUM(impressions) AS impressions,
    -- spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.
    SUM(spend / 100000000) AS ad_spend,
    advertiser,
    event_hour AS impression_hour,
    event_date AS impression_date,
    event_dt_utc AS impression_dt_utc,
    campaign
  FROM
    sponsored_ads_traffic
  GROUP BY
    user_id,
    impression_hour,
    impression_date,
    event_dt_utc,
    advertiser,
    campaign
) --Gather all sales information based on the impression hour and day
,
conversions AS (
  SELECT
    user_id,
    advertiser,
    SUM(product_sales) AS product_sales,
    SUM(purchases) AS purchases,
    SUM(units_sold) AS units_sold,
    campaign,
    ad_product_type,
    SUM(total_product_sales) AS total_product_sales,
    CASE
      WHEN traffic_event_subtype = 'AD_CLICK' then click_hour
      WHEN traffic_event_subtype = 'AD_IMP' then impression_hour
    end as impression_hour,
    CASE
      WHEN traffic_event_subtype = 'AD_CLICK' then click_date
      WHEN traffic_event_subtype = 'AD_IMP' then impression_date
    end as impression_date
  FROM
    amazon_attributed_events_by_conversion_time
  WHERE
    purchases > 0
    AND user_id IS NOT NULL
  GROUP BY
    user_id,
    impression_hour,
    impression_date,
    traffic_event_subtype,
    click_hour,
    click_date,
    advertiser,
    campaign,
    ad_product_type
) --Combine the information from sales and KPIs from impression and clicks table to create the report
,
assembled_with_imp_conv AS (
  SELECT
    b.advertiser,
    b.campaign,
    (
      CASE
        WHEN b.impression_hour <= 8 THEN 'Early Morning'
        WHEN b.impression_hour <= 12 THEN 'Morning'
        WHEN b.impression_hour <= 16 THEN 'Afternoon'
        WHEN b.impression_hour <= 20 THEN 'Late Afternoon'
        WHEN b.impression_hour <= 23 THEN 'Evening'
        ELSE 'Unknown'
      END
    ) AS day_part,
    EXTRACT(
      DOW
      FROM
        b.impression_date
    ) AS day_of_week,
    b.impression_hour,
    b.impression_date,
    c.ad_product_type,
    SUM(b.impressions) AS impressions,
    SUM(b.ad_spend) AS ad_spend,
    COUNT(DISTINCT b.user_id) AS unique_reach,
    COUNT(DISTINCT c.user_id) AS users_that_purchased,
    SUM(c.product_sales) AS sales_amount,
    SUM(c.purchases) AS purchases,
    SUM(c.units_sold) AS units_sold,
    SUM(c.total_product_sales) AS sales_amount_brand
  FROM
    impressions b
    LEFT JOIN conversions c ON b.advertiser = c.advertiser
    AND b.user_id = c.user_id
    AND b.impression_hour = c.impression_hour
    AND b.impression_date = c.impression_date
    AND b.campaign = c.campaign
  GROUP BY
    b.impression_hour,
    b.impression_date,
    b.campaign,
    b.advertiser,
    day_part,
    day_of_week,
    c.ad_product_type
)
SELECT
  advertiser,
  campaign,
  CASE
    WHEN ad_product_type IS NULL THEN 'DSP'
    ELSE ad_product_type
  END AS ad_product_type,
  impression_hour,
  impression_date,
  day_part,
  day_of_week,
  unique_reach,
  impressions,
  ad_spend,
  (ad_spend / sales_amount) * 100 AS ACOS,
  (sales_amount / ad_spend) AS ROAS,
  users_that_purchased,
  sales_amount,
  sales_amount_brand,
  purchases,
  units_sold,
  (purchases / unique_reach) AS purchase_rate
FROM
  assembled_with_imp_conv
```

### 4.2 Query template: Sponsored ads only


```
-- Instructional query: Time of day performance for sponsored ads only --
WITH impressions AS (
  SELECT
    user_id,
    advertiser,
    event_hour AS impression_hour,
    event_date AS impression_date,
    event_dt_utc AS impression_dt_utc,
    campaign,
    SUM(impressions) AS impressions,
    SUM(clicks) AS clicks,
    -- spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.
    SUM(spend / 100000000) AS ad_spend
  FROM
    sponsored_ads_traffic
  GROUP BY
    user_id,
    impression_hour,
    impression_date,
    impression_dt_utc,
    advertiser,
    campaign
) --Gather all sales information based on the impression hour and day
,
conversions AS (
  SELECT
    user_id,
    advertiser,
    SUM(product_sales) AS product_sales,
    SUM(purchases) AS purchases,
    SUM(units_sold) AS units_sold,
    campaign,
    ad_product_type,
    SUM(total_product_sales) AS total_product_sales,
    CASE
      WHEN traffic_event_subtype = 'AD_CLICK' THEN click_hour
      WHEN traffic_event_subtype = 'AD_IMP' THEN impression_hour
    END AS impression_hour,
    CASE
      WHEN traffic_event_subtype = 'AD_CLICK' THEN click_date
      WHEN traffic_event_subtype = 'AD_IMP' THEN impression_date
    END AS impression_date
  FROM
    amazon_attributed_events_by_conversion_time
  WHERE
    purchases > 0
    AND user_id IS NOT NULL -- only Sponsored Ads has values for ad_product_type.
    AND ad_product_type IS NOT NULL
  GROUP BY
    user_id,
    impression_hour,
    impression_date,
    traffic_event_subtype,
    click_hour,
    click_date,
    advertiser,
    campaign,
    ad_product_type
) --Combine the information from sales and KPIs from impression and clicks table to create the report
,
assembled_with_imp_conv AS (
  SELECT
    b.advertiser,
    b.campaign,
    (
      CASE
        WHEN b.impression_hour <= 8 THEN 'Early Morning'
        WHEN b.impression_hour <= 12 THEN 'Morning'
        WHEN b.impression_hour <= 16 THEN 'Afternoon'
        WHEN b.impression_hour <= 20 THEN 'Late Afternoon'
        WHEN b.impression_hour <= 23 THEN 'Evening'
        ELSE 'Unknown'
      END
    ) AS day_part,
    EXTRACT(
      DOW
      FROM
        b.impression_date
    ) AS day_of_week,
    b.impression_hour,
    b.impression_date,
    c.ad_product_type,
    SUM(b.impressions) AS impressions,
    SUM(b.clicks) AS clicks,
    SUM(b.ad_spend) AS ad_spend,
    COUNT(DISTINCT b.user_id) AS unique_reach,
    COUNT(DISTINCT c.user_id) AS users_that_purchased,
    SUM(c.product_sales) AS sales_amount,
    SUM(c.purchases) AS purchases,
    SUM(c.units_sold) AS units_sold,
    SUM(c.total_product_sales) AS sales_amount_brand
  FROM
    impressions b
    LEFT JOIN conversions c ON b.advertiser = c.advertiser
    AND b.user_id = c.user_id
    AND b.impression_hour = c.impression_hour
    AND b.impression_date = c.impression_date
    AND b.campaign = c.campaign
    where c.ad_product_type is not null
  GROUP BY
    b.impression_hour,
    b.impression_date,
    b.campaign,
    b.advertiser,
    day_part,
    day_of_week,
    c.ad_product_type
)
SELECT
  advertiser,
  campaign,
  ad_product_type,
  impression_hour,
  impression_date,
  day_part,
  day_of_week,
  unique_reach,
  impressions,
  clicks,
  ad_spend,
  (ad_spend / sales_amount) * 100 AS ACOS,
  (sales_amount / ad_spend) AS ROAS,
  users_that_purchased,
  sales_amount,
  sales_amount_brand,
  purchases,
  units_sold,
  (purchases / unique_reach) AS purchase_rate
FROM
  assembled_with_imp_conv
```

### 4.3 Query template: Amazon DSP only


```
-- Instructional query: Time of day performance for Amazon DSP only --
WITH impressions AS (
  SELECT
    user_id,
    SUM(impressions) AS impressions,
    -- total_cost is reported in millicents. Divide by 100,000 to get the cost in dollars/your currency.
    SUM(total_cost / 100000) AS ad_spend,
    advertiser,
    impression_hour,
    impression_date,
    impression_dt_utc,
    campaign
  FROM
    dsp_impressions
  GROUP BY
    user_id,
    impression_hour,
    impression_date,
    impression_dt_utc,
    advertiser,
    campaign
) --Gather all sales information based on the impression hour and day
,
conversions AS (
  SELECT
    user_id,
    advertiser,
    SUM(product_sales) AS product_sales,
    SUM(purchases) AS purchases,
    SUM(units_sold) AS units_sold,
    campaign,
    ad_product_type,
    SUM(total_product_sales) AS total_product_sales,
    impression_hour,
    impression_date,
    traffic_event_dt_utc
  FROM
    amazon_attributed_events_by_conversion_time
  WHERE
    purchases > 0
    AND user_id IS NOT NULL -- only Sponsored Ads has values for ad_product_type and all DSP campaigns have a NULL value.
    AND ad_product_type IS NULL
  GROUP BY
    user_id,
    impression_hour,
    impression_date,
    traffic_event_dt_utc,
    advertiser,
    campaign,
    ad_product_type
) --Combine the information from sales and KPIs from impression and clicks table to create the report
,
assembled_with_imp_conv AS (
  SELECT
    b.advertiser,
    b.campaign,
    (
      CASE
        WHEN b.impression_hour <= 8 THEN 'Early Morning'
        WHEN b.impression_hour <= 12 THEN 'Morning'
        WHEN b.impression_hour <= 16 THEN 'Afternoon'
        WHEN b.impression_hour <= 20 THEN 'Late Afternoon'
        WHEN b.impression_hour <= 23 THEN 'Evening'
        ELSE 'Unknown'
      END
    ) AS day_part,
    EXTRACT(
      DOW
      FROM
        b.impression_date
    ) AS day_of_week,
    b.impression_hour,
    b.impression_date,
    c.ad_product_type,
    SUM(b.impressions) AS impressions,
    SUM(b.ad_spend) AS ad_spend,
    COUNT(DISTINCT b.user_id) AS unique_reach,
    COUNT(DISTINCT c.user_id) AS users_that_purchased,
    SUM(c.product_sales) AS sales_amount,
    SUM(c.purchases) AS purchases,
    SUM(c.units_sold) AS units_sold,
    SUM(c.total_product_sales) AS sales_amount_brand
  FROM
    impressions b
    LEFT JOIN conversions c ON b.advertiser = c.advertiser
    AND b.user_id = c.user_id
    AND b.impression_hour = c.impression_hour
    AND b.impression_date = c.impression_date
    AND b.impression_dt_utc = c.traffic_event_dt_utc
    AND b.campaign = c.campaign
  GROUP BY
    b.impression_hour,
    b.impression_date,
    b.campaign,
    b.advertiser,
    day_part,
    day_of_week,
    c.ad_product_type
)
SELECT
  advertiser,
  campaign,
  CASE
    WHEN ad_product_type IS NULL THEN 'DSP'
    ELSE ad_product_type
  END AS ad_product_type,
  impression_hour,
  impression_date,
  day_part,
  day_of_week,
  unique_reach,
  impressions,
  ad_spend,
  (ad_spend / sales_amount) * 100 AS ACOS,
  (sales_amount/ ad_spend) AS ROAS,
  users_that_purchased,
  sales_amount,
  sales_amount_brand,
  purchases,
  units_sold,
  (purchases / unique_reach) AS purchase_rate
FROM
  assembled_with_imp_conv
```
