## Metadata

**id:** a55dd3eaf3d877edb7e3f5ea80fd0fc238f4b816f074b5820cdecd4da21bb04d
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Identify Sponsored Display campaigns by CPC/vCPM and calculate vCPM views and viewability.
**tags:** []
**use_case_categories:** ['How to']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 Purpose 
This instructional query (IQ) will help you to query Sponsored Display campaigns that were purchased on a CPC (cost-per-click) or vCPM (cost per thousand viewable impressions) basis. 

You will learn:
* How to calculate the sum of impressions and clicks, as well as click-through rates
* How to calculate views and viewability rates for vCPM Sponsored Display campaigns
* How to calculate Sponsored Display cost

To JOIN / UNION Sponsored Display traffic to conversion events, view [How to JOIN and Union Sponsored Display traffic with conversions](/marketing-cloud/instructional-queries/f292ef87c4d2628bf56814973ad7657dfd29116b78a0370d3f5ce71c962c8668). 

### 1.2 Requirements 
Advertisers must run Sponsored Display campaigns to use this IQ. 


## 2. Tables Used
- [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic) - This IQ includes impressions, views and clicks for Sponsored Display using the filter `ad_product_type = 'sponsored_display'`.



## 3. How to query Sponsored Display impressions and clicks
### 3.1 Query template
You can use the `sponsored_ads_traffic` table to query Sponsored Display traffic events, such as impressions and clicks. Sponsored Display campaigns have the value `‘sponsored_display’` under the column `ad_product_type`.

This query also returns the `line_item_price_type`, which identifies the cost structure (either CPC or vCPM).


```
-- Instructional Query: Sponsored Display impressions and clicks --
-- AD PRODUCTS: Sponsored ads
SELECT
  campaign,
  campaign_id_string,
  line_item_price_type,
  SUM(impressions) AS impressions,
  SUM(clicks) AS clicks,
  SUM(clicks) / SUM(impressions) AS click_through_rate
FROM
  sponsored_ads_traffic
WHERE
  ad_product_type = 'sponsored_display'
GROUP BY
  1,
  2,
  3
```

### 3.2 Example results

**This data is for instructional purposes only. Your results will differ.**

|campaign|campaign_id_string|line_item_price_type|impressions|clicks|click_through_rate|
|:---|:---|:---|:---|:---|:---|
|Kitchen Smart &#x7c; SD &#x7c; All Products &#x7c; Awareness|111111111111111|VCPM|943,757|3,187|0.34%|
|Kitchen Smart &#x7c; SD &#x7c; Toasters &#x7c; Awareness|22222222222222|VCPM|1,522,365|3,806|0.25%|
|Kitchen Smart &#x7c; SD &#x7c; Blenders &#x7c; Category Views &#x7c; ASIN56789|3333333333333|CPC|1,830,030|2,629|0.14%|
|Kitchen Smart &#x7c; SD &#x7c; Blenders &#x7c; Remarekting &#x7c; ASIN12345|44444444444|CPC|3,812,034|4,295|0.11%|



## 4. How to calculate Sponsored Display views
This section explains how to calculate views and viewability rates for vCPM campaigns that measured views. 

A viewable impression (i.e. a view) is the number of impressions shoppers viewed as per the MRC (Media Rating Council) / Interactive Advertising Bureau (IAB) viewability standard. A display ad is viewable if 50% of pixels were viewable for at least 1 second. 

### 4.1 Query template
The query template for Sponsored Display views and viewability rate is below. View events and impression events are distinct events. If a user views an ad, it has two impression events: one recorded as `impressions` and one recorded as `viewable_impressions`. Only vCPM campaigns will have values for `viewable_impressions`.

The `viewability_rate` is calculated as:

`viewability_rate = SUM(viewable_impressions) / SUM(impressions)`


```
-- Instructional Query: Sponsored Display views --
-- AD PRODUCTS: Sponsored ads
WITH campaigns_with_views_measured AS (
  SELECT
    DISTINCT campaign
  FROM
    sponsored_ads_traffic
  WHERE
    ad_product_type = 'sponsored_display'
    AND line_item_price_type = 'VCPM'
) -- only vCPM campaigns measure views
SELECT
  campaign,
  campaign_id_string,
  SUM(impressions) AS impressions,
  SUM(viewable_impressions) AS viewable_impressions,
  SUM(viewable_impressions) / SUM(impressions) AS viewability_rate
FROM
  sponsored_ads_traffic
WHERE
  campaign IN (
    SELECT
      campaign
    FROM
      campaigns_with_views_measured
  )
GROUP BY
  1, 2
```

### 4.2 Example results
**This data is for instructional purposes only. Your results will differ.**

|campaign|campaign_id_string|impressions|viewable_impressions|viewability_rate|
|:---|:---|:---|:---|:---|
|Kitchen Smart &#x7c; SD &#x7c; All Products &#x7c; Awareness|1111111111111|943,757|615,424|65.21%|
|Kitchen Smart &#x7c; SD &#x7c; Toasters &#x7c; Awareness|2222222222222|1,522,365|627,976|41.25%|


## 5. How to calculate Sponsored Display cost - vCPM and CPC Based

Sponsored Display campaigns have two billing strategies: Pay per click (CPC) and Cost per thousand viewable impressions (vCPM). vCPM billing helps advertisers understand the cost of their awareness efforts when using the “Optimize for viewable impressions” bidding option. 

### 5.1 Query template
The query template for Sponsored Display cost is below. 

* The cost structure (CPC or vCPM) is identified using `line_item_price_type`, which has two values for Sponsored Display campaigns: CPC or VCPM.
* Only vCPM campaigns will have values for `viewable_impressions`. 
* View events and impression events are distinct events. If a user views an ad, it has two events impression events: one recorded as `impressions` and one recorded as `viewable_impressions`. 
* Sponsored Ads spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.



```
-- Instructional Query: How to Calculate Campaign Cost for Sponsored Display campaigns (vCPM and CPC Based) --
-- AD PRODUCTS: Sponsored ads
SELECT
    campaign,
    campaign_id_string,
    line_item_price_type,
    sum(impressions) as imp,
    sum(viewable_impressions) as view_imp,
    SUM(clicks) AS clicks,
    SUM(spend / 100000000) AS spend
FROM sponsored_ads_traffic
WHERE
    ad_product_type = 'sponsored_display'
GROUP BY
    1,2, 3
```

### 5.2 Example results

**This data is for instructional purposes only. Your results will differ.**

|campaign|campaign_id_string|line_item_price_type|imp|view_imp|clicks|spend|
|---|---|---|---|---|---|---|
|Kitchen Smart &#x7c; SB &#x7c; All Products &#x7c; Awareness|11111111111111|CPC|317|0|0|0|
|Kitchen Smart &#x7c; SB &#x7c; Toasters &#x7c; Awareness|22222222222222|VCPM|81058|45675|388|1802.79|
|Kitchen Smart &#x7c; SB &#x7c; Lifestyle&#x7c; |33333333333333|CPC|2789312|0|2517|4414.36|
|Kitchen Smart 2022&#x7c;Prospecting|4444444444444|VCPM|10771|76497|24.51907|
|Kitchen Smart&#x7c;Blenders &#x7c;Remarketing|5555555555555|CPC|2421865|0|5804|5901.26|

